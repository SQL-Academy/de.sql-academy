---
meta:
    title: "Scheduler in SQL: MySQL EVENT und PostgreSQL pg_cron"
    description: "Vollständiger Leitfaden zum Anlegen von Events in MySQL und pg_cron-Aufgaben in PostgreSQL. Lerne, den EVENT Scheduler und pg_cron zu nutzen, um Daten zu bereinigen, Statistiken zu aktualisieren und Reports zeitgesteuert zu erzeugen. Codebeispiele und Praxistipps inklusive."
---

# Event Scheduler

In echten Anwendungen taucht oft die Anforderung auf, bestimmte Aktionen automatisch und nach Zeitplan auszuführen: alte Einträge bereinigen, Statistiken aktualisieren, Reports erzeugen.

**MySQL**

Für solche Aufgaben bietet MySQL den Mechanismus der **geplanten Events**.

> **Ein Event** ist eine Aufgabe, die die Datenbank selbst nach Zeitplan startet. Du konfigurierst sie einmal — sie läuft danach automatisch.

Events in MySQL funktionieren ähnlich wie der Task Scheduler eines Betriebssystems: Du legst die Aufgabe einmal an, und die Datenbank führt sie automatisch nach Zeitplan aus.

**PostgreSQL**

Für solche Aufgaben wird in PostgreSQL die Extension **pg_cron** verwendet. Damit kannst du geplante Aufgaben mit der cron-Syntax anlegen (wie unter Unix).

## Wann brauchst du das?

**MySQL**

Geplante Events helfen bei der Automatisierung von Aufgaben wie:

**PostgreSQL**

Mit pg_cron kannst du Folgendes automatisieren:

- **Datenbereinigung**: Löschen veralteter Log-Einträge oder temporärer Daten
- **Statistik-Update**: Neuberechnung aggregierter Daten für Analytics
- **Report-Erstellung**: Automatisches Erzeugen periodischer Reports
- **Datenpflege**: Verschieben von Einträgen in Archivtabellen und andere regelmäßige Wartungsarbeiten

## Scheduler aktivieren

**MySQL**

Bevor du Events anlegen kannst, solltest du sicherstellen, dass der Event Scheduler eingeschaltet ist:

```sql
SHOW VARIABLES LIKE 'event_scheduler';
```

Falls er ausgeschaltet ist, schalte ihn ein:

```sql
SET GLOBAL event_scheduler = ON;
```

Dieser Befehl ändert die Einstellung bis zum nächsten Server-Neustart und erfordert die Berechtigung zum Ändern globaler Systemvariablen. Um den Scheduler dauerhaft zu aktivieren, nutze die Serverkonfiguration oder `SET PERSIST`, sofern der Befehl in deiner MySQL-Version verfügbar ist.

**PostgreSQL**

Zuerst musst du pg_cron auf dem Server installieren, zu `shared_preload_libraries` hinzufügen und PostgreSQL neu starten. Anschließend kannst du die Extension in der Datenbank anlegen:

```sql
CREATE EXTENSION IF NOT EXISTS pg_cron;
```

> **Wichtig:** Installation und Anlegen der Extension übernimmt normalerweise die Administration. Regulären Benutzern kann das Anlegen von Aufgaben mit `GRANT USAGE ON SCHEMA cron TO user_name;` erlaubt werden. Wie pg_cron bei einem Cloud-Anbieter aktiviert wird, hängt vom jeweiligen Dienst ab.

## Einmalige Ausführung

**MySQL**

Beginnen wir mit dem Einfachsten — einem Event, das einmalig zu einem bestimmten Zeitpunkt läuft:

**PostgreSQL**

Beginnen wir mit dem Einfachsten — einer Aufgabe, die einmalig zu einem bestimmten Zeitpunkt läuft:

**MySQL**

```sql
CREATE EVENT cleanup_old_logs
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 DAY
DO
    DELETE FROM logs WHERE created_at < NOW() - INTERVAL 30 DAY;
```

Dieses Event löscht 24 Stunden nach seiner Erstellung Log-Einträge, die älter als 30 Tage sind.

**Die Syntax im Detail:**

- `CREATE EVENT cleanup_old_logs` — wir legen ein Event mit dem Namen `cleanup_old_logs` an
- `ON SCHEDULE AT` — wir geben an, wann das Event ausgeführt werden soll
- `CURRENT_TIMESTAMP + INTERVAL 1 DAY` — Ausführungszeitpunkt (in 1 Tag)
- `DO` — der Event-Body: eine einfache SQL-Anweisung oder eine zusammengesetzte `BEGIN ... END`-Anweisung

**PostgreSQL**

pg_cron hat keinen eigenen Zeitplantyp für eine einmalige Ausführung. Verwende dafür einen externen Scheduler der Anwendung oder lege eine temporäre Aufgabe an und entferne sie nach der Ausführung.

Diese Aufgabe löscht zum Beispiel beim nächsten Erreichen von 3:00 Uhr alte Log-Einträge und anschließend sich selbst:

```sql
SELECT cron.schedule(
    'cleanup_old_logs_once',
    '0 3 * * *',
    $command$
    DO $$
    BEGIN
        DELETE FROM logs WHERE created_at < NOW() - INTERVAL '30 days';
        PERFORM cron.unschedule('cleanup_old_logs_once');
    END;
    $$;
    $command$
);
```

Normalerweise führt der Zeitplan `'0 3 * * *'` eine Aufgabe jeden Tag um 3:00 Uhr aus. Nach dem ersten Lauf ruft diese Aufgabe jedoch `cron.unschedule()` mit ihrem eigenen Namen auf und wird daher nicht erneut ausgeführt.

## Wiederkehrende Ausführung

**MySQL**

Häufiger sollen Events regelmäßig laufen — jeden Tag, jede Stunde oder jede Minute:

**PostgreSQL**

Häufiger sollen Aufgaben regelmäßig laufen — jeden Tag, jede Stunde oder jede Minute:

**MySQL**

```sql
CREATE EVENT update_statistics
ON SCHEDULE EVERY 1 HOUR
DO
BEGIN
    UPDATE product_stats SET
        total_sales = (SELECT SUM(amount) FROM orders WHERE product_id = product_stats.product_id),
        last_updated = NOW();
END;
```

Dieses Event aktualisiert die Verkaufsstatistik jede Stunde.

**Die Syntax im Detail:**

- `ON SCHEDULE EVERY 1 HOUR` — stündlich ausführen
- `BEGIN ... END` — eine zusammengesetzte Anweisung, der du bei Bedarf mehrere SQL-Anweisungen hinzufügen kannst

**Mögliche Intervalle:**

- `EVERY 1 MINUTE` — jede Minute
- `EVERY 1 HOUR` — jede Stunde
- `EVERY 1 DAY` — jeden Tag
- `EVERY 1 WEEK` — jede Woche
- `EVERY 1 MONTH` — jeden Monat
- `EVERY 30 SECOND` — alle 30 Sekunden

**PostgreSQL**

```sql
SELECT cron.schedule(
    'cleanup_old_logs',
    '0 3 * * *',
    'DELETE FROM logs WHERE created_at < NOW() - INTERVAL ''30 days'''
);
```

Diese Aufgabe läuft jeden Tag um 3:00 Uhr und löscht Log-Einträge, die älter als 30 Tage sind.

**Die Syntax im Detail:**

- `cron.schedule()` — Funktion zum Anlegen einer geplanten Aufgabe
- `'cleanup_old_logs'` — Name der Aufgabe
- `'0 3 * * *'` — Zeitplan im cron-Format (Minute, Stunde, Tag des Monats, Monat, Wochentag)
- letzter Parameter — der auszuführende SQL-Befehl

**cron-Zeitplan-Format:**

![cron-Zeitplan-Format](https://sql-academy.org/static/guidePage/scheduled-events/cron_schedule_de.png "cron-Zeitplan-Format")

```sql
SELECT cron.schedule(
    'update_statistics_hourly',
    '0 * * * *',
    $$
    UPDATE product_stats SET
        total_sales = (SELECT SUM(amount) FROM orders WHERE product_id = product_stats.product_id),
        last_updated = NOW()
    $$
);
```

Diese Aufgabe aktualisiert die Verkaufsstatistik jede Stunde (zur vollen Stunde).

**Beispiele für Zeitpläne:**

- `'*/5 * * * *'` — alle 5 Minuten
- `'0 * * * *'` — jede Stunde (zur vollen Stunde)
- `'0 0 * * *'` — jeden Tag um Mitternacht
- `'0 0 * * 0'` — jeden Sonntag um Mitternacht
- `'0 9 1 * *'` — am 1. jedes Monats um 9:00 Uhr
- `'30 seconds'` — alle 30 Sekunden ab pg_cron 1.5

Sekundenintervalle werden als eigene Zeichenfolge und nicht als sechstes cron-Feld angegeben. pg_cron unterstützt Werte von 1 bis 59 Sekunden.

## Ausführungszeitraum begrenzen

**MySQL**

Manchmal soll ein Event nur in einem bestimmten Zeitraum aktiv sein.

**PostgreSQL**

Manchmal soll eine Aufgabe nur in einem bestimmten Zeitraum aktiv sein.

**MySQL**

```sql
CREATE EVENT temporary_log_cleanup
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP
ENDS CURRENT_TIMESTAMP + INTERVAL 30 DAY
DO
    DELETE FROM logs WHERE created_at < NOW() - INTERVAL 30 DAY;
```

Dieses Event löscht 30 Tage lang einmal täglich veraltete Logs.

**Neue Elemente:**

- `STARTS` — Beginn des Gültigkeitszeitraums
- `ENDS` — Ende des Gültigkeitszeitraums

Nach `ENDS` wird das Event nicht mehr ausgeführt und standardmäßig gelöscht. Füge beim Anlegen `ON COMPLETION PRESERVE` hinzu, wenn seine Definition erhalten bleiben soll.

**PostgreSQL**

In einem pg_cron-Zeitplan kannst du kein Datum angeben, an dem eine Aufgabe automatisch beendet wird. Eine Aufgabe, die in den nächsten 30 Tagen täglich Logs bereinigt, legst du daher mit einem normalen Zeitplan an:

```sql
SELECT cron.schedule(
    'temporary_log_cleanup',
    '0 0 * * *',
    $$DELETE FROM logs WHERE created_at < NOW() - INTERVAL '30 days'$$
);
```

Damit die Aufgabe automatisch endet, verwendest du denselben Ansatz wie im Beispiel zur einmaligen Ausführung: Lege die Aufgabe `stop_temporary_log_cleanup` für ein Datum in 30 Tagen an. Ihr Befehlsblock sieht so aus:

```sql
DO $$
BEGIN
    PERFORM cron.unschedule('temporary_log_cleanup');
    PERFORM cron.unschedule('stop_temporary_log_cleanup');
END;
$$;
```

Der erste Aufruf entfernt die Bereinigungsaufgabe, der zweite die Hilfsaufgabe. Gib im Zeitplan der Hilfsaufgabe Minute, Stunde, Tag und Monat des Datums in 30 Tagen an.

## Zeitplan anzeigen

**MySQL**

Alle angelegten Events anzeigen:

```sql
SHOW EVENTS;
```

Events einer bestimmten Datenbank ansehen:

```sql
SHOW EVENTS FROM your_database_name;
```

**PostgreSQL**

Die für den aktuellen Benutzer verfügbaren geplanten Aufgaben anzeigen:

```sql
SELECT * FROM cron.job;
```

Ein regulärer Benutzer sieht nur die eigenen Aufgaben. Ein Superuser oder eine Rolle mit dem Attribut `BYPASSRLS` kann auch die Aufgaben anderer Benutzer sehen.

Ausführungshistorie der Aufgaben ansehen:

```sql
SELECT * FROM cron.job_run_details
ORDER BY start_time DESC
LIMIT 10;
```

## Zeitplan verwalten

**MySQL**

**Event vorübergehend deaktivieren:**

```sql
ALTER EVENT cleanup_old_logs DISABLE;
```

**Event aktivieren:**

```sql
ALTER EVENT cleanup_old_logs ENABLE;
```

**Zeitplan eines Events ändern:**

```sql
ALTER EVENT cleanup_old_logs
ON SCHEDULE EVERY 2 HOUR;
```

**Event löschen:**

```sql
DROP EVENT IF EXISTS cleanup_old_logs;
```

**PostgreSQL**

**Geplante Aufgabe löschen:**

```sql
SELECT cron.unschedule('cleanup_old_logs');
```

Oder über die Job-ID:

```sql
SELECT cron.unschedule(42);  -- wobei 42 die jobid aus der Tabelle cron.job ist
```

**Aufgabe ändern:**

Zeitplan, Befehl und Aktivitätsstatus einer bestehenden Aufgabe lassen sich mit `cron.alter_job()` ändern:

```sql
SELECT cron.alter_job(
    42,
    schedule := '0 */2 * * *'
);
```

Dabei ist `42` die `jobid` der Aufgabe aus der Tabelle `cron.job`.

**MySQL**

## Wichtige Punkte beim Arbeiten mit Events

**PostgreSQL**

## Wichtige Punkte beim Arbeiten mit pg_cron-Aufgaben

**MySQL**

1. **Berechtigungen**: Zum Erstellen von Events brauchst du das Privileg `EVENT`.

2. **Zeitzone**: MySQL interpretiert den Zeitplan mit dem `time_zone`-Wert der aktuellen Sitzung, wenn das Event angelegt oder geändert wird, und speichert diese Zeitzone mit dem Event.

3. **Überlappende Ausführungen**: Dauert ein Event länger als sein Intervall, kann MySQL mehrere Instanzen gleichzeitig starten. Verwende eine Sperre oder einen anderen Schutz, wenn sich Ausführungen nicht überlappen dürfen.

4. **Performance**: Eine passende Häufigkeit hängt von den Kosten der Operation und der Datenbanklast ab, nicht von einem allgemeinen Mindestintervall.

**PostgreSQL**

1. **Berechtigungen**: Normalerweise installiert ein Superuser die Extension. Danach kann regulären Benutzern `USAGE` für das Schema `cron` erteilt werden. Eine Aufgabe läuft mit den Rechten des Benutzers, der sie angelegt hat.

2. **Zeitzone**: cron-Ausdrücke verwenden die Einstellung `cron.timezone`, standardmäßig `GMT`. Den aktuellen Wert prüfst du mit `SHOW cron.timezone;`.

3. **Intervalle**: Ab Version 1.5 unterstützt pg_cron Intervalle von 1 bis 59 Sekunden. Wähle die Häufigkeit passend zu den Kosten der Aufgabe und der erwarteten Last.

4. **Überlappende Ausführungen**: pg_cron führt nicht mehrere Instanzen derselben Aufgabe gleichzeitig aus. Wird die nächste Ausführung fällig, während die Aufgabe noch läuft, kommt sie in die Warteschlange.

5. **Logging**: Wenn `cron.log_run` aktiviert ist, werden Ausführungsdetails in `cron.job_run_details` gespeichert. Das Logging ist standardmäßig aktiviert.

## Selbsttest

**MySQL**

Welches Mindestintervall kannst du für wiederkehrende Events verwenden?

1. **Richtige Antwort:** Events können jede Sekunde laufen — MySQL erlaubt \`EVERY 1 SECOND\`. In der Praxis wählst du die Häufigkeit passend zur Ausführungsdauer und zur entstehenden Datenbanklast.

2. Das kleinste Intervall ist 1 Minute — Ein Intervall von einer Minute ist in der Praxis üblich, MySQL erlaubt technisch aber auch eine Ausführung pro Sekunde.

3. Das kleinste Intervall ist 1 Stunde — Eine stündliche Ausführung kann zu einer bestimmten Aufgabe passen, ist aber eine Empfehlung für diesen Anwendungsfall und keine Einschränkung des Schedulers.

**PostgreSQL**

Welches Mindestintervall kannst du für wiederkehrende Aufgaben verwenden?

1. **Richtige Antwort:** Aufgaben können jede Sekunde laufen — Ab Version 1.5 akzeptiert pg_cron Intervalle wie \`1 second\`. In der Praxis wählst du die Häufigkeit passend zur Laufzeit der Aufgabe und zur Datenbanklast.

2. Das kleinste Intervall ist 1 Minute — Ein normaler cron-Ausdruck mit fünf Feldern hat Minutengenauigkeit, ab Version 1.5 unterstützt pg_cron zusätzlich Sekundenintervalle.

3. Das kleinste Intervall ist 1 Stunde — Eine stündliche Ausführung kann zu einer bestimmten Aufgabe passen, ist aber eine Empfehlung für diesen Anwendungsfall und keine Einschränkung von pg_cron.

**MySQL**

Geplante Events sind ein mächtiges Werkzeug, um Routineaufgaben in der Datenbank zu automatisieren. Sie sorgen für saubere Daten, aktuelle Statistiken und regelmäßige Wartungsoperationen — ganz ohne Eingreifen von Entwickelnden! 🚀

**PostgreSQL**

Geplante Aufgaben sind ein mächtiges Werkzeug, um Routineaufgaben in der Datenbank zu automatisieren. Sie sorgen für saubere Daten, aktuelle Statistiken und regelmäßige Wartungsoperationen — ganz ohne Eingreifen von Entwickelnden! 🚀
