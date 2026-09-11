---
meta:
    title: "Views in SQL: MySQL und PostgreSQL"
    description: "Syntax zum Erstellen von Views in MySQL und PostgreSQL, Beschreibung der Funktionsweise von Views und wofür sie nützlich sind."
---

# Views

Gut entworfene Anwendungen stellen in der Regel eine offene Schnittstelle bereit und verbergen die Implementierungsdetails, sodass Änderungen am Design vorgenommen werden können, ohne die Endnutzer zu beeinträchtigen.

Beim Entwurf deiner Datenbank kannst du ein ähnliches Ergebnis erzielen, indem du die Tabellen abschirmst und den Zugriff auf die Daten nur über eine Reihe von Views erlaubst.

In diesem Artikel schauen wir uns an, was Views sind, wie sie erstellt werden und wofür sie nützlich sein können.

## Was ist eine View

> Eine View ist ein Datenbankobjekt, das das Ergebnis einer per `SELECT` definierten Abfrage zum Zeitpunkt des Zugriffs auf die View darstellt.

Views werden manchmal als „virtuelle Tabellen“ bezeichnet. Das liegt daran, dass eine View für den Nutzer wie eine Tabelle aussieht, tatsächlich aber keine Daten speichert, sondern sie zum Zeitpunkt des Zugriffs aus anderen Tabellen holt.

Ändern sich die Daten in der zugrundeliegenden Tabelle, bekommt der Nutzer beim Zugriff auf die View, die diese Tabelle verwendet, die aktuellen Daten. Views cachen die Ergebnisse der Tabellenabfrage während ihrer Arbeit nicht.

## Beispiel für das Erstellen einer View

Als einfaches Beispiel: Nehmen wir an, du möchtest die E-Mail-Adressen in der Nutzertabelle (`Users`) teilweise verbergen.

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/view).

Das kann zum Beispiel nützlich sein, wenn die Richtlinien deines Unternehmens es nicht allen erlauben, vertrauliche Informationen über Nutzer zu verwenden. Anstatt also direkten Zugriff auf die Tabelle `Users` zu erlauben, definierst du eine View mit dem Namen `ViewUsers` und verlangst, dass alle sie für den Zugriff auf die Nutzerdaten verwenden.

**MySQL**

Hier ist ein Beispiel für die Definition dieser View:

```sql
CREATE VIEW ViewUsers AS
    SELECT id,
           name,
           CONCAT(SUBSTR(email, 1, 2), '****', SUBSTR(email, -4)) AS email
FROM Users;
```

**PostgreSQL**

Hier ist ein Beispiel für die Definition dieser View:

```sql
CREATE VIEW ViewUsers AS
    SELECT id,
           name,
           CONCAT(SUBSTR(email, 1, 2), '****', RIGHT(email, 4)) AS email
FROM Users;
```

Eine View sieht in einer SQL-Abfrage aus wie eine normale Tabelle und wird auch so verwendet:

```sql
SELECT * FROM ViewUsers;
```

| id  | name                 | email          |
| --- | -------------------- | -------------- |
| 1   | Bruce Willis         | ba\*\*\*\*.com |
| 2   | George Clooney       | te\*\*\*\*.com |
| 3   | Kevin Costner        | me\*\*\*\*.com |
| 4   | Donald Sutherland    | ra\*\*\*\*.net |
| 5   | Jennifer Lopez       | ba\*\*\*\*.com |
| 6   | Ray Liotta           | jd\*\*\*\*.net |
| 7   | Samuel L. Jackson    | mo\*\*\*\*.com |
| 8   | Nikole Kidman        | ok\*\*\*\*.com |
| 9   | Alan Rickman         | ka\*\*\*\*.net |
| 10  | Kurt Russell         | ga\*\*\*\*.com |
| 11  | Harrison Ford        | ko\*\*\*\*.com |
| 12  | Russell Crowe        | gl\*\*\*\*.net |
| 13  | Steve Martin         | ne\*\*\*\*.com |
| 14  | Michael Caine        | dm\*\*\*\*.com |
| 15  | Angelina Jolie       | ch\*\*\*\*.net |
| 16  | Mel Gibson           | ro\*\*\*\*o.ca |
| 17  | Michael Douglas      | ti\*\*\*\*.com |
| 18  | John Travolta        | wa\*\*\*\*.com |
| 19  | Sylvester Stallone   | ko\*\*\*\*.com |
| 20  | Tommy Lee Jones      | sz\*\*\*\*.com |
| 21  | Catherine Zeta-Jones | fl\*\*\*\*.com |
| 22  | Antonio Banderas     | sa\*\*\*\*.net |
| 23  | Kim Basinger         | ja\*\*\*\*.net |
| 24  | Sam Neill            | cl\*\*\*\*.com |
| 25  | Hideo Kojima         | ad\*\*\*\*.org |
| 26  | ClINT Eastwood       | fr\*\*\*\*.net |
| 27  | Brad Pitt            | ke\*\*\*\*.net |
| 28  | Johnny Depp          | cg\*\*\*\*o.ca |
| 29  | Pierce Brosnan       | tr\*\*\*\*.com |
| 30  | Sean Connery         | js\*\*\*\*.com |
| 31  | Bruce Willis         | ke\*\*\*\*.com |
| 32  | Mullah Omar          | jg\*\*\*\*.com |
| 33  | Vasanta Roberta      | ro\*\*\*\*.com |

**MySQL**

Wenn du wissen willst, welche Spalten in der View verfügbar sind, kannst du den Befehl `DESCRIBE` nutzen:

```sql
DESCRIBE ViewUsers;
```

| Field | Type        | Null | Key | Default | Extra |
| ----- | ----------- | ---- | --- | ------- | ----- |
| id    | int         | NO   |     | \<NULL> |       |
| name  | varchar(32) | NO   |     | \<NULL> |       |
| email | varchar(38) | YES  |     | \<NULL> |       |

**PostgreSQL**

Wenn du wissen willst, welche Spalten in der View verfügbar sind, kannst du eine Abfrage auf `information_schema` ausführen:

```sql
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'viewusers';
```

## Allgemeine Syntax einer View

**MySQL**

```sql
CREATE [OR REPLACE]
VIEW view_name [(view_spalten_namen)]
AS select_ausdruck
```

`OR REPLACE` — wird dieser optionale Parameter verwendet und existiert bereits eine View mit diesem Namen, wird die alte View gelöscht und eine neue erstellt. Andernfalls tritt beim Versuch, eine View mit einem bereits existierenden Namen anzulegen, ein Fehler auf.

**PostgreSQL**

```sql
CREATE [OR REPLACE] VIEW view_name [(view_spalten_namen)]
AS select_ausdruck
```

`OR REPLACE` — wird dieser optionale Parameter verwendet und existiert bereits eine View mit diesem Namen, wird die alte View gelöscht und eine neue erstellt. Andernfalls tritt beim Versuch, eine View mit einem bereits existierenden Namen anzulegen, ein Fehler auf.

## Wofür Views nützlich sind

### Vereinfachung komplexer Abfragen

Views werden eingesetzt, um komplexe Abfragen zu vereinfachen und eine Abstraktion zwischen Nutzer und Datenbank zu schaffen. Sie können die Komplexität der Datenstruktur verbergen und eine vereinfachte Schnittstelle für den Datenzugriff bereitstellen.

### Verbesserung der Performance

**MySQL**

Das Erstellen von Views, die komplexe Abfragen kapseln, kann dabei helfen, die Ausführung dieser Abfragen zu optimieren. Das kann zu einer schnelleren Ausführung der Abfragen und einer Verbesserung der Gesamt-Performance der Datenbank führen.

**PostgreSQL**

Das Erstellen von Views, die komplexe Abfragen kapseln, kann dabei helfen, die Ausführung dieser Abfragen zu optimieren. PostgreSQL unterstützt materialisierte Views (`MATERIALIZED VIEW`), die die Ergebnisse einer Abfrage physisch speichern und regelmäßig aktualisiert werden, was die Performance bei komplexen Abfragen erheblich verbessern kann.

### Sicherstellung der Sicherheit

Views können verwendet werden, um die Sicherheit vertraulicher Daten zu gewährleisten. Indem man Views erstellt, die den Zugriff auf bestimmte Spalten oder Zeilen einschränken, können Administratoren den Zugriff auf sensible Informationen begrenzen. Das hilft sicherzustellen, dass nur autorisierte Nutzer Zugriff auf vertrauliche Daten haben.

## Fazit

Views sind ein wichtiges Werkzeug in SQL, mit dem sich komplexe Abfragen vereinfachen, der Datenzugriff standardisieren, die Performance verbessern und die Datensicherheit gewährleisten lassen.

Lass uns prüfen, wie gut du das Thema verstanden hast: Wähle die richtige Aussage zur Frage „Was ist eine View in der Datenbank?“

1. **Richtige Antwort:** Eine virtuelle Tabelle, die keine Daten speichert, sondern sie beim Zugriff aus anderen Tabellen holt. — Eine View in der Datenbank ist tatsächlich eine virtuelle Tabelle, die keine eigenen Daten speichert, sondern sie beim Zugriff aus anderen Tabellen holt.

2. Eine physische Tabelle, die Daten dauerhaft speichert. — Eine View ist keine physische Tabelle, die Daten dauerhaft speichert. Sie ist eine virtuelle Tabelle, die die Daten beim Zugriff aus anderen Tabellen holt. Views speichern selbst keine Daten, sondern bieten Zugriff auf Daten, die bereits in anderen Tabellen liegen.

3. Eine Schnittstelle für den direkten Zugriff der Nutzer auf die Datenbank. — Eine View ist keine Schnittstelle für den direkten Zugriff der Nutzer auf die Datenbank. Sie verbirgt die Implementierungsdetails und bietet eine vereinfachte Schnittstelle für den Datenzugriff. Die Nutzer greifen auf die View zu und nicht direkt auf die Datenbank.

4. Eine temporäre Tabelle, die zur Datenmanipulation verwendet wird. — Eine View ist keine temporäre Tabelle zur Datenmanipulation. Sie ist eine virtuelle Tabelle, die die Daten beim Zugriff aus anderen Tabellen holt. Views werden nicht zur Veränderung von Daten verwendet, sondern um einen bequemen Zugriff darauf bereitzustellen.
