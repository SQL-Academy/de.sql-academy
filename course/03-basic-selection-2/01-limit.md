---
meta:
    title: "Ergebnis begrenzen: der LIMIT-Operator"
    description: "Beschreibung und Syntax des SQL-Operators LIMIT zum Begrenzen der Anzahl an Datensätzen, Anwendungsbeispiele und Übungen zur Selbstkontrolle."
---

# Ergebnis begrenzen: der LIMIT-Operator

Mit `LIMIT` holst du einen bestimmten Ausschnitt von Datensätzen aus einer oder mehreren Tabellen.

## Allgemeine Struktur einer Query mit LIMIT

**MySQL**

**Syntax mit Komma:**

```sql
SELECT auswahl_felder
FROM tabellen_liste
LIMIT [anzahl_uebersprungener_datensaetze,] anzahl_datensaetze_zur_ausgabe;
```

**Syntax mit OFFSET:**

```sql
SELECT auswahl_felder
FROM tabellen_liste
LIMIT anzahl_datensaetze_zur_ausgabe [OFFSET anzahl_uebersprungener_datensaetze];
```

Wenn du die Anzahl übersprungener Datensätze weglässt, wird ab dem Tabellenanfang gezählt.

**PostgreSQL**

```sql
SELECT auswahl_felder
FROM tabellen_liste
LIMIT anzahl_datensaetze_zur_ausgabe [OFFSET anzahl_uebersprungener_datensaetze];
```

Ohne `OFFSET` wird ab dem Tabellenanfang gezählt.

## Anwendungsbeispiel

Nehmen wir die Tabelle `Company`:

| id  | name       |
| --- | ---------- |
| 1   | Don_avia   |
| 2   | Aeroflot   |
| 3   | Dale_avia  |
| 4   | air_France |
| 5   | British_AW |

Um die Zeilen 3 bis 5 auszugeben, brauchst du diese Query:

**MySQL**

```sql
SELECT * FROM Company LIMIT 2, 3;
```

Oder, was dasselbe bewirkt:

```sql
SELECT * FROM Company LIMIT 3 OFFSET 2;
```

**PostgreSQL**

```sql
SELECT * FROM Company LIMIT 3 OFFSET 2;
```

Als Ergebnis kommt folgende Auswahl zurück:

| id  | name       |
| --- | ---------- |
| 3   | Dale_avia  |
| 4   | air_France |
| 5   | British_AW |

In dieser Query werden die ersten zwei Zeilen der Tabelle (1, 2) übersprungen, danach werden die nächsten drei Datensätze (3, 4, 5) ausgegeben.

Und jetzt probier es selbst aus.

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/limit).
