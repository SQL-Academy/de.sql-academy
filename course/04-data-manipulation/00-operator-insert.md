---
meta:
    title: "Daten hinzufügen, der INSERT-Operator"
    description: "Einen neuen Datensatz in SQL hinzufügen. Auto-Increment des Primary Keys beim Erstellen einer Zeile. SQL-Operator INSERT INTO."
---

# Daten hinzufügen, der INSERT-Operator

Um neue Datensätze in eine Tabelle einzufügen, verwendest du den Operator `INSERT`. Schauen wir uns seine allgemeine Struktur an.

## Allgemeine Struktur einer Query mit INSERT

```sql
INSERT INTO tabellen_name [(tabellen_feld, ...)]
VALUES (wert_des_tabellen_felds, ...)
| SELECT tabellen_feld, ... FROM tabellen_name ...
```

Werte kannst du entweder direkt mit `VALUES` in Klammern und durch Kommas getrennt auflisten oder über einen `SELECT`-Operator einfügen.

## Felder aufzählen und Werte zuordnen

Beim Einsatz des `INSERT`-Operators kannst du explizit angeben, in welche Felder der Tabelle die Daten geschrieben werden. Dazu listest du die Feldnamen in Klammern nach dem Tabellennamen auf:

```sql
INSERT INTO tabellen_name (feld1, feld2, feld3)
VALUES (wert1, wert2, wert3);
```

**Wichtige Regeln:**

- Die Reihenfolge der Werte in `VALUES` muss exakt der Reihenfolge der Felder in der Aufzählung entsprechen
- Die Anzahl der Werte muss mit der Anzahl der angegebenen Felder übereinstimmen
- Wird ein Feld nicht aufgeführt, erhält es den Default-Wert (falls definiert) oder `NULL` (falls das Feld `NULL` erlaubt)

Wenn die Tabelle `Goods` zum Beispiel die Felder `good_id`, `good_name` und `type` hat, sind die folgenden Queries gleichwertig:

```sql
-- Explizite Angabe der Felder
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 2);

-- Geänderte Feldreihenfolge - die Werte werden entsprechend vertauscht
INSERT INTO Goods (good_name, type, good_id)
VALUES ('Table', 2, 20);
```

Gibst du keine Feldliste an, müssen die Werte für **alle** Felder in der Reihenfolge stehen, in der sie beim Erstellen der Tabelle definiert wurden:

```sql
INSERT INTO Goods
VALUES (20, 'Table', 2);
```

> Es empfiehlt sich, die Feldliste immer explizit anzugeben.
> Das macht den Code lesbarer, schützt vor Fehlern bei Änderungen der Tabellenstruktur und erlaubt das Einfügen von Werten nur in die benötigten Felder.

## Unterschiede zwischen den INSERT-Syntaxen

Der `INSERT`-Operator unterstützt zwei Hauptvarianten, um Daten anzugeben:

### INSERT INTO ... VALUES

Wird für **bereits bekannte** Werte verwendet. Du kannst damit eine oder mehrere Zeilen auf einmal einfügen:

```sql
-- Eine Zeile
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 2);

-- Mehrere Zeilen
INSERT INTO Goods (good_id, good_name, type)
VALUES
    (20, 'Table', 2),
    (21, 'Chair', 2),
    (22, 'Lamp', 8);
```

**Wann benutzen:** für konkrete, statische Daten, die im Voraus bekannt sind.

### INSERT INTO ... SELECT

Wird verwendet, um Daten einzufügen, die **aus einer Query stammen**. Damit kannst du Daten von einer Tabelle in eine andere kopieren oder die Ergebnisse komplexer Berechnungen einfügen:

```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT 20, 'Table', 2;

-- Oder Kopieren aus einer anderen Tabelle
INSERT INTO Goods (good_id, good_name, type)
SELECT good_id + 100, good_name, type
FROM Goods
WHERE type = 2;
```

**Wann benutzen:** zum Kopieren von Daten zwischen Tabellen, zum Einfügen von Berechnungsergebnissen oder wenn die Daten von vorhandenen Einträgen in der Datenbank abhängen.

So kannst du neue Datensätze auf folgende Arten hinzufügen:

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-insert).

- Mit der Syntax `INSERT INTO ... SELECT`

    ```sql
    INSERT INTO Goods (good_id, good_name, type)
    SELECT 20, 'Table', 2;
    ```

- Mit der Syntax `INSERT INTO ... VALUES (...)`

    ```sql
    INSERT INTO Goods (good_id, good_name, type)
    VALUES (20, 'Table', 2);
    ```

Beide Queries liefern dasselbe Ergebnis:

| good_id | good_name          | type |
| ------- | ------------------ | ---- |
| 1       | apartment fee      | 1    |
| 2       | phone fee          | 1    |
| 3       | bread              | 2    |
| 4       | milk               | 2    |
| 5       | red caviar         | 3    |
| 6       | cinema             | 4    |
| 7       | black caviar       | 3    |
| 8       | cough tablets      | 5    |
| 9       | potato             | 2    |
| 10      | pineapples         | 3    |
| 11      | television         | 8    |
| 12      | vacuum cleaner     | 8    |
| 13      | jacket             | 7    |
| 14      | fur coat           | 7    |
| 15      | music school fee   | 6    |
| 16      | english school fee | 6    |
| 20      | Table              | 2    |

## Primary Key beim Einfügen eines neuen Datensatzes

Denk daran, dass der Primary Key einer Tabelle eindeutig sein muss. Das Einfügen eines bereits vorhandenen Werts führt zu einem Fehler.

Beim Anlegen neuer Datensätze mit eindeutigen Indizes ist es nicht immer einfach, einen passenden eindeutigen Wert zu wählen.
Eine Möglichkeit ist eine zusätzliche Query, die den größten vorhandenen Wert des Primary Keys ermittelt, um daraus einen neuen eindeutigen Wert abzuleiten.

```sql
INSERT INTO Goods SELECT MAX(good_id) + 1, 'Table', 2 FROM Goods;
```

Hier nutzen wir die Funktion `MAX`, um den maximalen Wert des Primary Keys zu ermitteln. Das ist allerdings nicht der zuverlässigste und universellste Ansatz: Er funktioniert am besten mit numerischen Datentypen, für alle anderen wird die Umsetzung schnell unhandlich. Außerdem kannst du so einen Wert erhalten, der zuvor in der Tabelle existierte und gelöscht wurde. Das kann zu Inkonsistenzen in den Daten führen 💥, daher solltest du in echten Projekten lieber eine alternative Methode verwenden.

## Automatische Generierung des Primary Keys

**MySQL**

MySQL bringt einen Mechanismus zur automatischen Generierung des Primary Keys mit. Dazu reicht es, dem Primary Key `good_id` das Attribut `AUTO_INCREMENT` zu geben.
Beim Anlegen eines neuen Datensatzes kannst du dann als Wert für `good_id` einfach `NULL` oder `0` übergeben — das Feld erhält automatisch einen Wert, der um eins größer ist als der vorherige.

```sql
CREATE TABLE Goods (
	good_id INT NOT NULL AUTO_INCREMENT,
	good_name VARCHAR(255),
	type INT
);
```

```sql
INSERT INTO Goods VALUES (NULL, 'Table', 2);
```

**PostgreSQL**

PostgreSQL bietet ebenfalls einen Mechanismus zur automatischen Vergabe eindeutiger Identifikatoren.
Dafür stehen die Typen `SMALLSERIAL`, `SERIAL` und `BIGSERIAL` zur Verfügung. Das sind keine echten Datentypen, sondern eher eine bequeme Kurzschreibweise für Spalten mit eindeutigem Identifikator.
Eine Spalte mit einem dieser Typen ist eine Ganzzahl und wird beim Einfügen eines neuen Datensatzes automatisch hochgezählt.

```sql
CREATE TABLE Goods (
	good_id SERIAL,
	good_name VARCHAR(255),
	type INT
);
```

```sql
INSERT INTO Goods (good_name, type) VALUES ('Table', 2);
```
