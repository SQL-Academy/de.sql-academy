---
meta:
    title: "Common Table Expression, der WITH-Operator"
    description: "Common Table Expressions in SQL. Syntax des WITH-Operators und Beispiele für seinen Einsatz."
---

# Common Table Expression, der WITH-Operator

Eine Common Table Expression (CTE) ist eine temporäre Ergebnismenge, auf die du in nachfolgenden Queries zugreifen kannst.
Für das Schreiben einer CTE wird der Operator `WITH` verwendet.

```sql
-- Beispiel für die Verwendung der WITH-Konstruktion
WITH Aeroflot_trips AS
    (SELECT TRIP.* FROM Company
        INNER JOIN Trip ON Trip.company = Company.id WHERE name = 'Aeroflot')

SELECT plane, COUNT(plane) AS amount FROM Aeroflot_trips GROUP BY plane;
```

Ein Ausdruck mit `WITH` gilt als „temporär“, weil das Ergebnis nirgendwo dauerhaft im
Datenbankschema gespeichert wird, sondern wie eine temporäre View nur für die Dauer der Query existiert,
also nur während der Ausführung der Operatoren `SELECT`, `INSERT`, `UPDATE`, `DELETE` oder `MERGE` verfügbar ist.
Eine CTE ist nur innerhalb der Query gültig, zu der sie gehört. Das verbessert die Struktur der Query, ohne den globalen Namensraum zu belasten.

## Syntax des WITH-Operators

```sql
WITH cte_name [(spalte_1 [, spalte_2 ] …)] AS (subquery)
    [, cte_name [(spalte_1 [, spalte_2 ] …)] AS (subquery)] …
```

So benutzt du den `WITH`-Operator:

1. Schreibe den Operator `WITH`.
2. Gib den Namen der Common Table Expression an.
3. Optional: Definiere durch Kommas getrennte Spaltennamen für die entstehende CTE.
4. Schreibe `AS` und dahinter die Subquery, deren Ergebnis du an anderer Stelle der SQL-Query über den in Schritt 2 vergebenen Namen verwenden kannst.
5. Optional: Wenn du mehr als eine CTE brauchst, setze ein Komma und wiederhole die Schritte 2–4.

## Beispiel-Queries

ER-Diagramm der Datenbank Airo: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-with).

1. Wir bauen die CTE `Aeroflot_trips`, die alle Flüge der Airline „Aeroflot“ enthält.

```sql
WITH Aeroflot_trips AS
    (SELECT plane, town_from, town_to FROM Company
        INNER JOIN Trip ON Trip.company = Company.id WHERE name = 'Aeroflot')

SELECT * FROM Aeroflot_trips;
```

| plane | town_from | town_to |
| ----- | --------- | ------- |
| IL-86 | Moscow    | Rostov  |
| IL-86 | Rostov    | Moscow  |

2. Genauso bauen wir die CTE `Aeroflot_trips`, aber mit umbenannten Spalten.

```sql
WITH Aeroflot_trips (aeroflot_plane, town_from, town_to) AS
    (SELECT plane, town_from, town_to FROM Company
        INNER JOIN Trip ON Trip.company = Company.id WHERE name = 'Aeroflot')

SELECT * FROM Aeroflot_trips;
```

| aeroflot_plane | town_from | town_to |
| -------------- | --------- | ------- |
| IL-86          | Moscow    | Rostov  |
| IL-86          | Rostov    | Moscow  |

3. Mit dem `WITH`-Operator definieren wir mehrere CTEs.

```sql
WITH Aeroflot_trips AS
    (SELECT TRIP.* FROM Company
        INNER JOIN Trip ON Trip.company = Company.id WHERE name = 'Aeroflot'),
    Don_avia_trips AS
    (SELECT TRIP.* FROM Company
        INNER JOIN Trip ON Trip.company = Company.id WHERE name = 'Don_avia')

SELECT * FROM Don_avia_trips UNION SELECT * FROM  Aeroflot_trips;
```

| id   | company | plane  | town_from | town_to | time_out                 | time_in                  |
| ---- | ------- | ------ | --------- | ------- | ------------------------ | ------------------------ |
| 1181 | 1       | TU-134 | Rostov    | Moscow  | 1900-01-01T06:12:00.000Z | 1900-01-01T08:01:00.000Z |
| 1182 | 1       | TU-134 | Moscow    | Rostov  | 1900-01-01T12:35:00.000Z | 1900-01-01T14:30:00.000Z |
| 1187 | 1       | TU-134 | Rostov    | Moscow  | 1900-01-01T15:42:00.000Z | 1900-01-01T17:39:00.000Z |
| 1188 | 1       | TU-134 | Moscow    | Rostov  | 1900-01-01T22:50:00.000Z | 1900-01-02T00:48:00.000Z |
| 1195 | 1       | TU-154 | Rostov    | Moscow  | 1900-01-01T23:30:00.000Z | 1900-01-02T01:11:00.000Z |
| 1196 | 1       | TU-154 | Moscow    | Rostov  | 1900-01-01T04:00:00.000Z | 1900-01-01T05:45:00.000Z |
| 1145 | 2       | IL-86  | Moscow    | Rostov  | 1900-01-01T09:35:00.000Z | 1900-01-01T11:23:00.000Z |
| 1146 | 2       | IL-86  | Rostov    | Moscow  | 1900-01-01T17:55:00.000Z | 1900-01-01T20:01:00.000Z |

## Rekursion in CTEs

CTEs können auch für rekursive Queries eingesetzt werden,
die Daten iterativ verarbeiten — zum Beispiel
für hierarchische Datenstrukturen wie „Vorgesetzter — Untergebener“.

### Syntax einer rekursiven CTE

Eine rekursive CTE besteht aus zwei Teilen, die durch `UNION ALL` getrennt werden:

- Der Startdatensatz ohne rekursive Verweise.
- Der rekursive Teil: eine Query, die sich auf die CTE selbst bezieht, um die Rekursion fortzusetzen.

```sql
WITH RECURSIVE cte_name (spalte_1, spalte_2, ...) AS (
    -- Startdatensatz
    SELECT spalte_1, spalte_2, ...
    FROM tabelle
    WHERE bedingung

    UNION ALL

    -- Rekursiver Teil
    SELECT spalte_1, spalte_2, ...
    FROM cte_name
    INNER JOIN tabelle ON cte_name.spalte = tabelle.spalte
    WHERE bedingung
)

SELECT * FROM cte_name;
```

### Beispiel: Hierarchie von Vorgesetzten und Untergebenen

Schauen wir uns die Tabelle `Employees` an, die IDs von Mitarbeitenden und ihren Vorgesetzten enthält:

| id  | name            | managerId |
| --- | --------------- | --------- |
| 1   | John Smith      | null      |
| 2   | Michael Johnson | 1         |
| 3   | Robert Williams | 1         |
| 4   | James Brown     | 2         |
| 5   | David Jones     | 2         |
| 6   | Richard Davis   | 3         |

Gesucht sind alle Untergebenen von `John Smith` (`id=1`) auf allen Hierarchieebenen.

```sql
WITH RECURSIVE Subordinates AS (
    -- Startdatensatz
    SELECT id, name, managerId
    FROM Employees
    WHERE managerId = 1

    UNION ALL

    -- Rekursiver Teil: Untergebene der Untergebenen
    SELECT e.id, e.name, e.managerId
    FROM Employees e
    INNER JOIN Subordinates s ON e.managerId = s.id
)

SELECT * FROM Subordinates;
```

| id  | name            | managerId |
| --- | --------------- | --------- |
| 2   | Michael Johnson | 1         |
| 3   | Robert Williams | 1         |
| 4   | James Brown     | 2         |
| 5   | David Jones     | 2         |
| 6   | Richard Davis   | 3         |

### Ablauf einer rekursiven CTE

1. **Startdatensatz:** Alle Mitarbeitenden mit `managerId=1` werden ausgewählt (direkte Untergebene von `John Smith`).
2. **Rekursiver Teil:** Für jeden im Startdatensatz ausgewählten Mitarbeiter werden dessen Untergebene geholt (wo `managerId` gleich der `id` des ausgewählten Mitarbeiters ist).
3. **Vereinigung:** Die Ergebnisse aus Startdatensatz und rekursivem Teil werden mit `UNION ALL` zusammengeführt.
4. **Rekursion:** Der Vorgang wiederholt sich für jede neue Gruppe von Untergebenen, bis alle Hierarchieebenen abgedeckt sind.

## Fazit

CTEs wurden in SQL eingeführt, um komplexe, lange Queries — insbesondere mit vielen Subqueries — zu vereinfachen. Ihr Hauptzweck ist, Queries lesbarer, einfacher zu schreiben und besser zu warten zu machen.
Das wird erreicht, indem große und komplexe Queries in benannten Ausdrücken versteckt werden, die dann in der Hauptquery verwendet werden.
