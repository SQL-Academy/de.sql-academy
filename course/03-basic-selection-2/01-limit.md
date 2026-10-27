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
SELECT tabellen_felder
FROM tabellen_name
LIMIT [anzahl_uebersprungen,] anzahl_zeilen;
```

- `tabellen_felder` — Die Spalten, die im Ergebnis landen.
- `tabellen_name` — Die Tabelle, aus der die Daten ausgewählt werden.
- `anzahl_uebersprungen` (optional) — Wie viele Zeilen vor der Ausgabe übersprungen werden. Lässt du es weg, beginnt die
  Ausgabe mit der ersten Zeile.
- `anzahl_zeilen` — Wie viele Zeilen ausgegeben werden. Hat das Ergebnis weniger Zeilen, kommen alle.

**Syntax mit OFFSET:**

```sql
SELECT tabellen_felder
FROM tabellen_name
LIMIT anzahl_zeilen [OFFSET anzahl_uebersprungen];
```

- `tabellen_felder` — Die Spalten, die im Ergebnis landen.
- `tabellen_name` — Die Tabelle, aus der die Daten ausgewählt werden.
- `anzahl_zeilen` — Wie viele Zeilen ausgegeben werden. Hat das Ergebnis weniger Zeilen, kommen alle.
- `anzahl_uebersprungen` (optional) — Wie viele Zeilen vor der Ausgabe übersprungen werden. Lässt du es weg, beginnt die
  Ausgabe mit der ersten Zeile.

**PostgreSQL**

```sql
SELECT tabellen_felder
FROM tabellen_name
LIMIT anzahl_zeilen [OFFSET anzahl_uebersprungen];
```

- `tabellen_felder` — Die Spalten, die im Ergebnis landen.
- `tabellen_name` — Die Tabelle, aus der die Daten ausgewählt werden.
- `anzahl_zeilen` — Wie viele Zeilen ausgegeben werden. Hat das Ergebnis weniger Zeilen, kommen alle.
- `anzahl_uebersprungen` (optional) — Wie viele Zeilen vor der Ausgabe übersprungen werden. Lässt du es weg, beginnt die
  Ausgabe mit der ersten Zeile.

## Anwendungsbeispiel

Nehmen wir die Tabelle `Company`:

| id  | name       |
| --- | ---------- |
| 1   | Don_avia   |
| 2   | Aeroflot   |
| 3   | Dale_avia  |
| 4   | Air_France |
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
| 4   | Air_France |
| 5   | British_AW |

In dieser Query werden die ersten zwei Zeilen der Tabelle (1, 2) übersprungen, danach werden die nächsten drei Datensätze (3, 4, 5) ausgegeben.

Und jetzt probier es selbst aus.

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/limit).
