---
meta:
    title: "Die wichtigsten Window-Funktionen"
    description: "Die wichtigsten Window-Funktionen in SQL — Aggregat-, Rang- und Versatzfunktionen. Erfahre, wie du Aggregatfunktionen wie SUM, COUNT, AVG, MAX und MIN zur Berechnung von Gesamtwerten einsetzt. Rangfunktionen wie ROW_NUMBER, RANK und DENSE_RANK erlauben es, Werte innerhalb eines Fensters zu ranken. Versatzfunktionen wie LAG, LEAD, FIRST_VALUE und LAST_VALUE ermöglichen den Zugriff auf Daten aus vorherigen und nachfolgenden Zeilen des Fensters."
---

# Die wichtigsten Window-Funktionen

In den vorigen Artikeln haben wir uns angeschaut, wie Window-Funktionen funktionieren, und das Konzept des Datenfensters kennengelernt, das an eine Window-Funktion übergeben wird. Jetzt ist es an der Zeit, sich anzusehen, welche Arten von Window-Funktionen es gibt.

## Arten von Window-Funktionen

![Kategorien der Window-Funktionen](https://sql-academy.org/static/guidePage/types-of-windows-functions/categories_of_windows_functions_de.png "Kategorien der Window-Funktionen")

Window-Funktionen lassen sich in 3 Gruppen einteilen:

- Aggregat-Window-Funktionen
- Rang-Window-Funktionen
- Versatz-Window-Funktionen

### Aggregat-Window-Funktionen

Aggregatfunktionen sind Funktionen, die auf einer Datenmenge arithmetische Berechnungen ausführen und ein Gesamtergebnis zurückgeben.

- `SUM` — berechnet die Gesamtsumme der Werte;
- `COUNT` — zählt die Gesamtanzahl der Einträge in einer Spalte;
- `AVG` — berechnet das arithmetische Mittel;
- `MAX` — ermittelt den größten Wert;
- `MIN` — ermittelt den kleinsten Wert.

```sql
SELECT id,
	home_type,
	price,
	SUM(price) OVER(PARTITION BY home_type) AS "Sum",
	COUNT(price) OVER(PARTITION BY home_type) AS "Count",
	AVG(price) OVER(PARTITION BY home_type) AS "Avg",
	MAX(price) OVER(PARTITION BY home_type) AS "Max",
	MIN(price) OVER(PARTITION BY home_type) AS "Min"
FROM Rooms;
```

| id  | home_type       | price | Sum  | Count | Avg      | Max | Min |
| --- | --------------- | ----- | ---- | ----- | -------- | --- | --- |
| 2   | Entire home/apt | 225   | 3122 | 21    | 148.6667 | 299 | 80  |
| 30  | Entire home/apt | 180   | 3122 | 21    | 148.6667 | 299 | 80  |
| 28  | Entire home/apt | 150   | 3122 | 21    | 148.6667 | 299 | 80  |
| 38  | Entire home/apt | 85    | 3122 | 21    | 148.6667 | 299 | 80  |
| 25  | Entire home/apt | 120   | 3122 | 21    | 148.6667 | 299 | 80  |
| 42  | Entire home/apt | 120   | 3122 | 21    | 148.6667 | 299 | 80  |
| 21  | Entire home/apt | 299   | 3122 | 21    | 148.6667 | 299 | 80  |
| 20  | Entire home/apt | 190   | 3122 | 21    | 148.6667 | 299 | 80  |
| 19  | Entire home/apt | 99    | 3122 | 21    | 148.6667 | 299 | 80  |
| 17  | Entire home/apt | 215   | 3122 | 21    | 148.6667 | 299 | 80  |
| 16  | Entire home/apt | 140   | 3122 | 21    | 148.6667 | 299 | 80  |
| 15  | Entire home/apt | 120   | 3122 | 21    | 148.6667 | 299 | 80  |
| 46  | Entire home/apt | 150   | 3122 | 21    | 148.6667 | 299 | 80  |
| 11  | Entire home/apt | 135   | 3122 | 21    | 148.6667 | 299 | 80  |
| 10  | Entire home/apt | 150   | 3122 | 21    | 148.6667 | 299 | 80  |
| 48  | Entire home/apt | 110   | 3122 | 21    | 148.6667 | 299 | 80  |
| 49  | Entire home/apt | 115   | 3122 | 21    | 148.6667 | 299 | 80  |
| 6   | Entire home/apt | 200   | 3122 | 21    | 148.6667 | 299 | 80  |
| 45  | Entire home/apt | 150   | 3122 | 21    | 148.6667 | 299 | 80  |
| 5   | Entire home/apt | 80    | 3122 | 21    | 148.6667 | 299 | 80  |
| 4   | Entire home/apt | 89    | 3122 | 21    | 148.6667 | 299 | 80  |
| 41  | Private room    | 68    | 2504 | 28    | 89.4286  | 150 | 35  |
| 34  | Private room    | 50    | 2504 | 28    | 89.4286  | 150 | 35  |
| 35  | Private room    | 70    | 2504 | 28    | 89.4286  | 150 | 35  |
| 50  | Private room    | 80    | 2504 | 28    | 89.4286  | 150 | 35  |
| 36  | Private room    | 89    | 2504 | 28    | 89.4286  | 150 | 35  |
| 1   | Private room    | 149   | 2504 | 28    | 89.4286  | 150 | 35  |
| 37  | Private room    | 35    | 2504 | 28    | 89.4286  | 150 | 35  |
| 39  | Private room    | 150   | 2504 | 28    | 89.4286  | 150 | 35  |
| 47  | Private room    | 130   | 2504 | 28    | 89.4286  | 150 | 35  |
| 43  | Private room    | 120   | 2504 | 28    | 89.4286  | 150 | 35  |
| 44  | Private room    | 135   | 2504 | 28    | 89.4286  | 150 | 35  |
| 22  | Private room    | 130   | 2504 | 28    | 89.4286  | 150 | 35  |
| 3   | Private room    | 150   | 2504 | 28    | 89.4286  | 150 | 35  |
| 7   | Private room    | 60    | 2504 | 28    | 89.4286  | 150 | 35  |
| 8   | Private room    | 79    | 2504 | 28    | 89.4286  | 150 | 35  |
| 9   | Private room    | 79    | 2504 | 28    | 89.4286  | 150 | 35  |
| 12  | Private room    | 85    | 2504 | 28    | 89.4286  | 150 | 35  |
| 13  | Private room    | 89    | 2504 | 28    | 89.4286  | 150 | 35  |
| 14  | Private room    | 85    | 2504 | 28    | 89.4286  | 150 | 35  |
| 18  | Private room    | 140   | 2504 | 28    | 89.4286  | 150 | 35  |
| 18  | Private room    | 140   | 2504 | 28    | 89.4286  | 150 | 35  |
| 33  | Private room    | 55    | 2504 | 28    | 89.4286  | 150 | 35  |
| 23  | Private room    | 80    | 2504 | 28    | 89.4286  | 150 | 35  |
| 24  | Private room    | 110   | 2504 | 28    | 89.4286  | 150 | 35  |
| 26  | Private room    | 60    | 2504 | 28    | 89.4286  | 150 | 35  |
| 27  | Private room    | 80    | 2504 | 28    | 89.4286  | 150 | 35  |
| 29  | Private room    | 44    | 2504 | 28    | 89.4286  | 150 | 35  |
| 31  | Private room    | 50    | 2504 | 28    | 89.4286  | 150 | 35  |
| 32  | Private room    | 52    | 2504 | 28    | 89.4286  | 150 | 35  |
| 40  | Shared room     | 40    | 40   | 1     | 40       | 40  | 40  |

### Rang-Window-Funktionen

Rang-Window-Funktionen sind Funktionen, die für jede Zeile in einem Fenster einen Rang vergeben.

Bei Rangfunktionen ist nach dem Schlüsselwort `OVER` zwingend die Angabe der Bedingung `ORDER BY` erforderlich, nach der die Sortierung für die Rangvergabe erfolgt.

- `ROW_NUMBER` — gibt die Zeilennummer zurück und wird zum Nummerieren verwendet;
- `RANK` — gibt den Rang jeder Zeile zurück. So funktioniert es:
    - Sortierung: Zunächst werden die Zeilen nach einer oder mehreren Spalten sortiert. Diese Spalten werden in der `ORDER BY`-Klausel innerhalb von `OVER` angegeben.
    - Rangvergabe: Jeder eindeutigen Zeile oder Gruppe von Zeilen mit gleichen Werten in den Sortierspalten wird ein Rang zugewiesen. Der Rang beginnt bei 1.
    - Gleiche Werte: Wenn mehrere Zeilen in den Sortierspalten dieselben Werte haben, erhalten sie denselben Rang. Wenn zum Beispiel zwei Zeilen den zweiten Platz belegen, erhalten beide den Rang 2.
    - Überspringen von Rängen: Nach einer Gruppe von Zeilen mit gleichem Rang erhöht sich der nächste Rang um die Anzahl der Zeilen in dieser Gruppe. Wenn zwei Zeilen also den Rang 2 haben, bekommt die nächste Zeile den Rang 4, nicht 3.
    - Fortsetzung der Sortierung: Dieser Vorgang setzt sich fort, bis allen Zeilen in der Ergebnismenge Ränge zugewiesen wurden.
- `DENSE_RANK` — gibt ebenfalls den Rang jeder Zeile zurück. Im Gegensatz zur Funktion `RANK` überspringt sie aber keine Ränge: Nach einer Gruppe gleicher Werte erhöht sich der Rang um eins und nicht um die Anzahl der Zeilen. Wenn zum Beispiel zwei Zeilen den Rang 2 haben, bekommt die nächste Zeile den Rang 3, nicht 4.

```sql
SELECT id,
	home_type,
	price,
	ROW_NUMBER() OVER(PARTITION BY home_type ORDER BY price) AS "row_number",
	RANK() OVER(PARTITION BY home_type ORDER BY price) AS "rank",
	DENSE_RANK() OVER(PARTITION BY home_type ORDER BY price) AS "dense_rank"
FROM Rooms;
```

| id  | home_type       | price | row_number | rank | dense_rank |
| --- | --------------- | ----- | ---------- | ---- | ---------- |
| 5   | Entire home/apt | 80    | 1          | 1    | 1          |
| 38  | Entire home/apt | 85    | 2          | 2    | 2          |
| 4   | Entire home/apt | 89    | 3          | 3    | 3          |
| 19  | Entire home/apt | 99    | 4          | 4    | 4          |
| 48  | Entire home/apt | 110   | 5          | 5    | 5          |
| 49  | Entire home/apt | 115   | 6          | 6    | 6          |
| 25  | Entire home/apt | 120   | 7          | 7    | 7          |
| 15  | Entire home/apt | 120   | 8          | 7    | 7          |
| 42  | Entire home/apt | 120   | 9          | 7    | 7          |
| 11  | Entire home/apt | 135   | 10         | 10   | 8          |
| 16  | Entire home/apt | 140   | 11         | 11   | 9          |
| 28  | Entire home/apt | 150   | 12         | 12   | 10         |
| 10  | Entire home/apt | 150   | 13         | 12   | 10         |
| 45  | Entire home/apt | 150   | 14         | 12   | 10         |
| 46  | Entire home/apt | 150   | 15         | 12   | 10         |
| 30  | Entire home/apt | 180   | 16         | 16   | 11         |
| 20  | Entire home/apt | 190   | 17         | 17   | 12         |
| 6   | Entire home/apt | 200   | 18         | 18   | 13         |
| 17  | Entire home/apt | 215   | 19         | 19   | 14         |
| 2   | Entire home/apt | 225   | 20         | 20   | 15         |
| 21  | Entire home/apt | 299   | 21         | 21   | 16         |
| 37  | Private room    | 35    | 1          | 1    | 1          |
| 29  | Private room    | 44    | 2          | 2    | 2          |
| 34  | Private room    | 50    | 3          | 3    | 3          |
| 31  | Private room    | 50    | 4          | 3    | 3          |
| 32  | Private room    | 52    | 5          | 5    | 4          |
| 33  | Private room    | 55    | 6          | 6    | 5          |
| 26  | Private room    | 60    | 7          | 7    | 6          |
| 7   | Private room    | 60    | 8          | 7    | 6          |
| 41  | Private room    | 68    | 9          | 9    | 7          |
| 35  | Private room    | 70    | 10         | 10   | 8          |
| 8   | Private room    | 79    | 11         | 11   | 9          |
| 9   | Private room    | 79    | 12         | 11   | 9          |
| 27  | Private room    | 80    | 13         | 13   | 10         |
| 23  | Private room    | 80    | 14         | 13   | 10         |
| 50  | Private room    | 80    | 15         | 13   | 10         |
| 12  | Private room    | 85    | 16         | 16   | 11         |
| 14  | Private room    | 85    | 17         | 16   | 11         |
| 13  | Private room    | 89    | 18         | 18   | 12         |
| 36  | Private room    | 89    | 19         | 18   | 12         |
| 24  | Private room    | 110   | 20         | 20   | 13         |
| 43  | Private room    | 120   | 21         | 21   | 14         |
| 22  | Private room    | 130   | 22         | 22   | 15         |
| 47  | Private room    | 130   | 23         | 22   | 15         |
| 44  | Private room    | 135   | 24         | 24   | 16         |
| 18  | Private room    | 140   | 25         | 25   | 17         |
| 1   | Private room    | 149   | 26         | 26   | 18         |
| 3   | Private room    | 150   | 27         | 27   | 19         |
| 39  | Private room    | 150   | 28         | 27   | 19         |
| 40  | Shared room     | 40    | 1          | 1    | 1          |

### Versatz-Window-Funktionen

Versatz-Window-Funktionen sind Funktionen, mit denen man sich innerhalb eines Fensters bewegen und auf andere Zeilen relativ zur aktuellen Zeile zugreifen kann, sowie auf Werte am Anfang oder Ende des Fensters.

- `LAG` — greift auf Daten aus vorherigen Zeilen des Fensters zu.

    Hat drei Argumente: die Spalte, deren Wert zurückgegeben werden soll, die Anzahl der Zeilen für den Versatz (standardmäßig 1) und den Wert, der zurückgegeben werden soll, falls der Versatz `NULL` ergibt.

- `LEAD` — greift auf Daten aus nachfolgenden Zeilen zu. Hat analog zu `LAG` 3 Argumente.

- `FIRST_VALUE` — gibt den ersten Wert im Fenster zurück. Erwartet als Argument die Spalte, deren Wert zurückgegeben werden soll.

- `LAST_VALUE` — gibt den letzten Wert im Fenster zurück. Erwartet als Argument die Spalte, deren Wert zurückgegeben werden soll.

    > Bei Verwendung von `ORDER BY` werden die Fensterränder standardmäßig vom Beginn der Partition bis zur aktuellen Zeile gesetzt (`RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`). Deshalb gibt `LAST_VALUE` den Wert der aktuellen Zeile zurück, nicht den der letzten Zeile der gesamten Partition. Um wirklich den letzten Wert der Partition zu bekommen, musst du die Fenstergrenzen explizit erweitern: `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.

```sql
SELECT id,
	home_type,
	price,
	LAG(price) OVER(PARTITION BY home_type ORDER BY price) AS "lag",
	LAG(price, 2) OVER(PARTITION BY home_type ORDER BY price) AS "lag_2",
	LEAD(price) OVER(PARTITION BY home_type ORDER BY price) AS "lead",
	FIRST_VALUE(price) OVER(PARTITION BY home_type ORDER BY price) AS "first_value",
	LAST_VALUE(price) OVER(PARTITION BY home_type ORDER BY price ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS "last_value"
FROM Rooms;
```

| id  | home_type       | price | lag  | lag_2 | lead | first_value | last_value |
| --- | --------------- | ----- | ---- | ----- | ---- | ----------- | ---------- |
| 5   | Entire home/apt | 80    | null | null  | 85   | 80          | 299        |
| 38  | Entire home/apt | 85    | 80   | null  | 89   | 80          | 299        |
| 4   | Entire home/apt | 89    | 85   | 80    | 99   | 80          | 299        |
| 19  | Entire home/apt | 99    | 89   | 85    | 110  | 80          | 299        |
| 48  | Entire home/apt | 110   | 99   | 89    | 115  | 80          | 299        |
| 49  | Entire home/apt | 115   | 110  | 99    | 120  | 80          | 299        |
| 25  | Entire home/apt | 120   | 115  | 110   | 120  | 80          | 299        |
| 15  | Entire home/apt | 120   | 120  | 115   | 120  | 80          | 299        |
| 42  | Entire home/apt | 120   | 120  | 120   | 135  | 80          | 299        |
| 11  | Entire home/apt | 135   | 120  | 120   | 140  | 80          | 299        |
| 16  | Entire home/apt | 140   | 135  | 120   | 150  | 80          | 299        |
| 28  | Entire home/apt | 150   | 140  | 135   | 150  | 80          | 299        |
| 10  | Entire home/apt | 150   | 150  | 140   | 150  | 80          | 299        |
| 45  | Entire home/apt | 150   | 150  | 150   | 150  | 80          | 299        |
| 46  | Entire home/apt | 150   | 150  | 150   | 180  | 80          | 299        |
| 30  | Entire home/apt | 180   | 150  | 150   | 190  | 80          | 299        |
| 20  | Entire home/apt | 190   | 180  | 150   | 200  | 80          | 299        |
| 6   | Entire home/apt | 200   | 190  | 180   | 215  | 80          | 299        |
| 17  | Entire home/apt | 215   | 200  | 190   | 225  | 80          | 299        |
| 2   | Entire home/apt | 225   | 215  | 200   | 299  | 80          | 299        |
| 21  | Entire home/apt | 299   | 225  | 215   | null | 80          | 299        |
| 37  | Private room    | 35    | null | null  | 44   | 35          | 150        |
| 29  | Private room    | 44    | 35   | null  | 50   | 35          | 150        |
| 34  | Private room    | 50    | 44   | 35    | 50   | 35          | 150        |
| 31  | Private room    | 50    | 50   | 44    | 52   | 35          | 150        |
| 32  | Private room    | 52    | 50   | 50    | 55   | 35          | 150        |
| 33  | Private room    | 55    | 52   | 50    | 60   | 35          | 150        |
| 26  | Private room    | 60    | 55   | 52    | 60   | 35          | 150        |
| 7   | Private room    | 60    | 60   | 55    | 68   | 35          | 150        |
| 41  | Private room    | 68    | 60   | 60    | 70   | 35          | 150        |
| 35  | Private room    | 70    | 68   | 60    | 79   | 35          | 150        |
| 8   | Private room    | 79    | 70   | 68    | 79   | 35          | 150        |
| 9   | Private room    | 79    | 79   | 70    | 80   | 35          | 150        |
| 27  | Private room    | 80    | 79   | 79    | 80   | 35          | 150        |
| 23  | Private room    | 80    | 80   | 79    | 80   | 35          | 150        |
| 50  | Private room    | 80    | 80   | 80    | 85   | 35          | 150        |
| 12  | Private room    | 85    | 80   | 80    | 85   | 35          | 150        |
| 14  | Private room    | 85    | 85   | 80    | 89   | 35          | 150        |
| 13  | Private room    | 89    | 85   | 85    | 89   | 35          | 150        |
| 36  | Private room    | 89    | 89   | 85    | 110  | 35          | 150        |
| 24  | Private room    | 110   | 89   | 89    | 120  | 35          | 150        |
| 43  | Private room    | 120   | 110  | 89    | 130  | 35          | 150        |
| 22  | Private room    | 130   | 120  | 110   | 130  | 35          | 150        |
| 47  | Private room    | 130   | 130  | 120   | 135  | 35          | 150        |
| 44  | Private room    | 135   | 130  | 130   | 140  | 35          | 150        |
| 18  | Private room    | 140   | 135  | 130   | 149  | 35          | 150        |
| 1   | Private room    | 149   | 140  | 135   | 150  | 35          | 150        |
| 3   | Private room    | 150   | 149  | 140   | 150  | 35          | 150        |
| 39  | Private room    | 150   | 150  | 149   | null | 35          | 150        |
| 40  | Shared room     | 40    | null | null  | null | 40          | 40         |
