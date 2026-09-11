---
meta:
    title: "Struktur relationaler Datenbanken"
    description: "Struktur von Tabellen in relationalen Datenbanken, die Begriffe Datensatz und Attribut. Definition von Primary Key und Foreign Key."
---

# Struktur relationaler Datenbanken

Wir haben relationale Datenbanken bereits kurz <a href="https://sql-academy.org/de/guide/relation-databases" target="_blank">im vorigen Artikel</a> kennengelernt. Aber ein oberflächliches Verständnis reicht uns natürlich nicht? Lass uns über die Bojen hinausschwimmen und tiefer in die Struktur und Terminologie relationaler Datenbanken eintauchen.

## Struktur einer Tabelle

In relationalen Datenbanken werden Informationen in miteinander verknüpften Tabellen gespeichert. Die Tabellen selbst bestehen aus:

- Zeilen, die „Datensätze“ genannt werden
- Spalten, die „Felder“ oder auch „Attribute“ genannt werden

![Struktur einer Tabelle](https://sql-academy.org/static/guidePage/structure-of-relation-databases/de_structure_db.png "Struktur einer Tabelle")

In jeder Tabelle hat jede Spalte einen vorab festgelegten Datentyp. Das können zum Beispiel sein:

- `VARCHAR` (Datentyp für Zeichenketten)
- `INTEGER` (numerischer Datentyp)
- `DATETIME` (Datentyp für Datum und Uhrzeit) und weitere

Und jede Zeile der Tabelle muss für jede Spalte den passenden Typ haben. Das DBMS wird es nicht zulassen, eine beliebige Zeichenkette in ein Feld vom Typ `DATETIME` einzufügen.

Um die Datentypen der Attribute herauszufinden, kannst du einen SQL-Befehl ausführen und den Namen der Tabelle angeben:

**MySQL**

```sql
DESCRIBE FamilyMembers
```

| Field       | Type        | Null | Key | Default | Extra |
| ----------- | ----------- | ---- | --- | ------- | ----- |
| member_id   | int         | NO   | PRI |         |       |
| status      | varchar(50) | NO   |     |         |       |
| member_name | varchar(50) | NO   |     |         |       |
| birthday    | datetime    | NO   |     |         |       |

**PostgreSQL**

```sql
SELECT column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_name = 'familymembers'
  AND table_schema = current_schema();
```

| column_name | data_type                   | is_nullable |
| ----------- | --------------------------- | ----------- |
| member_id   | integer                     | NO          |
| status      | character varying           | NO          |
| member_name | character varying           | NO          |
| birthday    | timestamp without time zone | NO          |

Oder du schaust dir das ER-Diagramm des Datenbankschemas an:

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/structure-of-relation-databases).

## Primary Key

Jedes DBMS hat ein eingebautes System für Datenintegrität und Widerspruchsfreiheit. Dieses System arbeitet mit einer Reihe von Regeln, die im Schema der Datenbank festgelegt sind. Primary Key und Foreign Keys gehören genau zu diesen Regeln.

Um Mehrdeutigkeiten bei der Suche in Tabellen zu vermeiden, gibt es den Primary Key, oder, wie er auch genannt wird, das „Schlüsselfeld“.

> Das Schlüsselfeld (Primary Key) ist ein Feld (oder eine Menge von Feldern), dessen Wert einen Datensatz in der Tabelle eindeutig identifiziert.

Wenn wir uns unsere oben erwähnte Tabelle `FamilyMembers` anschauen, dann ist dort `member_id` das Schlüsselfeld. Dank dieser Regel erlaubt uns das DBMS nicht, einen neuen Datensatz anzulegen, bei dem das Feld `member_id` nicht eindeutig ist.

Es ist anzumerken, dass das Vorhandensein eines Primary Key nicht zwingend erforderlich ist und die Datenintegrität zum Beispiel auch auf Anwendungsebene sichergestellt werden kann.

## Foreign Key

> Ein Foreign Key ist ein Feld (oder eine Menge von Feldern) in einer Tabelle, das auf den Primary Key einer anderen Tabelle verweist.

Die Tabelle mit dem Foreign Key wird als Kindtabelle bezeichnet, die Tabelle mit dem Primary Key als referenzierte oder Elterntabelle.

Die Foreign-Key-Regel garantiert, dass beim Anlegen von Datensätzen in der Kindtabelle der Wert des Feldes, das als Foreign Key fungiert, in der Elterntabelle vorhanden ist.

![Beispiel für Foreign Keys](https://sql-academy.org/static/guidePage/structure-of-relation-databases/de_keys.png "Beispiel für Foreign Keys")

Das Vorhandensein eines Foreign Key ist genauso wenig zwingend wie das Vorhandensein eines Primary Key.

Ist kein Foreign Key definiert, funktioniert alles weiterhin, aber das DBMS prüft zum Beispiel nicht, ob beim Anlegen eines Datensatzes in der Tabelle `Purchase` in den Feldern `buyer_id` und `good_id` Werte stehen, die in den entsprechenden Tabellen im Feld `id` definiert sind.

Welche der folgenden Aussagen ist **falsch** in Bezug auf Schlüssel in relationalen DBMS?

1. In jeder Tabelle kann es nur 1 Primary Key geben — In jeder Tabelle kann es nur 1 Primary Key geben, mit dem sich ein Datensatz in der Tabelle eindeutig bestimmen lässt. Dieser Schlüssel kann aus mehreren Feldern der Tabelle bestehen, aber es gibt immer nur einen.

2. Eine Tabelle kann mehrere Foreign Keys enthalten oder auch gar keinen — Die Definition der Foreign-Key-Regel ist optional, sie muss nicht vorhanden sein. Gleichzeitig können wir, wenn eine Tabelle mehrere Felder hat, die auf Identifikatoren anderer Tabellen verweisen, auch mehrere Foreign Keys definieren.

3. **Richtige Antwort:** Die Regeln für Primary Key und Foreign Key dienen demselben Zweck — Die Ziele der Primary-Key-Regel und der Foreign-Key-Regel sind unterschiedlich. Der Primary Key sorgt für die eindeutige Identifizierung jedes Datensatzes innerhalb einer Tabelle. Der Foreign Key hingegen sorgt für die referenzielle Integrität.
