---
meta:
    title: "Daten aktualisieren, der UPDATE-Operator"
    description: "Datensätze in SQL bearbeiten (aktualisieren). Daten in relationalen Datenbanken ändern."
---

# Daten aktualisieren, der UPDATE-Operator

Um vorhandene Datensätze in Tabellen zu ändern, gibt es den SQL-Operator `UPDATE`.

## Allgemeine Struktur einer Query mit UPDATE

```sql
UPDATE tabellen_name
SET tabellen_feld1 = wert_des_tabellen_felds1,
    tabellen_feldN = wert_des_tabellen_feldsN
[WHERE auswahl_bedingung]
```

Wenn du zum Beispiel den Namen ändern möchtest, sieht die Query so aus:

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-update).

```sql
UPDATE FamilyMembers
SET member_name = "Andie Anthony"
WHERE member_name = "Andie Quincey";
```

| member_id | status      | member_name       | birthday                 |
| --------- | ----------- | ----------------- | ------------------------ |
| 1         | father      | Headley Quincey   | 1960-05-13T00:00:00.000Z |
| 2         | mother      | Flavia Quincey    | 1963-02-16T00:00:00.000Z |
| 3         | varchar(50) | Andie Anthony     | 1983-06-05T00:00:00.000Z |
| 4         | daughter    | Lela Quincey      | 1985-06-07T00:00:00.000Z |
| 5         | daughter    | Annie Quincey     | 1988-04-10T00:00:00.000Z |
| 6         | father      | Ernest Forrest    | 1961-09-11T00:00:00.000Z |
| 7         | mother      | Constance Forrest | 1968-09-06T00:00:00.000Z |
| 8         | daughter    | Wednesday Addams  | 2005-01-13T00:00:00.000Z |

> Sei vorsichtig beim Aktualisieren von Daten. Wenn du den `WHERE`-Operator weglässt, werden alle Datensätze in der Tabelle aktualisiert.

## Berechnete Werte

In Update-Queries kannst du Werte ausgehend vom vorherigen Wert ändern.

```sql
UPDATE Payments
SET unit_price = unit_price * 2;
```

Es ist außerdem erlaubt, Werte einer Spalte in eine andere Spalte zu schreiben. Voraussetzung ist natürlich, dass die Datentypen kompatibel sind.
