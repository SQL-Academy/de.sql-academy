---
meta:
    title: "Sortierung in SQL, der ORDER BY-Operator"
    description: "Der SQL-Operator ORDER BY, Sortierung nach mehreren Spalten, Beispiele zur Verwendung."
---

# Sortierung, der ORDER BY-Operator

Bei einer `SELECT`-Query werden Zeilen standardmäßig in einer
nicht festgelegten Reihenfolge zurückgegeben. Die tatsächliche Reihenfolge hängt vom
Join- und Scan-Plan sowie von der Anordnung der Daten auf der Festplatte ab — verlassen darfst du dich
darauf also nicht. Für die geordnete Ausgabe nutzt du die Konstruktion `ORDER BY`.

## Allgemeine Struktur einer Query mit ORDER BY

```sql
SELECT tabellen_felder FROM tabellen_name
WHERE ...
ORDER BY spalte_1 [ASC | DESC][, spalte_n [ASC | DESC]]
```

Dabei steht `ASC` bzw. `DESC` für die Sortierrichtung:

- `ASC` — aufsteigend (Standard)
- `DESC` — absteigend

Holen wir uns zum Beispiel die Namen der Airlines aus der Tabelle `Company` in alphabetischer Reihenfolge:

```sql
SELECT name FROM Company ORDER BY name;
```

**MySQL**

| name       |
| ---------- |
| Aeroflot   |
| air_France |
| British_AW |
| Dale_avia  |
| Don_avia   |

**PostgreSQL**

| name       |
| ---------- |
| Aeroflot   |
| British_AW |
| Dale_avia  |
| Don_avia   |
| air_France |

## Auf- und absteigende Sortierung für die wichtigsten Datentypen

**MySQL**

| Datentyp       | ASC (aufsteigend)                                                                                                                                  | DESC (absteigend)                                                                                                         |
| :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Stringtyp      | Lexikografische (alphabetische) Reihenfolge von „A“ bis „Z“ (bzw. von „А“ bis „Я“) <br /> <br /> Zuerst kommen Einträge mit „a“, dann mit „b“ usw. | Lexikografische Reihenfolge von „Z“ bis „A“ (bzw. von „Я“ bis „А“) <br /> <br /> Zuerst kommen Einträge mit „z“, „y“ usw. |
| Zahlentyp      | Vom kleineren zum größeren Wert                                                                                                                    | Vom größeren zum kleineren Wert                                                                                           |
| Datum und Zeit | Vom früheren Datum/Zeit zum späteren <br /> <br /> Zum Beispiel zuerst `01.01.2024`, dann `01.02.2024`                                             | Vom späteren Datum/Zeit zum früheren <br /> <br /> Zum Beispiel zuerst `01.02.2024`, dann `01.01.2024`                    |
| Boolescher Typ | `False` steht vor `True`                                                                                                                           | `True` steht vor `False`                                                                                                  |
| NULL-Werte     | `NULL`-Werte stehen zuerst                                                                                                                         | `NULL`-Werte stehen am Ende                                                                                               |

**PostgreSQL**

| Datentyp       | ASC (aufsteigend)                                                                                                                                  | DESC (absteigend)                                                                                                         |
| :------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Stringtyp      | Lexikografische (alphabetische) Reihenfolge von „A“ bis „Z“ (bzw. von „А“ bis „Я“) <br /> <br /> Zuerst kommen Einträge mit „a“, dann mit „b“ usw. | Lexikografische Reihenfolge von „Z“ bis „A“ (bzw. von „Я“ bis „А“) <br /> <br /> Zuerst kommen Einträge mit „z“, „y“ usw. |
| Zahlentyp      | Vom kleineren zum größeren Wert                                                                                                                    | Vom größeren zum kleineren Wert                                                                                           |
| Datum und Zeit | Vom früheren Datum/Zeit zum späteren <br /> <br /> Zum Beispiel zuerst `01.01.2024`, dann `01.02.2024`                                             | Vom späteren Datum/Zeit zum früheren <br /> <br /> Zum Beispiel zuerst `01.02.2024`, dann `01.01.2024`                    |
| Boolescher Typ | `false` steht vor `true`                                                                                                                           | `true` steht vor `false`                                                                                                  |
| NULL-Werte     | `NULL`-Werte stehen am Ende                                                                                                                        | `NULL`-Werte stehen zuerst                                                                                                |

## Sortierung nach mehreren Spalten

Um nach zwei oder mehr Spalten zu sortieren, listest du sie durch Kommas getrennt auf.

```sql
...ORDER BY spalte_1 [ASC | DESC], spalte_2 [ASC | DESC];
```

Die Daten werden nach der ersten Spalte sortiert. Treffen mehrere Zeilen mit demselben Wert in der ersten Spalte aufeinander,
werden sie nach der zweiten Spalte sortiert. Die Anzahl der Spalten, nach denen du sortieren kannst, ist nicht begrenzt.

> Die Sortierrichtung gilt nur für die unmittelbar davor stehende Spalte.
>
> `ORDER BY spalte_1, spalte_2 DESC`
>
> ist nicht dasselbe wie
>
> `ORDER BY spalte_1 DESC, spalte_2 DESC`

Holen wir die Flugdaten aus der Tabelle `Trip`, sortiert nach Abflugstadt aufsteigend und nach Ankunftsstadt absteigend:

```sql
SELECT DISTINCT town_from, town_to FROM Trip
ORDER BY town_from, town_to DESC;
```

| town_from   | town_to     |
| ----------- | ----------- |
| London      | Singapore   |
| London      | Paris       |
| Moscow      | Rostov      |
| Paris       | Rostov      |
| Rostov      | Vladivostok |
| Rostov      | Paris       |
| Rostov      | Moscow      |
| Singapore   | London      |
| Vladivostok | Rostov      |

In diesem Beispiel werden die Einträge zuerst nach dem Feld `town_from` sortiert. Anschließend wird innerhalb der Gruppen mit gleichem `town_from` die absteigende Sortierung nach `town_to` angewendet.

## Sortierung in Aktion

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/sorting).
