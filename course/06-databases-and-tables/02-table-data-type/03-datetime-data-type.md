---
meta:
    title: "Arbeit mit Datum und Zeit in SQL: MySQL und PostgreSQL"
    description: "Funktionen für Datum und Zeit in MySQL und PostgreSQL. Die Datentypen DATE, TIME, DATETIME, TIMESTAMP und ihre Unterschiede."
---

# Arbeit mit Datum und Zeit

**MySQL**

Für Datum und Zeit gibt es in MySQL mehrere Datentypen: `DATE`, `TIME`, `DATETIME` und `TIMESTAMP`.

| Typ         | Beschreibung                                                                                                              | Wertebereich                                    | Größe   |
| :---------- | :------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------- | ------- |
| `DATE`      | Speichert Datumswerte im Format JJJJ-MM-TT. <br /> Zum Beispiel 2022-12-05                                                | von 1000-01-01 bis 9999-12-31                   | 3 Bytes |
| `TIME`      | Speichert Zeitwerte im Format HH:MM:SS (oder HHH:MM:SS bei Werten mit mehr als 24 Stunden). <br /> Zum Beispiel 800:50:50 | von -838:59:59 bis 838:59:59                    | 3 Bytes |
| `DATETIME`  | Speichert Datum und Zeit im Format JJJJ-MM-TT HH:MM:SS. <br /> Zum Beispiel 2022-12-05 10:37:22                           | von 1000-01-01 00:00:00 bis 9999-12-31 23:59:59 | 8 Bytes |
| `TIMESTAMP` | Speichert Datum und Zeit im Format JJJJ-MM-TT HH:MM:SS. <br /> Zum Beispiel 2022-12-05 10:37:22                           | von 1970-01-01 00:00:01 bis 2038-01-19 03:14:07 | 4 Bytes |

**PostgreSQL**

Für Datum und Zeit gibt es in PostgreSQL mehrere Datentypen: `DATE`, `TIME`, `TIMESTAMP`, `TIMESTAMPTZ` und `INTERVAL`.

| Typ           | Beschreibung                                                                      | Wertebereich                         | Größe    |
| :------------ | :-------------------------------------------------------------------------------- | :----------------------------------- | -------- |
| `DATE`        | Speichert Datumswerte im Format JJJJ-MM-TT. <br /> Zum Beispiel 2022-12-05        | von 4713 v. Chr. bis 5874897 n. Chr. | 4 Bytes  |
| `TIME`        | Speichert Zeitwerte im Format HH:MM:SS. <br /> Zum Beispiel 14:30:45              | von 00:00:00 bis 24:00:00            | 8 Bytes  |
| `TIMESTAMP`   | Speichert Datum und Zeit ohne Zeitzone. <br /> Zum Beispiel 2022-12-05 10:37:22   | von 4713 v. Chr. bis 294276 n. Chr.  | 8 Bytes  |
| `TIMESTAMPTZ` | Speichert Datum und Zeit mit Zeitzone. <br /> Zum Beispiel 2022-12-05 10:37:22+03 | von 4713 v. Chr. bis 294276 n. Chr.  | 8 Bytes  |
| `INTERVAL`    | Speichert ein Zeitintervall. <br /> Zum Beispiel 1 year 2 months 3 days 4 hours   | von -178000000 bis 178000000 Jahre   | 16 Bytes |

**MySQL**

## Unterschied zwischen TIMESTAMP und DATETIME

`DATETIME` und `TIMESTAMP` ähneln sich in MySQL stark, weil beide für Datum und Zeit gedacht sind.
Es gibt aber einige wichtige Unterschiede, die entscheiden, welcher Typ wann besser passt.

## DATETIME

Speichert Werte zwischen 1000-01-01 00:00:00 und 9999-12-31 23:59:59 und belegt dabei 8 Bytes. Dieser Datentyp hängt nicht von der in MySQL eingestellten Zeitzone ab.
Er wird immer genau so angezeigt, wie er gesetzt wurde und in der Datenbank gespeichert ist.
Das heißt: Ändert sich die Zeitzone, ändert sich die angezeigte Zeit nicht.

```sql
CREATE TABLE datetime_table (datetime_field DATETIME);
SET @@session.time_zone="+00:00"; -- Zeitzone in MYSQL zurücksetzen
INSERT INTO datetime_table VALUES("2022-06-16 16:37:23");
SET @@session.time_zone="+03:00"; -- Zeitzone in MYSQL ändern
SELECT * FROM datetime_table;
```

| datetime_field      |
| ------------------- |
| 2022-06-16 16:37:23 |

## TIMESTAMP

Speichert die Anzahl der Sekunden seit 1970-01-01 00:00:00 in der Zeitzone UTC und belegt dabei 4 Bytes.
Bei der Abfrage wird der Wert unter Berücksichtigung der aktuellen Zeitzone angezeigt.
Die Zeitzone kannst du in den Einstellungen des Betriebssystems, in den globalen MySQL-Einstellungen oder in einer konkreten Session festlegen.
Beim Anlegen eines Datensatzes mit `TIMESTAMP` wird der Wert in der Datenbank in UTC abgelegt.

```sql
CREATE TABLE timestamp_table (timestamp_field TIMESTAMP);
SET @@session.time_zone="+00:00"; -- Zeitzone in MYSQL zurücksetzen
INSERT INTO timestamp_table VALUES("2022-06-16 16:37:23");
SET @@session.time_zone="+03:00"; -- Zeitzone in MYSQL ändern
SELECT * FROM timestamp_table;
```

| timestamp_field     |
| ------------------- |
| 2022-06-16 19:37:23 |

Vergiss auch nicht: `TIMESTAMP` ist auf den Wertebereich 1970-01-01 00:00:01 bis 2038-01-19 03:14:07 begrenzt, was seinen Einsatz einschränkt.
Für die Speicherung von Geburtsdaten ist dieser Datentyp also nicht geeignet.

**PostgreSQL**

## Unterschied zwischen TIMESTAMP und TIMESTAMPTZ

In PostgreSQL gibt es zwei Haupttypen für Datum und Zeit: `TIMESTAMP` (ohne Zeitzone) und `TIMESTAMPTZ` (mit Zeitzone).

## TIMESTAMP

`TIMESTAMP` (bzw. `TIMESTAMP WITHOUT TIME ZONE`) speichert Datum und Zeit ohne Zeitzoneninformation.
Das heißt: PostgreSQL weiß nicht, in welcher Zeitzone der Wert erfasst wurde, und führt keine Umrechnung durch.

```sql
CREATE TABLE timestamp_table (timestamp_field TIMESTAMP);
SET timezone = 'UTC'; -- Zeitzone auf UTC setzen
INSERT INTO timestamp_table VALUES('2022-06-16 16:37:23');
SET timezone = 'Europe/Moscow'; -- Zeitzone ändern
SELECT * FROM timestamp_table;
```

Ergebnis: `2022-06-16 16:37:23` (der Wert hat sich nicht geändert)

## TIMESTAMPTZ

`TIMESTAMPTZ` (bzw. `TIMESTAMP WITH TIME ZONE`) speichert Datum und Zeit zusammen mit der Zeitzoneninformation.
PostgreSQL rechnet den Wert für die Speicherung automatisch in UTC um und bei der Ausgabe zurück in die lokale Zeitzone.

```sql
CREATE TABLE timestamptz_table (timestamptz_field TIMESTAMPTZ);
SET timezone = 'UTC'; -- Zeitzone auf UTC setzen
INSERT INTO timestamptz_table VALUES('2022-06-16 16:37:23');
SET timezone = 'Europe/Moscow'; -- Zeitzone auf Moscow (+3) ändern
SELECT * FROM timestamptz_table;
```

Ergebnis: `2022-06-16 19:37:23+03` (die Zeit wurde automatisch umgerechnet)

## INTERVAL

Der Typ `INTERVAL` wird für Zeiträume verwendet. Er kann Intervalle in Jahren, Monaten, Tagen, Stunden, Minuten und Sekunden ausdrücken.

```sql
SELECT '1 year 2 months 3 days 4 hours 5 minutes 6 seconds'::INTERVAL;
SELECT '2 weeks'::INTERVAL;
SELECT '90 minutes'::INTERVAL;
```

## Schreibweise von Werten

**MySQL**

Werte für `DATETIME`, `DATE` und `TIMESTAMP` kannst du auf folgende Arten angeben:

- Als String im Format YYYY-MM-DD HH:MM:SS oder YY-MM-DD HH:MM:SS für Datum und Zeit
- Als String im Format YYYY-MM-DD oder YY-MM-DD für nur das Datum

Beim Datum kannst du als Trennzeichen zwischen den Teilen praktisch jedes Satzzeichen verwenden. Auch ganz ohne Trennzeichen – also zusammenhängend – ist erlaubt.

```sql
CREATE TABLE date_table (datetime TIMESTAMP);
INSERT INTO date_table VALUES("2022-06-16 16:37:23");
INSERT INTO date_table VALUES("22.05.31 8+15+04");
INSERT INTO date_table VALUES("2014/02/22 16*37*22");
INSERT INTO date_table VALUES("20220616163723");
INSERT INTO date_table VALUES("2021-02-12");
SELECT * FROM date_table;
```

| datetime            |
| ------------------- |
| 2022-06-16 16:37:23 |
| 2022-05-31 08:15:04 |
| 2014-02-22 16:37:22 |
| 2022-06-16 16:37:23 |
| 2021-02-12 00:00:00 |

**PostgreSQL**

Werte für Datums- und Zeittypen kannst du in verschiedenen Formaten angeben:

- Als String im ISO-8601-Format: 'YYYY-MM-DD HH:MM:SS'
- Als String mit Zeitzonenangabe: 'YYYY-MM-DD HH:MM:SS+TZ'
- Nur Datum: 'YYYY-MM-DD'
- Nur Zeit: 'HH:MM:SS'

```sql
CREATE TABLE date_table (
    date_field DATE,
    time_field TIME,
    timestamp_field TIMESTAMP,
    timestamptz_field TIMESTAMPTZ
);

INSERT INTO date_table VALUES(
    '2022-06-16',
    '16:37:23',
    '2022-06-16 16:37:23',
    '2022-06-16 16:37:23+03'
);

SELECT * FROM date_table;
```

PostgreSQL hält sich strikt an ISO 8601 und bevorzugt die standardisierten Datumsformate.
