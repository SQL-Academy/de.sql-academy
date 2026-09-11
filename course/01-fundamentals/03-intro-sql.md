---
meta:
    title: "Einführung in SQL"
    description: "Definition der Sprache SQL. Dialekte (Erweiterungen) von SQL. Unterschiede zwischen T-SQL, PL/SQL und PL/pgSQL."
---

# Einführung in SQL

> SQL – Structured Query Language – ist eine Sprache, mit der sich Daten effizient speichern,
> in Teilen abfragen, aktualisieren, auslesen und aus einer Datenbank löschen lassen.

Mit relationalen Datenbankmanagementsystemen kommunizierst du genau über SQL. Damit erledigst du alle grundlegenden Operationen mit Datenbanken:

**Daten**

- `SELECT` — Daten aus einer Datenbank auslesen
- `INSERT` — Datensätze in eine Datenbank einfügen
- `UPDATE` — Datensätze in einer Datenbank aktualisieren
- `DELETE` — Datensätze aus einer Datenbank löschen

**Struktur**

- `CREATE DATABASE` — Neue Datenbanken anlegen
- `CREATE TABLE` — Neue Tabellen in einer Datenbank anlegen

**Logik und Zugriff**

- `CREATE PROCEDURE` — Stored Procedures anlegen
- `CREATE VIEW` — Views (Sichten) anlegen
- `GRANT` — Berechtigungen für Tabellen, Prozeduren und Views vergeben

## SQL-Dialekte (SQL-Erweiterungen)

SQL ist eine universelle Sprache für alle relationalen Datenbankmanagementsysteme, aber viele DBMS ergänzen oder
verändern die Sprache und weichen so vom Standard ab. Solche Varianten heißen Dialekte oder Spracherweiterungen.

Einige davon sind unten aufgeführt. Schauen wir uns als Beispiel an, wie in diesen DBMS jeweils die ersten 3 Zeilen ausgewählt werden.

| Dialekt  | DBMS                 | Beispiel            |
| -------- | -------------------- | ------------------- |
| T-SQL    | Microsoft SQL Server | `SELECT TOP 3 name` |
| PL/SQL   | Oracle Database      | `WHERE ROWNUM <= 3` |
| MySQL    | MySQL                | `name LIMIT 3`      |
| PL/pgSQL | PostgreSQL           | `name LIMIT 3`      |

### Welchen Dialekt solltest du lernen?

Wenn du nur weisst, dass du SQL lernen willst, fang mit Standard-SQL an.
Weisst du dagegen schon, mit welcher Datenbank du arbeiten wirst, ist es meistens besser, direkt deren Dialekt zu lernen und im Hinterkopf zu behalten,
dass unterschiedliche Datenbanken eine leicht abweichende Syntax verwenden können.

In unserem Kurs hast du die Wahl zwischen MySQL und PostgreSQL – den beiden beliebtesten Lösungen.
Wähle gleich jetzt eine davon aus – alle Beispiele und Aufgaben im Kurs passen sich deiner Wahl an:

**MySQL**

- Der Web-Klassiker: leicht zu starten, überall im Einsatz
- Das am weitesten verbreitete Open-Source-DBMS der Welt
- Treibt WordPress, YouTube und Booking.com an

**PostgreSQL**

- Kraft und Präzision: der Favorit der Start-ups
- Reiche Syntax und strikte Einhaltung des SQL-Standards
- Instagram, Spotify und Reddit setzen darauf

Du kannst das DBMS jederzeit wechseln — direkt hier, im Zahnrad-Menü in der Kopfzeile der Seite oder im Code-Editor.
