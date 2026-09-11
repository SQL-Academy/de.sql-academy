---
meta:
    title: "Duplikate entfernen, der DISTINCT-Operator"
    description: "Beispiele, wozu der SQL-Operator DISTINCT gut ist – Duplikate in SQL entfernen für eine oder mehrere Spalten."
---

# Duplikate entfernen, DISTINCT

In manchen Situationen liefert eine SQL-Query Zeilen, die sich wiederholen.

Geben wir zum Beispiel das Feld `class` aus der Tabelle `Student_in_class` einer Datenbank aus, in der der Schulstundenplan organisiert ist.

ER-Diagramm der Datenbank Schedule: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/distinct-operator).

```sql
SELECT class FROM Student_in_class;
```

| class |
| ----- |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 9     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 8     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 6     |
| 5     |
| 5     |
| 5     |
| 5     |
| 5     |
| 5     |
| 5     |
| 5     |
| 4     |
| 4     |
| 4     |
| 4     |
| 4     |
| 4     |
| 4     |
| 4     |
| 4     |
| 3     |
| 3     |
| 3     |
| 3     |
| 3     |
| 3     |
| 3     |
| 3     |
| 2     |
| 2     |
| 2     |
| 2     |
| 2     |
| 2     |
| 2     |
| 1     |
| 1     |
| 1     |
| 1     |
| 1     |
| 1     |
| 1     |

Da in einer Klasse mehrere Schüler sein können, ist es nicht überraschend, dass im Ergebnis identische Werte auftauchen.
Um solche Duplikate beim Abfragen zu vermeiden, gibt es den `DISTINCT`-Operator.

## Syntax des Operators

```sql
SELECT [DISTINCT] tabellen_felder FROM tabellen_name;
```

> Eckige Klammern in der Syntaxbeschreibung markieren einen optionalen Teil der Query. In der SQL-Query selbst werden sie nicht geschrieben.

In unserem Fall sieht die Query für alle einzigartigen Klassen, in denen mindestens ein Schüler ist, also so aus:

```sql
SELECT DISTINCT class FROM Student_in_class;
```

| class |
| ----- |
| 9     |
| 8     |
| 7     |
| 6     |
| 5     |
| 4     |
| 3     |
| 2     |
| 1     |

## DISTINCT für mehrere Spalten

Verwendest du `DISTINCT` für zwei oder mehr Spalten, werden Datensätze entfernt, die in allen angegebenen Feldern identisch sind.

Für so eine Tabelle

| first_name | last_name |
| ---------- | --------- |
| John       | Scott     |
| William    | Dawson    |
| Raul       | Hartman   |
| William    | Hartman   |
| John       | Scott     |
| John       | Hartman   |

würde die Query mit `DISTINCT` alle Kombinationen aus Vor- und Nachnamen zurückgeben – außer dem doppelten „John Scott".

```sql
SELECT DISTINCT first_name, last_name FROM User;
```

| first_name | last_name |
| ---------- | --------- |
| John       | Scott     |
| William    | Dawson    |
| Raul       | Hartman   |
| William    | Hartman   |
| John       | Hartman   |
