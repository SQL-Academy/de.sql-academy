---
meta:
    title: "Gruppierung, der GROUP BY-Operator"
    description: "Aufbau einer SQL-Query mit dem Gruppierungsoperator GROUP BY, Gruppierung nach mehreren Feldern und Beispiele"
---

# Gruppierung, der GROUP BY-Operator

Lass uns eine Query ausführen:

```sql
SELECT id, home_type, has_tv, price FROM Rooms;
```

**MySQL**

| id  | home_type       | has_tv | price |
| --- | --------------- | ------ | ----- |
| 1   | Private room    | 1      | 149   |
| 2   | Entire home/apt | 0      | 225   |
| 3   | Private room    | 1      | 150   |
| 4   | Entire home/apt | 1      | 89    |
| 5   | Entire home/apt | 0      | 80    |
| 6   | Entire home/apt | 0      | 200   |
| 7   | Private room    | 0      | 60    |
| 8   | Private room    | 1      | 79    |
| 9   | Private room    | 1      | 79    |
| 10  | Entire home/apt | 1      | 150   |
| 11  | Entire home/apt | 1      | 135   |
| 12  | Private room    | 0      | 85    |
| 13  | Private room    | 0      | 89    |
| 14  | Private room    | 0      | 85    |
| 15  | Entire home/apt | 1      | 120   |
| 40  | Shared room     | 1      | 40    |

**PostgreSQL**

| id  | home_type       | has_tv | price |
| --- | --------------- | ------ | ----- |
| 1   | Private room    | true   | 149   |
| 2   | Entire home/apt | false  | 225   |
| 3   | Private room    | true   | 150   |
| 4   | Entire home/apt | true   | 89    |
| 5   | Entire home/apt | false  | 80    |
| 6   | Entire home/apt | false  | 200   |
| 7   | Private room    | false  | 60    |
| 8   | Private room    | true   | 79    |
| 9   | Private room    | true   | 79    |
| 10  | Entire home/apt | true   | 150   |
| 11  | Entire home/apt | true   | 135   |
| 12  | Private room    | false  | 85    |
| 13  | Private room    | false  | 89    |
| 14  | Private room    | false  | 85    |
| 15  | Entire home/apt | true   | 120   |
| 40  | Shared room     | true   | 40    |

So bekommen wir Infos zu jeder einzelnen vermieteten Unterkunft. Aber was, wenn wir nicht jede einzelne Zeile sehen wollen, sondern Informationen über die Gruppen, die sie bilden?

Solche Gruppen könnten zum Beispiel die Datensätze sein, aufgeteilt nach Unterkunftstyp:

- Shared room (Zimmer für mehrere Personen mieten)
- Private room (ein eigenes Zimmer mieten)
- Entire home/apt (eine komplette Wohnung mieten)

Diese Gruppen enthalten unterschiedliche Datensätze und haben entsprechend unterschiedliche Eigenschaften, die sehr nützlich sein können.

Solche nützlichen Informationen über Gruppen können sein:

- der durchschnittliche Mietpreis für ein Zimmer oder eine komplette Unterkunft
- die Anzahl der vermieteten Unterkünfte pro Typ

Genau dafür gibt es den `GROUP BY`-Operator.

## Allgemeine Struktur einer Query mit GROUP BY

```sql
SELECT [Literale, Aggregatfunktionen, Gruppierungsfelder]
FROM tabellen_name
GROUP BY gruppierungsfelder;
```

Damit unsere Datensätze nach Unterkunftstyp gruppiert werden, müssen wir nach `GROUP BY` das Feld `home_type` angeben – also das Feld, nach dem gruppiert wird.

```sql
SELECT home_type FROM Rooms
GROUP BY home_type
```

| home_type       |
| --------------- |
| Private room    |
| Entire home/apt |
| Shared room     |

> Beachte: Bei `GROUP BY` werden alle `NULL`-Werte als gleich behandelt.
> Wird also nach einem Feld gruppiert, das `NULL`-Werte enthält, landen alle diese Zeilen in einer einzigen Gruppe.

Mit `GROUP BY` arbeiten wir nicht mehr mit einzelnen Datensätzen, sondern mit den gebildeten Gruppen.
Deshalb können wir nicht einfach beliebige Felder aus einem Datensatz ausgeben (zum Beispiel `has_tv` oder `price`), wie wir das vorher konnten.
Denn in jeder Gruppe können mehrere Datensätze stehen, und jeder davon kann in diesem Feld einen anderen Wert haben.

Mit `GROUP BY` darfst du nur folgende Dinge ausgeben:

- Literale, also explizit angegebene feste Werte.

    Die kannst du ausgeben, weil es feste Werte sind, die von nichts abhängen.
    Zum Beispiel:

    ```sql
    SELECT home_type, 'literal' FROM Rooms
    GROUP BY home_type
    ```

    | home_type       | literal |
    | --------------- | ------- |
    | Private room    | literal |
    | Entire home/apt | literal |
    | Shared room     | literal |

- Ergebnisse von Aggregatfunktionen, also berechnete Werte auf Basis einer Wertemenge.

    Aggregatfunktionen behandeln wir ausführlich in der nächsten Lektion. Zur Veranschaulichung schauen wir uns aber kurz `AVG` an.
    `AVG` nimmt als Argument den Namen des Feldes, für das wir den Durchschnitt pro Gruppe berechnen wollen.

    ```sql
    SELECT home_type, AVG(price) as avg_price FROM Rooms
    GROUP BY home_type
    ```

    | home_type       | avg_price |
    | --------------- | --------- |
    | Private room    | 89.4286   |
    | Entire home/apt | 148.6667  |
    | Shared room     | 40        |

    Die ausgeführte Query teilt alle Datensätze aus `Rooms` zunächst anhand von `home_type` in 3 Gruppen auf.
    Dann summiert sie für jede Gruppe alle Werte aus dem Feld `price` der jeweiligen Datensätze und teilt das Ergebnis
    durch die Anzahl der Datensätze in dieser Gruppe.

- Gruppierungsfelder.

    Die kannst du ausgeben, weil die Werte der Felder, nach denen gruppiert wird, innerhalb einer Gruppe identisch sind.

## Gruppierung nach 2 und mehr Feldern

Wir haben gesehen, wie die Datensätze einer Tabelle nach einem Feld gruppiert werden.
Zur Veranschaulichung sieht es bei einer Gruppierung nach `home_type` etwa so aus:

![Gruppierung nach einem Feld](https://sql-academy.org/static/guidePage/groupping/groupping_by_1_field.png "Gruppierung nach einem Feld")

Beim Gruppieren nach 2 oder mehr Feldern bleibt das Prinzip dasselbe, nur werden die gebildeten Gruppen zusätzlich anhand
des zweiten Gruppierungsfeldes weiter unterteilt.

Beispiel für eine Gruppierung nach `home_type` und `has_tv`:

![Gruppierung nach zwei Feldern](https://sql-academy.org/static/guidePage/groupping/groupping_by_2_field.png "Gruppierung nach zwei Feldern")

Wollen wir uns selbst testen? Was kann mit `GROUP BY` in einem `SELECT` ausgegeben werden?

1. **Richtige Antwort:** Nur Literale, Ergebnisse von Aggregatfunktionen und Gruppierungsfelder — Bei einer Gruppierung dürfen im SELECT nur Literale, Ergebnisse von Aggregatfunktionen und die Felder ausgegeben werden, nach denen gruppiert wurde.

2. Alle Felder, die wir auch für jeden einzelnen Datensatz hätten ausgeben können — Bei einer Gruppierung dürfen im SELECT nur Literale, Ergebnisse von Aggregatfunktionen und die Felder ausgegeben werden, nach denen gruppiert wurde.

3. Nur Literale und Ergebnisse von Aggregatfunktionen — Neben Literalen und Ergebnissen von Aggregatfunktionen darfst du auch die Felder ausgeben, nach denen gruppiert wurde.
