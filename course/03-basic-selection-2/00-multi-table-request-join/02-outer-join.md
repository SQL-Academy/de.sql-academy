---
meta:
    title: "Äußerer Join: OUTER JOIN"
    description: "Wie LEFT, RIGHT und FULL OUTER JOIN arbeiten: Zeilen ohne Gegenstück, NULL im Ergebnis und warum es mehr Zeilen werden"
---

# Äußerer Join: OUTER JOIN

Ein innerer Join behält nur die Zeilen, für die es in der anderen Tabelle ein Gegenstück gibt. Ein äußerer Join arbeitet anders: Er liefert immer alle Zeilen einer Tabelle oder beider Tabellen und füllt die fehlende Hälfte mit `NULL` auf.

Es gibt drei Arten des äußeren Joins: links (`LEFT`), rechts (`RIGHT`) und vollständig (`FULL`). Die Art musst du angeben — ein bloßes `OUTER JOIN` ist ein Syntaxfehler. Das Wort `OUTER` selbst ist dagegen optional: `LEFT JOIN` und `LEFT OUTER JOIN` bedeuten dasselbe, und im Folgenden wird die kurze Schreibweise verwendet.

> Verwechsle das nicht mit einem `JOIN` ohne Zusatz — das ist ein innerer Join, dasselbe wie `INNER JOIN`.

## Linker äußerer Join (LEFT OUTER JOIN)

Liefert alle Zeilen der linken Tabelle. Findet eine Zeile ein Gegenstück in der rechten Tabelle, werden beide Zeilen zusammengefügt; findet sie keines, werden die Spalten der rechten Tabelle mit `NULL` gefüllt.

Als Beispiel holen wir aus der Datenbank den Klingelplan, verknüpft mit den passenden Einträgen aus dem Stundenplan.

ER-Diagramm der Datenbank Schedule: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/outer-join).

Daten in der Tabelle `Timepair` (Klingelplan):

| id  | start_pair | end_pair |
| --- | ---------- | -------- |
| 1   | 08:30:00   | 09:15:00 |
| 2   | 09:20:00   | 10:05:00 |
| 3   | 10:15:00   | 11:00:00 |
| 4   | 11:05:00   | 11:50:00 |
| 5   | 12:50:00   | 13:35:00 |
| 6   | 13:40:00   | 14:25:00 |
| 7   | 14:35:00   | 15:20:00 |
| 8   | 15:25:00   | 16:10:00 |

Daten in der Tabelle `Schedule` (Stundenplan):

| id  | date                     | class | number_pair | teacher | subject | classroom |
| --- | ------------------------ | ----- | ----------- | ------- | ------- | --------- |
| 1   | 2019-09-01T00:00:00.000Z | 9     | 1           | 11      | 1       | 47        |
| 2   | 2019-09-01T00:00:00.000Z | 9     | 2           | 8       | 2       | 13        |
| 3   | 2019-09-01T00:00:00.000Z | 9     | 3           | 4       | 3       | 13        |
| 4   | 2019-09-02T00:00:00.000Z | 9     | 1           | 4       | 3       | 13        |
| 5   | 2019-09-02T00:00:00.000Z | 9     | 2           | 2       | 4       | 34        |
| 6   | 2019-09-02T00:00:00.000Z | 9     | 3           | 6       | 5       | 35        |
| 7   | 2019-09-03T00:00:00.000Z | 9     | 1           | 5       | 6       | 36        |
| 8   | 2019-09-03T00:00:00.000Z | 9     | 2           | 13      | 7       | 37        |
| 9   | 2019-09-03T00:00:00.000Z | 9     | 3           | 6       | 8       | 38        |
| 10  | 2019-09-04T00:00:00.000Z | 9     | 1           | 9       | 9       | 39        |
| 11  | 2019-09-04T00:00:00.000Z | 9     | 2           | 10      | 10      | 40        |
| 12  | 2019-09-04T00:00:00.000Z | 9     | 3           | 3       | 11      | 41        |
| 13  | 2019-09-05T00:00:00.000Z | 9     | 1           | 3       | 13      | 43        |
| 14  | 2019-09-05T00:00:00.000Z | 9     | 2           | 11      | 1       | 47        |
| 15  | 2019-09-05T00:00:00.000Z | 9     | 3           | 5       | 6       | 36        |
| 16  | 2019-08-30T00:00:00.000Z | 9     | 1           | 2       | 4       | 34        |
| 17  | 2019-08-30T00:00:00.000Z | 9     | 2           | 8       | 2       | 13        |
| 18  | 2019-08-30T00:00:00.000Z | 9     | 3           | 6       | 5       | 35        |
| 19  | 2019-08-30T00:00:00.000Z | 9     | 4           | 10      | 1       | 47        |
| 20  | 2019-09-03T00:00:00.000Z | 9     | 4           | 10      | 10      | 40        |
| 21  | 2019-08-30T00:00:00.000Z | 8     | 1           | 7       | 9       | 53        |
| 22  | 2019-08-30T00:00:00.000Z | 8     | 2           | 7       | 9       | 53        |
| 23  | 2019-08-30T00:00:00.000Z | 8     | 3           | 8       | 2       | 38        |
| 24  | 2019-08-30T00:00:00.000Z | 8     | 4           | 11      | 1       | 43        |
| 25  | 2019-08-30T00:00:00.000Z | 8     | 5           | 8       | 3       | 39        |
| 26  | 2019-09-01T00:00:00.000Z | 8     | 2           | 2       | 4       | 34        |
| 27  | 2019-09-01T00:00:00.000Z | 8     | 3           | 6       | 5       | 35        |
| 28  | 2019-09-01T00:00:00.000Z | 8     | 4           | 12      | 6       | 36        |
| 29  | 2019-09-01T00:00:00.000Z | 8     | 5           | 13      | 7       | 37        |
| 30  | 2019-09-02T00:00:00.000Z | 8     | 3           | 6       | 8       | 38        |
| 31  | 2019-09-02T00:00:00.000Z | 8     | 4           | 7       | 9       | 53        |
| 32  | 2019-09-03T00:00:00.000Z | 8     | 1           | 10      | 10      | 40        |
| 33  | 2019-09-03T00:00:00.000Z | 8     | 2           | 7       | 9       | 53        |
| 34  | 2019-09-03T00:00:00.000Z | 8     | 3           | 7       | 9       | 53        |
| 35  | 2019-09-04T00:00:00.000Z | 8     | 1           | 1       | 11      | 4         |
| 36  | 2019-09-04T00:00:00.000Z | 8     | 2           | 1       | 12      | 42        |
| 37  | 2019-09-04T00:00:00.000Z | 8     | 3           | 3       | 13      | 43        |
| 38  | 2019-09-04T00:00:00.000Z | 8     | 4           | 8       | 2       | 42        |
| 39  | 2019-09-04T00:00:00.000Z | 8     | 5           | 11      | 1       | 43        |
| 40  | 2019-09-05T00:00:00.000Z | 8     | 2           | 11      | 1       | 43        |

```sql
SELECT Timepair.id "timepair.id", start_pair, end_pair,
    Schedule.id "schedule.id", date, class, number_pair, teacher, subject, classroom
FROM Timepair
    LEFT JOIN Schedule ON Schedule.number_pair = Timepair.id;
```

| timepair.id | start_pair | end_pair | schedule.id | date                     | class | number_pair | teacher | subject | classroom |
| ----------- | ---------- | -------- | ----------- | ------------------------ | ----- | ----------- | ------- | ------- | --------- |
| 1           | 08:30:00   | 09:15:00 | 35          | 2019-09-04T00:00:00.000Z | 8     | 1           | 1       | 11      | 4         |
| 1           | 08:30:00   | 09:15:00 | 32          | 2019-09-03T00:00:00.000Z | 8     | 1           | 10      | 10      | 40        |
| 1           | 08:30:00   | 09:15:00 | 21          | 2019-08-30T00:00:00.000Z | 8     | 1           | 7       | 9       | 53        |
| 1           | 08:30:00   | 09:15:00 | 16          | 2019-08-30T00:00:00.000Z | 9     | 1           | 2       | 4       | 34        |
| 1           | 08:30:00   | 09:15:00 | 13          | 2019-09-05T00:00:00.000Z | 9     | 1           | 3       | 13      | 43        |
| 1           | 08:30:00   | 09:15:00 | 10          | 2019-09-04T00:00:00.000Z | 9     | 1           | 9       | 9       | 39        |
| 1           | 08:30:00   | 09:15:00 | 7           | 2019-09-03T00:00:00.000Z | 9     | 1           | 5       | 6       | 36        |
| 1           | 08:30:00   | 09:15:00 | 4           | 2019-09-02T00:00:00.000Z | 9     | 1           | 4       | 3       | 13        |
| 1           | 08:30:00   | 09:15:00 | 1           | 2019-09-01T00:00:00.000Z | 9     | 1           | 11      | 1       | 47        |
| 2           | 09:20:00   | 10:05:00 | 40          | 2019-09-05T00:00:00.000Z | 8     | 2           | 11      | 1       | 43        |
| 2           | 09:20:00   | 10:05:00 | 36          | 2019-09-04T00:00:00.000Z | 8     | 2           | 1       | 12      | 42        |
| 2           | 09:20:00   | 10:05:00 | 33          | 2019-09-03T00:00:00.000Z | 8     | 2           | 7       | 9       | 53        |
| 2           | 09:20:00   | 10:05:00 | 26          | 2019-09-01T00:00:00.000Z | 8     | 2           | 2       | 4       | 34        |
| 2           | 09:20:00   | 10:05:00 | 22          | 2019-08-30T00:00:00.000Z | 8     | 2           | 7       | 9       | 53        |
| 2           | 09:20:00   | 10:05:00 | 17          | 2019-08-30T00:00:00.000Z | 9     | 2           | 8       | 2       | 13        |
| 2           | 09:20:00   | 10:05:00 | 14          | 2019-09-05T00:00:00.000Z | 9     | 2           | 11      | 1       | 47        |
| 2           | 09:20:00   | 10:05:00 | 11          | 2019-09-04T00:00:00.000Z | 9     | 2           | 10      | 10      | 40        |
| 2           | 09:20:00   | 10:05:00 | 8           | 2019-09-03T00:00:00.000Z | 9     | 2           | 13      | 7       | 37        |
| 2           | 09:20:00   | 10:05:00 | 5           | 2019-09-02T00:00:00.000Z | 9     | 2           | 2       | 4       | 34        |
| 2           | 09:20:00   | 10:05:00 | 2           | 2019-09-01T00:00:00.000Z | 9     | 2           | 8       | 2       | 13        |
| 3           | 10:15:00   | 11:00:00 | 37          | 2019-09-04T00:00:00.000Z | 8     | 3           | 3       | 13      | 43        |
| 3           | 10:15:00   | 11:00:00 | 34          | 2019-09-03T00:00:00.000Z | 8     | 3           | 7       | 9       | 53        |
| 3           | 10:15:00   | 11:00:00 | 30          | 2019-09-02T00:00:00.000Z | 8     | 3           | 6       | 8       | 38        |
| 3           | 10:15:00   | 11:00:00 | 27          | 2019-09-01T00:00:00.000Z | 8     | 3           | 6       | 5       | 35        |
| 3           | 10:15:00   | 11:00:00 | 23          | 2019-08-30T00:00:00.000Z | 8     | 3           | 8       | 2       | 38        |
| 3           | 10:15:00   | 11:00:00 | 18          | 2019-08-30T00:00:00.000Z | 9     | 3           | 6       | 5       | 35        |
| 3           | 10:15:00   | 11:00:00 | 15          | 2019-09-05T00:00:00.000Z | 9     | 3           | 5       | 6       | 36        |
| 3           | 10:15:00   | 11:00:00 | 12          | 2019-09-04T00:00:00.000Z | 9     | 3           | 3       | 11      | 41        |
| 3           | 10:15:00   | 11:00:00 | 9           | 2019-09-03T00:00:00.000Z | 9     | 3           | 6       | 8       | 38        |
| 3           | 10:15:00   | 11:00:00 | 6           | 2019-09-02T00:00:00.000Z | 9     | 3           | 6       | 5       | 35        |
| 3           | 10:15:00   | 11:00:00 | 3           | 2019-09-01T00:00:00.000Z | 9     | 3           | 4       | 3       | 13        |
| 4           | 11:05:00   | 11:50:00 | 38          | 2019-09-04T00:00:00.000Z | 8     | 4           | 8       | 2       | 42        |
| 4           | 11:05:00   | 11:50:00 | 31          | 2019-09-02T00:00:00.000Z | 8     | 4           | 7       | 9       | 53        |
| 4           | 11:05:00   | 11:50:00 | 28          | 2019-09-01T00:00:00.000Z | 8     | 4           | 12      | 6       | 36        |
| 4           | 11:05:00   | 11:50:00 | 24          | 2019-08-30T00:00:00.000Z | 8     | 4           | 11      | 1       | 43        |
| 4           | 11:05:00   | 11:50:00 | 20          | 2019-09-03T00:00:00.000Z | 9     | 4           | 10      | 10      | 40        |
| 4           | 11:05:00   | 11:50:00 | 19          | 2019-08-30T00:00:00.000Z | 9     | 4           | 10      | 1       | 47        |
| 5           | 12:50:00   | 13:35:00 | 39          | 2019-09-04T00:00:00.000Z | 8     | 5           | 11      | 1       | 43        |
| 5           | 12:50:00   | 13:35:00 | 29          | 2019-09-01T00:00:00.000Z | 8     | 5           | 13      | 7       | 37        |
| 5           | 12:50:00   | 13:35:00 | 25          | 2019-08-30T00:00:00.000Z | 8     | 5           | 8       | 3       | 39        |
| 6           | 13:40:00   | 14:25:00 | null        | null                     | null  | null        | null    | null    | null      |
| 7           | 14:35:00   | 15:20:00 | null        | null                     | null  | null        | null    | null    | null      |
| 8           | 15:25:00   | 16:10:00 | null        | null                     | null  | null        | null    | null    | null      |

Alle acht Klingelzeiten sind im Ergebnis gelandet — genau das verspricht der linke Join. Zeilen sind es aber 43 und nicht 8.

Ein Join ergänzt die linke Tabelle nicht, sondern geht alle passenden Zeilenpaare durch. Dieselbe Stundennummer kommt im Stundenplan viele Male vor — an verschiedenen Tagen und in verschiedenen Klassen —, und jede Übereinstimmung ergibt eine eigene Zeile. Ist der Schlüssel in der rechten Tabelle nicht eindeutig, hat das Ergebnis mehr Zeilen als die linke Tabelle.

Am Ende der Ergebnismenge stehen Zeilen, in denen alle Spalten des Stundenplans `NULL` sind. Das sind die Klingelzeiten ohne jeden Unterricht: Ein Gegenstück fehlt, aber eine Zeile der linken Tabelle muss im Ergebnis auftauchen.

### Zeilen ohne Gegenstück

Auf diesen `NULL`-Werten beruht der häufigste Praxis-Kniff — Datensätze finden, die kein Gegenstück haben. Dazu genügt es, nur die Zeilen zu behalten, in denen der Schlüssel der rechten Tabelle leer ist:

```sql
SELECT Timepair.id, start_pair, end_pair
FROM Timepair
    LEFT JOIN Schedule ON Schedule.number_pair = Timepair.id
WHERE Schedule.number_pair IS NULL;
```

| id  | start_pair | end_pair |
| --- | ---------- | -------- |
| 6   | 13:40:00   | 14:25:00 |
| 7   | 14:35:00   | 15:20:00 |
| 8   | 15:25:00   | 16:10:00 |

Übrig bleiben drei Klingelzeiten, für die kein Unterricht eingeplant ist.

Eine Verknüpfung, aus der nur die Zeilen ohne Gegenstück übrig bleiben, nennt man **Anti-Join** (`ANTI JOIN`). Einen eigenen Operator hat er nicht: Sowohl in MySQL als auch in PostgreSQL schreibt man ihn genau so — als Verknüpfung mit einer `IS NULL`-Bedingung.

## Rechter äußerer Join (RIGHT OUTER JOIN)

Das Spiegelbild des linken Joins: Alle Zeilen der rechten Tabelle landen garantiert im Ergebnis, und die fehlenden Spalten der linken Tabelle werden mit `NULL` gefüllt.

```sql
SELECT Timepair.id "timepair.id", start_pair, end_pair,
    Schedule.id "schedule.id", date, class, number_pair, teacher, subject, classroom
FROM Timepair
    RIGHT JOIN Schedule ON Schedule.number_pair = Timepair.id;
```

Das Ergebnis hat 40 Zeilen — genau so viele, wie `Schedule` Datensätze enthält — und keine einzige Zeile mit `NULL`. Es stimmt also vollständig mit dem inneren Join überein.

Der Grund: Jeder Unterrichtseintrag verweist auf eine vorhandene Klingelzeit, die rechte Tabelle hat schlicht keine Zeilen ohne Gegenstück. Die Art des Joins gibt die Regel vor, was am Ende im Ergebnis steht, entscheiden die Daten.

## Vollständiger äußerer Join (FULL OUTER JOIN)

Liefert alle Zeilen beider Tabellen. Zeilen mit Gegenstück werden zusammengefügt, Zeilen ohne Gegenstück aus der linken und der rechten Tabelle landen mit `NULL` anstelle der fehlenden Hälfte im Ergebnis.

Das Ergebnis eines vollständigen Joins besteht aus drei Teilen:

- den Zeilen des inneren Joins (`INNER JOIN`);
- den Zeilen der linken Tabelle ohne Gegenstück;
- den Zeilen der rechten Tabelle ohne Gegenstück.

**PostgreSQL**

```sql
SELECT Timepair.id "timepair.id", start_pair, end_pair,
    Schedule.id "schedule.id", date, class, number_pair, teacher, subject, classroom
FROM Timepair
    FULL OUTER JOIN Schedule ON Schedule.number_pair = Timepair.id;
```

Mit den Daten dieser Datenbank stimmt das Ergebnis mit dem linken Join überein — dieselben 43 Zeilen: Zeilen ohne Gegenstück gibt es hier nur links.

**MySQL**

MySQL unterstützt `FULL OUTER JOIN` nicht, dasselbe Ergebnis kannst du aber von Hand zusammensetzen: den linken Join nehmen und die Zeilen der rechten Tabelle ergänzen, die kein Gegenstück gefunden haben.

```sql
SELECT tabellen_felder
FROM linke_tabelle
    LEFT JOIN rechte_tabelle ON rechte_tabelle.key = linke_tabelle.key

UNION ALL

SELECT tabellen_felder
FROM linke_tabelle
    RIGHT JOIN rechte_tabelle ON rechte_tabelle.key = linke_tabelle.key
WHERE linke_tabelle.key IS NULL;
```

Die Bedingung im zweiten Teil ist zwingend: ohne sie kämen die Zeilen mit Gegenstück doppelt ins Ergebnis.

## Alle Arten von Tabellen-Joins

| Join-Typ          | Ergebnis                                 | Zeilen im Beispiel |
| ----------------- | ---------------------------------------- | -----------------: |
| `LEFT JOIN`       | Alle Zeilen der linken Tabelle           |                  4 |
| `INNER JOIN`      | Nur übereinstimmende Zeilen              |                  3 |
| `RIGHT JOIN`      | Alle Zeilen der rechten Tabelle          |                  4 |
| `FULL JOIN`       | Alle Zeilen beider Tabellen              |                  5 |
| `LEFT ANTI JOIN`  | Linke Tabelle ohne Gegenstück            |                  1 |
| `RIGHT ANTI JOIN` | Rechte Tabelle ohne Gegenstück           |                  1 |
| `FULL ANTI JOIN`  | Alles außer den übereinstimmenden Zeilen |                  2 |

Prüfen wir dich einmal. Die linke Tabelle hat 8 Zeilen. Wie viele Zeilen liefert ein `LEFT JOIN` mit der rechten Tabelle?

1. **Richtige Antwort:** Acht oder mehr — je nachdem, wie viele Gegenstücke gefunden wurden — Jede Zeile der linken Tabelle landet garantiert im Ergebnis, aber eine Zeile mit mehreren Gegenstücken in der rechten Tabelle ergibt mehrere Zeilen.

2. Genau acht: Ein linker Join liefert alle Zeilen der linken Tabelle — Alle acht Zeilen landen tatsächlich im Ergebnis, aber eine Zeile mit mehreren Gegenstücken vervielfacht sich — es werden mehr als acht Zeilen.

3. Höchstens acht: Die überzähligen Zeilen der rechten Tabelle fallen weg — Weg fallen nur die Zeilen der rechten Tabelle ohne Gegenstück. Übereinstimmungen fallen nicht weg: Jede ergibt eine eigene Ergebniszeile.
