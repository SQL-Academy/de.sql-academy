---
meta:
    title: "Aggregatfunktionen"
    description: "Aggregatfunktionen in SQL-Queries, Syntax und Beispiele"
---

# Aggregatfunktionen

Im Artikel über Gruppierungen haben wir erwähnt, dass wir bei `GROUP BY` Aggregatfunktionen verwenden können.
Sprechen wir jetzt ausführlicher darüber 🐳.

> Eine Aggregatfunktion ist eine Funktion, die eine Berechnung über eine Wertemenge ausführt und einen einzelnen Wert zurückgibt.

## Allgemeine Struktur einer Query mit Aggregatfunktion

```sql
SELECT [Literale, Aggregatfunktionen, Gruppierungsfelder]
FROM tabellen_name
GROUP BY gruppierungsfelder;
```

So sieht zum Beispiel eine Query mit der Aggregatfunktion `AVG` aus:

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
```

| home_type       | avg_price |
| --------------- | --------- |
| Private room    | 89.4286   |
| Entire home/apt | 148.6667  |
| Shared room     | 40        |

## Beschreibung der Aggregatfunktionen

| Funktion               | Beschreibung                          |
| :--------------------- | :------------------------------------ |
| `SUM(tabellen_feld)`   | Gibt die Summe der Werte zurück       |
| `AVG(tabellen_feld)`   | Gibt den Durchschnittswert zurück     |
| `COUNT(tabellen_feld)` | Gibt die Anzahl der Datensätze zurück |
| `MIN(tabellen_feld)`   | Gibt den minimalen Wert zurück        |
| `MAX(tabellen_feld)`   | Gibt den maximalen Wert zurück        |

> Aggregatfunktionen berücksichtigen nur Werte, die ungleich `NULL` sind. Eine Ausnahme bildet `COUNT(*)`.

## Beispiele

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/aggregate-functions).

- Ermitteln wir die Anzahl jedes Unterkunftstyps und sortieren die Liste absteigend:

    ```sql
    SELECT home_type, COUNT(*) as amount FROM Rooms
    GROUP BY home_type
    ORDER BY amount DESC
    ```

    | home_type       | amount |
    | --------------- | ------ |
    | Private room    | 28     |
    | Entire home/apt | 21     |
    | Shared room     | 1      |

- Suchen wir für jede Unterkunft das späteste Abreisedatum (Feld `end_date`):

    ```sql
    SELECT room_id, MAX(end_date) AS last_end_date FROM Reservations
    GROUP BY room_id
    ```

    | room_id | last_end_date            |
    | ------- | ------------------------ |
    | 1       | 2019-02-04T12:00:00.000Z |
    | 2       | 2020-03-23T09:00:00.000Z |
    | 13      | 2020-04-21T10:00:00.000Z |
    | 16      | 2019-06-24T10:00:00.000Z |
    | 21      | 2020-02-29T10:00:00.000Z |
    | 19      | 2020-05-02T10:00:00.000Z |
    | 8       | 2020-01-21T12:00:00.000Z |
    | 7       | 2019-09-17T10:00:00.000Z |
    | 5       | 2020-05-15T10:00:00.000Z |
    | 50      | 2019-11-25T11:00:00.000Z |
    | 49      | 2020-06-11T10:00:00.000Z |
    | 48      | 2019-11-10T10:00:00.000Z |
    | 32      | 2020-01-18T13:00:00.000Z |
    | 17      | 2019-11-05T09:00:00.000Z |
    | 25      | 2020-04-22T09:00:00.000Z |
    | 14      | 2020-02-12T10:00:00.000Z |
    | 39      | 2019-12-09T10:00:00.000Z |
    | 38      | 2020-03-23T10:00:00.000Z |
