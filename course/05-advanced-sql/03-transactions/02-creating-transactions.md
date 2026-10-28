---
meta:
    title: "Transaktionen anlegen: MySQL und PostgreSQL"
    description: "Lerne, wie du sichere Transaktionen in MySQL und PostgreSQL anlegst, um dein Geld und deine Daten zu schützen. Erfahre, warum COMMIT und ROLLBACK so wichtig sind, um Änderungen zu steuern und Datenstabilität zu sichern. Entdecke Savepoints für die feingranulare Kontrolle über Transaktionen – das senkt Risiken und macht die Datenverarbeitung effizienter."
---

# Transaktionen anlegen

Stell dir vor, du willst 1000 Euro von deinem Sparkonto auf dein Girokonto überweisen und stellst plötzlich fest,
dass das Geld zwar abgebucht, aber nicht auf dem Girokonto angekommen ist. Das wäre ziemlich ärgerlich. 😿

Um solche Fehler zu vermeiden, startet das Programm, das deine Überweisung verarbeitet, zuerst eine Transaktion,
führt dann die SQL-Queries für die Überweisung aus und beendet die Transaktion bei Erfolg mit `COMMIT`, was die Änderungen festschreibt.

Wenn jedoch etwas schiefgeht, wird `ROLLBACK` ausgeführt –
das weist den Server an, alle Aktionen seit dem Start der Transaktion zurückzunehmen.

Der Ablauf kann zum Beispiel so aussehen:

```sql
-- Transaktion starten
START TRANSACTION;

-- Geld vom Konto des Absenders abbuchen
UPDATE accounts SET user_balance = user_balance - 1000 WHERE user_id = 1;

-- Geld dem Konto des Empfängers gutschreiben
UPDATE accounts SET user_balance = user_balance + 1000 WHERE user_id = 2;

-- Änderungen festschreiben
COMMIT;
```

Wenn eine der Queries fehlschlägt, wird die Transaktion zurückgerollt, anstatt `COMMIT` auszuführen:

```sql
ROLLBACK;
```

Per Transaktion sorgt das Programm dafür, dass deine 1000 Euro entweder auf dem Ausgangskonto bleiben
oder vollständig auf das andere Konto überwiesen werden – verloren gehen sie auf keinen Fall.

## Transaktionen starten und beenden

**MySQL**

Jede explizite Transaktion in MySQL beginnt mit `START TRANSACTION` oder `BEGIN`.

**PostgreSQL**

Jede explizite Transaktion in PostgreSQL beginnt mit `BEGIN` oder `START TRANSACTION`.

Beenden lässt sich die Transaktion auf zwei Arten:

- mit `COMMIT`, das den Server anweist, die Änderungen dauerhaft zu speichern und alle während der Transaktion belegten Ressourcen (etwa Row Locks) freizugeben
- mit `ROLLBACK`, das den Server auffordert, die Daten in den Zustand vor Beginn der Transaktion zurückzuversetzen.
  Nach dem Rollback werden ebenfalls alle von der Transaktion genutzten Ressourcen freigegeben.

Neben `COMMIT` und `ROLLBACK` kann eine Transaktion auch durch äußere Faktoren beendet werden.
Wird der Server zum Beispiel heruntergefahren, wird deine Transaktion beim Neustart automatisch zurückgerollt.

## Savepoints in Transaktionen

In manchen Fällen willst du innerhalb einer Transaktion einen Rollback machen, ohne dabei die gesamte bisherige Arbeit zu verlieren.
Dafür kannst du innerhalb einer Transaktion einen oder mehrere Savepoints setzen.
So kannst du gezielt auf einen bestimmten Punkt der Transaktion zurückspringen – nicht nur auf ihren Anfang.

Jedem Savepoint innerhalb einer Transaktion musst du einen eindeutigen Namen geben,
damit du mehrere Savepoints parallel nutzen kannst.
Einen Savepoint namens `my_savepoint` legst du so an:

```sql
SAVEPOINT my_savepoint;
```

Um auf einen bestimmten Savepoint zurückzuspringen, gibst du einfach
`ROLLBACK` ein, gefolgt von `TO SAVEPOINT` und dem Savepoint-Namen, zum Beispiel:

**MySQL**

```sql
START TRANSACTION;

-- Savepoint vor der Guthabenänderung des ersten Nutzers anlegen
SAVEPOINT before_updating_user_1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 1;

-- Bedingung für den ersten Nutzer prüfen
-- z. B. Business-Regeln verifizieren

-- Hier nehmen wir an, dass die Bedingung nicht erfüllt ist und die Guthabenänderung zurückgenommen werden muss
ROLLBACK TO SAVEPOINT before_updating_user_1;

-- Guthaben des zweiten Nutzers aktualisieren
UPDATE accounts SET balance = balance + 200 WHERE user_id = 2;

-- Transaktion abschließen
COMMIT;
```

**PostgreSQL**

```sql
BEGIN;

-- Savepoint vor der Guthabenänderung des ersten Nutzers anlegen
SAVEPOINT before_updating_user_1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 1;

-- Bedingung für den ersten Nutzer prüfen
-- z. B. Business-Regeln verifizieren

-- Hier nehmen wir an, dass die Bedingung nicht erfüllt ist und die Guthabenänderung zurückgenommen werden muss
ROLLBACK TO SAVEPOINT before_updating_user_1;

-- Guthaben des zweiten Nutzers aktualisieren
UPDATE accounts SET balance = balance + 200 WHERE user_id = 2;

-- Transaktion abschließen
COMMIT;
```

Im Ergebnis dieser Transaktion bleibt das Guthaben des ersten Nutzers wegen des Rollbacks zum Savepoint unverändert,
und das Guthaben des zweiten Nutzers steigt um 200.
Das zeigt, wie sich Änderungen in der Datenbank mit Transaktionen und Savepoints sehr feingranular steuern lassen.

Wenn du Savepoints nutzt, denk an Folgendes:

- Der Name ist irreführend: Beim Anlegen eines Savepoints wird nichts gespeichert.
  Damit deine Änderungen innerhalb der Transaktion dauerhaft werden, brauchst du `COMMIT`.
- Wenn du einen Rollback ohne Angabe eines konkreten Savepoints ausführst,
  werden alle zuvor gesetzten Savepoints ignoriert, und die gesamte Transaktion wird zurückgerollt.

## Prüf dich selbst

Vor dem Start der Transaktion haben beide Nutzer ein Guthaben von 500. Wie hoch sind die Guthaben des ersten und des zweiten Nutzers, nachdem diese Queries ausgeführt wurden?

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
SAVEPOINT after_debit;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;
ROLLBACK TO SAVEPOINT after_debit;

COMMIT;
```

1. 500 und 500 — Ein Rollback zum Savepoint nimmt nur die Änderungen zurück, die danach gemacht wurden. Die Abbuchung lief vor dem Savepoint, deshalb schreibt COMMIT sie fest.

2. **Richtige Antwort:** 400 und 500 — Die Abbuchung bleibt, die Gutschrift nach dem Savepoint ist zurückgenommen. Das Geld ist einfach verschwunden – setz einen Savepoint deshalb so, dass ein Rollback zu ihm zusammengehörige Änderungen nicht auseinanderreißt.

3. 400 und 600 — Die Gutschrift lief nach dem Savepoint after_debit, deshalb hat ROLLBACK TO SAVEPOINT sie zurückgenommen.

Dieselben Queries, aber am Ende läuft statt `ROLLBACK TO SAVEPOINT` und `COMMIT` nur `ROLLBACK`. Was bleibt in der Datenbank erhalten?

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
SAVEPOINT after_debit;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

ROLLBACK;
```

1. Die Abbuchung beim ersten Nutzer — Ein Savepoint speichert nichts, er markiert nur eine Stelle für einen teilweisen Rollback. ROLLBACK ohne Savepoint-Namen nimmt die gesamte Transaktion zurück.

2. **Richtige Antwort:** Nichts: Beide Guthaben bleiben bei 500 — ROLLBACK ohne Angabe eines Savepoints ignoriert alle Savepoints und bringt die Daten auf den Stand vor dem Start der Transaktion zurück.

3. Beide Änderungen — Die Änderungen einer Transaktion werden erst mit COMMIT dauerhaft. ROLLBACK macht das Gegenteil und nimmt sie zurück.
