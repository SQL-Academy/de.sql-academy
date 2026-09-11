---
meta:
    title: "Grundsyntax einer SQL-Query"
    description: "Beispiele einfacher SQL-Queries an die Datenbank. Verwendung von Aliassen in SQL-Queries."
---

# Grundsyntax einer SQL-Query

Eine der wichtigsten Aufgaben von SQL ist es, Daten aus einem DBMS auszulesen.
Dafür gibt es in SQL das `SELECT`-Statement. Schauen wir uns ein paar einfache Beispiele damit an.

## Beliebige Werte ausgeben

Zunächst ist wichtig zu verstehen, dass du mit `SELECT` nicht nur Daten aus Tabellen ausgeben kannst,
sondern auch beliebige Strings, Zahlen, Datumswerte usw. So lässt sich zum Beispiel ein beliebiger String ausgeben:

```sql
SELECT 'Hello world'
```

## Alle Daten einer Tabelle ausgeben

Um alle Felder einer bestimmten Tabelle auszugeben, nutzt man das Symbol `*`. Werfen wir einen Blick auf das Datenbankschema
und geben die Daten einer Tabelle aus.

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/basic-syntax-sql-query).

```sql
SELECT * FROM FamilyMembers
```

| member_id | status   | member_name       | birthday                 |
| --------- | -------- | ----------------- | ------------------------ |
| 1         | father   | Headley Quincey   | 1960-05-13T00:00:00.000Z |
| 2         | mother   | Flavia Quincey    | 1963-02-16T00:00:00.000Z |
| 3         | son      | Andie Quincey     | 1983-06-05T00:00:00.000Z |
| 4         | daughter | Lela Quincey      | 1985-06-07T00:00:00.000Z |
| 5         | daughter | Annie Quincey     | 1988-04-10T00:00:00.000Z |
| 6         | father   | Ernest Forrest    | 1961-09-11T00:00:00.000Z |
| 7         | mother   | Constance Forrest | 1968-09-06T00:00:00.000Z |

## Daten aus bestimmten Spalten der Tabelle ausgeben

Wenn du nur Informationen aus bestimmten Spalten brauchst und nicht aus allen auf einmal,
kannst du die Spaltennamen einfach durch Kommas getrennt aufzählen:

```sql
SELECT member_id, member_name FROM FamilyMembers
```

| member_id | member_name       |
| --------- | ----------------- |
| 1         | Headley Quincey   |
| 2         | Flavia Quincey    |
| 3         | Andie Quincey     |
| 4         | Lela Quincey      |
| 5         | Annie Quincey     |
| 6         | Ernest Forrest    |
| 7         | Constance Forrest |

## Aliasse

Wenn wir bestimmte Spalten ausgeben wollen, sie aber im Ergebnis anders heißen sollen,
können wir Aliasse verwenden.

Die Syntax ist ziemlich einfach: Dazu nutzt man das Schlüsselwort `AS`. So wie im Beispiel unten:

```sql
SELECT member_id, member_name AS Name FROM FamilyMembers
```

| member_id | Name              |
| --------- | ----------------- |
| 1         | Headley Quincey   |
| 2         | Flavia Quincey    |
| 3         | Andie Quincey     |
| 4         | Lela Quincey      |
| 5         | Annie Quincey     |
| 6         | Ernest Forrest    |
| 7         | Constance Forrest |

Du kannst aber auch ganz auf `AS` verzichten und den gewünschten Feldnamen einfach durch ein Leerzeichen getrennt schreiben.

```sql
SELECT member_id, member_name Name FROM FamilyMembers
```

> Aliasse dürfen bis zu 255 Zeichen lang sein (inklusive Leerzeichen, Ziffern und Sonderzeichen).

## Eigenständige Übungen

Das ist unsere erste Lektion im Praxismodul. Bisher gab es nur theoretische Lektionen, um mögliche Lücken im Wissen über relationale Datenbanken zu schließen.
Nach jeder Praxislektion bieten wir dir eine Reihe von Aufgaben zur eigenständigen Bearbeitung, damit du das Gelernte direkt festigen kannst.

Falls du das Modul „Einführung" und insbesondere den Artikel <a href="https://sql-academy.org/de/guide/intro-structure-of-course" target="_blank"> „Aufbau des Kurses" </a> übersprungen hast, in dem das Prinzip und die Oberfläche der „Eigenständigen Übungen" beschrieben sind,
empfehlen wir, <a href="https://sql-academy.org/de/guide/intro-structure-of-course" target="_blank"> dorthin zurückzukehren </a>.
