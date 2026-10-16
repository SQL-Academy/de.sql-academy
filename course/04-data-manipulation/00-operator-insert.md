---
meta:
    title: "Daten hinzufügen, der INSERT-Operator"
    description: "So fügst du mit INSERT INTO Datensätze in eine SQL-Tabelle ein: Syntax, mehrere Zeilen auf einmal einfügen, Auto-Increment des Primary Keys. Beispiele für MySQL und PostgreSQL."
---

# Daten hinzufügen, der INSERT-Operator

Bisher haben wir Daten nur gelesen. Aber Daten in Tabellen bleiben nicht stehen: Es kommen neue Produkte, Kunden und Bestellungen hinzu, und für jedes davon braucht eine Tabelle eine neue Zeile. Dafür gibt es den Operator `INSERT`.

## Allgemeine Struktur einer Query mit INSERT

```sql
INSERT INTO tabellen_name [(tabellen_feld [, ...])]
{ VALUES (wert [, ...]) [, ...] | select_ausdruck };
```

- `tabellen_name` — Die Tabelle, in die neue Datensätze eingefügt werden.
- `tabellen_feld` (optional) — Die Spalten, die Werte bekommen. Ohne diese Liste gibst du Werte für alle Spalten der
  Tabelle an.
- `wert` — Ein Wert für eine Spalte: eine Zahl, ein String, ein Ausdruck oder `NULL`. Die Werte
  stehen in derselben Reihenfolge wie die Spalten. Jedes Klammerpaar ist eine Zeile,
  mehrere Zeilen trennst du mit Kommas.
- `select_ausdruck` (Unterabfrage) — Eine `SELECT`-Query, deren Ergebniszeilen in die Tabelle eingefügt werden. Sie muss so
  viele Spalten liefern, wie du aufgezählt hast.

Die Werte listest du entweder nach `VALUES` auf oder holst sie mit einer `SELECT`-Query.

## Felder aufzählen und Werte zuordnen

Du kannst explizit angeben, in welche Felder der Tabelle die Daten geschrieben werden. Dazu listest du die Feldnamen in Klammern nach dem Tabellennamen auf:

```sql
INSERT INTO tabellen_name (tabellen_feld [, ...])
VALUES (wert [, ...]);
```

- `tabellen_name` — Die Tabelle, in die neue Datensätze eingefügt werden.
- `tabellen_feld` — Die Spalte, in die der Wert geschrieben wird. Du listest die Spalten durch Kommas
  getrennt in beliebiger Reihenfolge auf.
- `wert` — Der Wert für die Spalte an derselben Position in der Feldliste.

**Wichtige Regeln:**

- Die Reihenfolge der Werte in `VALUES` muss exakt der Reihenfolge der Felder in der Aufzählung entsprechen
- Die Anzahl der Werte muss mit der Anzahl der angegebenen Felder übereinstimmen
- Wird ein Feld nicht aufgeführt, erhält es den Default-Wert (falls definiert) oder `NULL` (falls das Feld `NULL` erlaubt)

Wenn die Tabelle `Goods` zum Beispiel die Felder `good_id`, `good_name` und `type` hat, sind die folgenden Queries gleichwertig:

```sql
-- Explizite Angabe der Felder
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 8);

-- Geänderte Feldreihenfolge - die Werte werden entsprechend vertauscht
INSERT INTO Goods (good_name, type, good_id)
VALUES ('Table', 8, 20);
```

Gibst du keine Feldliste an, müssen die Werte für **alle** Felder in der Reihenfolge stehen, in der sie beim Erstellen der Tabelle definiert wurden:

```sql
INSERT INTO Goods
VALUES (20, 'Table', 8);
```

> Es empfiehlt sich, die Feldliste immer explizit anzugeben.
> Das macht den Code lesbarer, schützt vor Fehlern bei Änderungen der Tabellenstruktur und erlaubt das Einfügen von Werten nur in die benötigten Felder.

## Unterschiede zwischen den INSERT-Syntaxen

Angenommen, du willst der Warenliste `Goods` einen neuen Artikel hinzufügen: einen Tisch. Dafür gibt es zwei Wege.

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-insert).

Der gewohnte Weg ist, die Werte nach `VALUES` aufzuzählen:

```sql
INSERT INTO Goods (good_id, good_name, type)
VALUES (20, 'Table', 8);
```

Aber `INSERT` nimmt nicht nur eine fertige Liste an. Statt `VALUES` kannst du eine `SELECT`-Query einsetzen, und alles, was sie zurückgibt, landet in der Tabelle. Dabei kommt die `SELECT`-Query sogar ohne `FROM` aus. Zählst du darin einfach Werte auf, gibt sie diese als einzelne Zeile zurück:

```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT 20, 'Table', 8;
```

Beide Queries führen zum selben Ergebnis — in `Goods` ist ein Tisch dazugekommen:

| good_id | good_name            | type  |
| ------- | -------------------- | ----- |
| 1       | apartment fee        | 1     |
| 2       | phone fee            | 1     |
| 3       | bread                | 2     |
| 4       | milk                 | 2     |
| 5       | red caviar           | 3     |
| 6       | cinema               | 4     |
| 7       | black caviar         | 3     |
| 8       | cough tablets        | 5     |
| 9       | potato               | 2     |
| 10      | pineapples           | 3     |
| 11      | television           | 8     |
| 12      | vacuum cleaner       | 8     |
| 13      | jacket               | 7     |
| 14      | fur coat             | 7     |
| 15      | music school fee     | 6     |
| 16      | english school fee   | 6     |
| 17      | airsoft game session | 4     |
| 18      | netflix subscription | 4     |
| **20**  | **Table**            | **8** |

### Wann welcher Weg passt

Wenn die Werte schon vorher feststehen, weil sie zum Beispiel ein Nutzer eingegeben hat, ist `INSERT ... VALUES` am einfachsten. Damit kannst du auch mehrere Zeilen auf einmal einfügen, jede in eigenen Klammern:

```sql
INSERT INTO Goods (good_id, good_name, type)
VALUES
    (20, 'Table', 8),
    (21, 'Chair', 8),
    (22, 'Lamp', 8);
```

`INSERT ... SELECT` hilft dagegen, wenn neue Zeilen aus dem entstehen sollen, was schon in der Datenbank liegt: Datensätze kopieren, in eine andere Tabelle kopieren oder Berechnungsergebnisse speichern. Duplizieren wir zum Beispiel alle Lebensmittel und geben den Kopien um 100 höhere Nummern:

```sql
INSERT INTO Goods (good_id, good_name, type)
SELECT good_id + 100, good_name, type
FROM Goods
WHERE type = 2;
```

Führ die Query aus und schau ans Ende der Tabelle: Eine einzige Query hat gleich drei Zeilen hinzugefügt — Kopien von bread, milk und potato.

## Primary Key beim Einfügen eines neuen Datensatzes

Ein Wert des Primary Keys darf sich in einer Tabelle nicht wiederholen: Der Versuch, einen vorhandenen einzufügen, endet mit einem Fehler.

Welche Nummer ist also frei? Am naheliegendsten ist es, die größte zu nehmen und eins zu addieren:

```sql
INSERT INTO Goods SELECT MAX(good_id) + 1, 'Table', 8 FROM Goods;
```

Das funktioniert, ist aber unzuverlässig. Laufen zwei solche Queries gleichzeitig, können sie dieselbe Nummer bekommen, und eine von ihnen endet mit einem Fehler. Außerdem klappt das nur mit numerischen Schlüsseln und kann eine Nummer vergeben, die früher zu einem gelöschten Datensatz gehörte. Wenn alte Daten irgendwo noch auf diese Nummer verweisen, hängen sie plötzlich an der neuen Zeile 💥. Deshalb vergibt in echten Projekten die Datenbank selbst die Nummer.

## Automatische Generierung des Primary Keys

**MySQL**

Der Primary Key kann automatisch generiert werden. Dazu reicht es, der Spalte `good_id` das Attribut `AUTO_INCREMENT` zu geben.
Beim Anlegen eines neuen Datensatzes kannst du dann als Wert für `good_id` einfach `NULL` oder `0` übergeben — das Feld erhält automatisch den nächsten Zählerwert: um eins größer als der größte bisher verwendete.

```sql
CREATE TABLE Goods (
	good_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
	good_name VARCHAR(255),
	type INT
);
```

```sql
INSERT INTO Goods VALUES (NULL, 'Table', 8);
```

**PostgreSQL**

Eindeutige Identifikatoren können automatisch vergeben werden.
Dafür stehen die Typen `SMALLSERIAL`, `SERIAL` und `BIGSERIAL` zur Verfügung. Das sind keine echten Datentypen, sondern eher eine bequeme Kurzschreibweise für Spalten mit eindeutigem Identifikator.
Eine Spalte mit einem dieser Typen ist eine Ganzzahl und wird beim Einfügen eines neuen Datensatzes automatisch hochgezählt.

```sql
CREATE TABLE Goods (
	good_id SERIAL PRIMARY KEY,
	good_name VARCHAR(255),
	type INT
);
```

```sql
INSERT INTO Goods (good_name, type) VALUES ('Table', 8);
```

## Prüf dich selbst

In der Tabelle `Goods` gibt es bereits eine Ware mit `good_id = 3`, und `good_id` ist der Primary Key. Was passiert, wenn du diese Query ausführst?

```sql
INSERT INTO Goods (good_id, good_name, type)
VALUES (3, 'chair', 8);
```

1. In der Tabelle gibt es danach zwei Zeilen mit good_id = 3 — Der Primary Key ist eindeutig: Eine Tabelle kann keine zwei Zeilen mit demselben Schlüsselwert enthalten.

2. Die Zeile mit good_id = 3 wird durch die neue ersetzt — Ein einfaches INSERT fügt nur Zeilen hinzu und ändert keine bestehenden. Zum Ändern von Datensätzen gibt es den UPDATE-Operator.

3. **Richtige Antwort:** Die Query bricht mit einem Fehler ab — Der Primary-Key-Wert 3 ist bereits vergeben, deshalb lehnt das DBMS das Einfügen ab und meldet einen doppelten Schlüssel.
