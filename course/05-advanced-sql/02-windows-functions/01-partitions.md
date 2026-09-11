---
meta:
    title: "Partitionen in Window Functions"
    description: "Partitionen in Window Functions in SQL. Partitionierung über mehrere Spalten. Syntax von Partitionen."
---

# Partitionen in Window Functions

Im <a href="https://sql-academy.org/de/guide/windows-functions" target="_blank">vorigen Artikel</a> haben wir bereits kurz erwähnt,
was Partitionen sind und wie man sie in Window Functions verwendet. Zeit, sie genauer zu betrachten 🤓.

## Was ist eine Partition?

> Partitionen sind Teilmengen von Zeilen, die für eine Window Function auf Basis einer oder mehrerer Spalten gebildet werden.

Sie dienen dazu, Daten zu segmentieren und erlauben so eine genauere Analyse
sowie Berechnungen wie Aggregation oder Ranking innerhalb jeder Gruppe.

Wenn wir zum Beispiel eine Tabelle mit Mietpreisen nach dem Unterkunftstyp partitionieren,
können wir in einer separaten Spalte etwa den Durchschnittspreis pro Unterkunftstyp berechnen.

![Schema einer Partitionierung](https://sql-academy.org/static/guidePage/windows-functions/3_de.png "Schema einer Partitionierung")

## Partitionen in SQL anwenden

Um eine Partition zusammen mit einer Window Function zu verwenden, hältst du dich an folgende Syntax:

```sql
SELECT <window_function>(<tabellen_feld>)
OVER (
    PARTITION BY <spalten_zum_partitionieren>
)
```

### Beispiel

Schauen wir uns die Verwendung einer Partition mit einer Window Function an einem einfachen Beispiel an.

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/partitions).

Wir betrachten die Tabelle `Rooms`, konkret die Felder `home_type` und `price`:

```sql
SELECT home_type, price FROM Rooms;
```

| home_type       | price |
| --------------- | ----- |
| Private room    | 149   |
| Entire home/apt | 225   |
| Private room    | 150   |
| Entire home/apt | 89    |
| Entire home/apt | 80    |
| Entire home/apt | 200   |
| Private room    | 60    |
| Private room    | 79    |
| Private room    | 79    |
| Entire home/apt | 150   |
| Entire home/apt | 135   |
| Private room    | 85    |
| Private room    | 89    |
| Private room    | 85    |
| Entire home/apt | 120   |
| Entire home/apt | 140   |
| Entire home/apt | 215   |
| Private room    | 140   |
| Entire home/apt | 99    |
| Entire home/apt | 190   |
| Entire home/apt | 299   |
| Private room    | 130   |
| Private room    | 80    |
| Private room    | 110   |
| Entire home/apt | 120   |
| Private room    | 60    |
| Private room    | 80    |
| Entire home/apt | 150   |
| Private room    | 44    |
| Entire home/apt | 180   |
| Private room    | 50    |
| Private room    | 52    |
| Private room    | 55    |
| Private room    | 50    |
| Private room    | 70    |
| Private room    | 89    |
| Private room    | 35    |
| Entire home/apt | 85    |
| Private room    | 150   |
| Shared room     | 40    |
| Private room    | 68    |
| Entire home/apt | 120   |
| Private room    | 120   |
| Private room    | 135   |
| Entire home/apt | 150   |
| Entire home/apt | 150   |
| Private room    | 130   |
| Entire home/apt | 110   |
| Entire home/apt | 115   |
| Private room    | 80    |

Wir sehen, dass alle Mietangebote in 3 Kategorien aufgeteilt sind: „Private room“, „Entire home/apt“ und „Shared room“.

Jede Kategorie hat ihren eigenen Preisrahmen.
Um den Durchschnittspreis innerhalb einer Kategorie zu ermitteln und ihn mit dem aktuellen Preis zu vergleichen, kommen Window Functions wie gerufen.

Dazu ergänzen wir unsere Ergebnistabelle um eine weitere Spalte `avg_price`, die den Durchschnittspreis pro Kategorie berechnet. Das sieht so aus:

```sql
SELECT
    home_type, price,
    AVG(price) OVER (PARTITION BY home_type) AS avg_price
FROM Rooms
```

| home_type       | price | avg_price |
| --------------- | ----- | --------- |
| Entire home/apt | 225   | 148.6667  |
| Entire home/apt | 180   | 148.6667  |
| Entire home/apt | 150   | 148.6667  |
| Entire home/apt | 85    | 148.6667  |
| Entire home/apt | 120   | 148.6667  |
| Entire home/apt | 120   | 148.6667  |
| Entire home/apt | 299   | 148.6667  |
| Entire home/apt | 190   | 148.6667  |
| Entire home/apt | 99    | 148.6667  |
| Entire home/apt | 215   | 148.6667  |
| Entire home/apt | 140   | 148.6667  |
| Entire home/apt | 120   | 148.6667  |
| Entire home/apt | 150   | 148.6667  |
| Entire home/apt | 135   | 148.6667  |
| Entire home/apt | 150   | 148.6667  |
| Entire home/apt | 110   | 148.6667  |
| Entire home/apt | 115   | 148.6667  |
| Entire home/apt | 200   | 148.6667  |
| Entire home/apt | 150   | 148.6667  |
| Entire home/apt | 80    | 148.6667  |
| Entire home/apt | 89    | 148.6667  |
| Private room    | 68    | 89.4286   |
| Private room    | 50    | 89.4286   |
| Private room    | 70    | 89.4286   |
| Private room    | 80    | 89.4286   |
| Private room    | 89    | 89.4286   |
| Private room    | 149   | 89.4286   |
| Private room    | 35    | 89.4286   |
| Private room    | 150   | 89.4286   |
| Private room    | 130   | 89.4286   |
| Private room    | 120   | 89.4286   |
| Private room    | 135   | 89.4286   |
| Private room    | 130   | 89.4286   |
| Private room    | 150   | 89.4286   |
| Private room    | 60    | 89.4286   |
| Private room    | 79    | 89.4286   |
| Private room    | 79    | 89.4286   |
| Private room    | 85    | 89.4286   |
| Private room    | 89    | 89.4286   |
| Private room    | 85    | 89.4286   |
| Private room    | 140   | 89.4286   |
| Private room    | 55    | 89.4286   |
| Private room    | 80    | 89.4286   |
| Private room    | 110   | 89.4286   |
| Private room    | 60    | 89.4286   |
| Private room    | 80    | 89.4286   |
| Private room    | 44    | 89.4286   |
| Private room    | 50    | 89.4286   |
| Private room    | 52    | 89.4286   |
| Shared room     | 40    | 40        |

Was passiert in der hinzugefügten Zeile?

- `PARTITION BY home_type` teilt alle Datensätze in verschiedene Partitionen auf Basis der eindeutigen Werte der Spalte `home_type` auf.
- Anschließend berechnet `AVG(price)` für jeden Datensatz den Durchschnittspreis (`price`) innerhalb seiner Partition (`home_type`).

Das Ergebnis dieses Teils der Query ist die Spalte `avg_price`,
in der für jeden Datensatz der Durchschnittspreis seiner Unterkunftskategorie (`home_type`) steht.

## Partitionen über mehrere Spalten

Die Partitionierung kann auch über mehrere Spalten erfolgen. Das ermöglicht komplexere und genauere Segmente für die Analyse.

Für unsere Tabelle `Rooms` können wir zum Beispiel Partitionen auf Basis von 2 Spalten bilden: der Unterkunftskategorie
`home_type` und der Verfügbarkeit eines Fernsehers `has_tv`.

Beispiel-Query mit Partitionierung nach zwei Spalten:

```sql
SELECT
    home_type, has_tv, price,
    AVG(price) OVER (PARTITION BY home_type, has_tv) AS avg_price
    FROM Rooms
```

| home_type       | has_tv | price | avg_price |
| --------------- | ------ | ----- | --------- |
| Entire home/apt | 0      | 225   | 170       |
| Entire home/apt | 0      | 180   | 170       |
| Entire home/apt | 0      | 80    | 170       |
| Entire home/apt | 0      | 200   | 170       |
| Entire home/apt | 0      | 150   | 170       |
| Entire home/apt | 0      | 150   | 170       |
| Entire home/apt | 0      | 190   | 170       |
| Entire home/apt | 0      | 215   | 170       |
| Entire home/apt | 0      | 140   | 170       |
| Entire home/apt | 1      | 99    | 132.6667  |
| Entire home/apt | 1      | 85    | 132.6667  |
| Entire home/apt | 1      | 150   | 132.6667  |
| Entire home/apt | 1      | 120   | 132.6667  |
| Entire home/apt | 1      | 120   | 132.6667  |
| Entire home/apt | 1      | 299   | 132.6667  |
| Entire home/apt | 1      | 120   | 132.6667  |
| Entire home/apt | 1      | 135   | 132.6667  |
| Entire home/apt | 1      | 150   | 132.6667  |
| Entire home/apt | 1      | 110   | 132.6667  |
| Entire home/apt | 1      | 89    | 132.6667  |
| Entire home/apt | 1      | 115   | 132.6667  |
| Private room    | 0      | 85    | 78.5455   |
| Private room    | 0      | 35    | 78.5455   |
| Private room    | 0      | 150   | 78.5455   |
| Private room    | 0      | 55    | 78.5455   |
| Private room    | 0      | 52    | 78.5455   |
| Private room    | 0      | 50    | 78.5455   |
| Private room    | 0      | 68    | 78.5455   |
| Private room    | 0      | 60    | 78.5455   |
| Private room    | 0      | 135   | 78.5455   |
| Private room    | 0      | 85    | 78.5455   |
| Private room    | 0      | 89    | 78.5455   |
| Private room    | 1      | 120   | 96.4706   |
| Private room    | 1      | 80    | 96.4706   |
| Private room    | 1      | 149   | 96.4706   |
| Private room    | 1      | 130   | 96.4706   |
| Private room    | 1      | 89    | 96.4706   |
| Private room    | 1      | 70    | 96.4706   |
| Private room    | 1      | 50    | 96.4706   |
| Private room    | 1      | 44    | 96.4706   |
| Private room    | 1      | 80    | 96.4706   |
| Private room    | 1      | 60    | 96.4706   |
| Private room    | 1      | 110   | 96.4706   |
| Private room    | 1      | 80    | 96.4706   |
| Private room    | 1      | 130   | 96.4706   |
| Private room    | 1      | 140   | 96.4706   |
| Private room    | 1      | 79    | 96.4706   |
| Private room    | 1      | 79    | 96.4706   |
| Private room    | 1      | 150   | 96.4706   |
| Shared room     | 1      | 40    | 40        |

Hier bildet `PARTITION BY home_type, has_tv` eindeutige Partitionen für jede Kombination aus `home_type` und `has_tv`
und erlaubt es, den Durchschnittspreis innerhalb einer Kategorie für Unterkünfte mit bzw. ohne Fernseher zu berechnen.

![Partitionen über 2 Spalten](https://sql-academy.org/static/guidePage/partitions/2-columns-partition_de.png "Partitionen über 2 Spalten")
