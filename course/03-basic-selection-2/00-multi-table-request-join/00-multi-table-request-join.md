---
meta:
    title: "Multi-Table-Queries und der JOIN-Operator"
    description: "Beschreibung und Syntax des SQL-Operators JOIN, Anwendungsbeispiele und Übungen zur Selbstkontrolle"
---

# Multi-Table-Queries: JOIN

## Multi-Table-Queries

In den vorherigen Artikeln haben wir nur mit einer einzelnen Tabelle gearbeitet.
In der Praxis musst du aber sehr oft Daten aus mehreren Tabellen abrufen und diese auf irgendeine Weise verbinden.
In diesem Artikel lernst du die wichtigsten Wege kennen, Tabellen zu verbinden.

Wenn wir zum Beispiel die Ausgaben für Käufe anzeigen wollen, können wir das so machen:

```sql
SELECT family_member, amount * unit_price AS price FROM Payments
```

| family_member | price |
| ------------- | ----- |
| 1             | 2000  |
| 2             | 2100  |
| 3             | 100   |
| 4             | 350   |
| 4             | 300   |
| 5             | 100   |
| 2             | 120   |
| 2             | 5500  |
| 5             | 230   |
| 3             | 2200  |
| 2             | 66000 |
| 1             | 40    |
| 3             | 100   |
| 3             | 1200  |

In der Spalte `family_member` der Ergebnismenge stehen die IDs der Datensätze aus der Tabelle `Payments`, die für uns aber wenig aussagen.

Statt dieser IDs wäre es viel sprechender, die Namen der Käufer auszugeben (das Feld `member_name` aus der Tabelle `FamilyMembers`).
Genau dafür gibt es das Verbinden von Tabellen und den `JOIN`-Operator.

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/multi-table-request-join).

## Allgemeine Struktur einer Multi-Table-Query

```sql
SELECT tabellen_felder
FROM tabelle_1
[INNER | {LEFT | RIGHT | FULL} [OUTER]] JOIN tabelle_2
    ON verbund_bedingung
[...]
```

- `tabellen_felder` — Die Spalten aus beliebigen verbundenen Tabellen, die ins Ergebnis kommen.
- `tabelle_1` — Die erste Tabelle, mit der die übrigen verbunden werden.
- `INNER` (Schlüsselwort, optional) — Innerer Verbund: Ins Ergebnis kommen nur Zeilen, für die ein Gegenstück gefunden
  wurde. Das ist der Standard, deshalb kannst du `INNER` weglassen.
- `LEFT | RIGHT | FULL` (Schlüsselwort, optional) — Äußerer Verbund: links, rechts oder vollständig. Neben den Paaren kommen auch die
  Zeilen ohne Gegenstück aus der linken, der rechten oder beiden Tabellen ins Ergebnis.
- `OUTER` (Schlüsselwort, optional) — Betont nur, dass der Verbund ein äußerer ist. Steht nach `LEFT`, `RIGHT` oder `FULL`
  und ändert am Ergebnis nichts.
- `tabelle_2` — Die Tabelle, die angefügt wird. Um weitere Tabellen anzufügen, ergänze für jede ein
  eigenes `JOIN ... ON ...`.
- `verbund_bedingung` — Die Regel, nach der Zeilen der beiden Tabellen einander finden, zum Beispiel
  `Payments.family_member = FamilyMembers.member_id`.

Die Unterschiede zwischen innerem und äusserem Verbund und ihre Funktionsweise schauen wir uns in den folgenden Artikeln genauer an.

Für unser Beispiel mit den Käufen reicht ein Verbund mit innerem `JOIN`. Die Query sieht so aus:

```sql
SELECT family_member, member_name, amount * unit_price AS price FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

| family_member | member_name     | price |
| ------------- | --------------- | ----- |
| 1             | Headley Quincey | 2000  |
| 2             | Flavia Quincey  | 2100  |
| 3             | Andie Quincey   | 100   |
| 4             | Lela Quincey    | 350   |
| 4             | Lela Quincey    | 300   |
| 5             | Annie Quincey   | 100   |
| 2             | Flavia Quincey  | 120   |
| 2             | Flavia Quincey  | 5500  |
| 5             | Annie Quincey   | 230   |
| 3             | Andie Quincey   | 2200  |
| 2             | Flavia Quincey  | 66000 |
| 1             | Headley Quincey | 40    |
| 3             | Andie Quincey   | 100   |
| 3             | Andie Quincey   | 1200  |

In dieser Query verknüpfen wir Datensätze aus `Payments` mit Datensätzen aus `FamilyMembers`.

Damit das funktioniert, geben wir an, wie genau
die Datensätze aus den beiden Tabellen einander zugeordnet werden. Diese Bedingung kommt nach `ON`:

```sql
ON Payments.family_member = FamilyMembers.member_id
```

In unserem Fall verweist das Feld `family_member` auf die ID in der Tabelle `FamilyMembers` und ermöglicht so die eindeutige Zuordnung.

> In den meisten Fällen ist die Verbundbedingung eine Gleichheit von Spalten der Tabellen (tabelle_1.feld = tabelle_2.feld),
> du kannst aber genauso andere Vergleichsoperatoren verwenden.

## Alle Spalten einer Tabelle in einer Multi-Table-Query ausgeben

Solange Queries nur an einer einzelnen Tabelle liefen, reichte ein `*`, um alle Felder dieser Tabelle auszugeben. Sobald mehrere Tabellen im Spiel sind, bedeutet `*` "alle Spalten aller in `FROM` aufgeführten Tabellen".

Manchmal wollen wir nur die Spalten einer bestimmten Tabelle ausgeben. Beispiel: ein Verbund der Tabellen `Payments` und `FamilyMembers`, im Ergebnis sollen aber nur die Felder aus `FamilyMembers` stehen. Wie geht das? Ganz einfach – vor das `*` setzt du den Tabellennamen:

```sql
SELECT FamilyMembers.* FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

> Merk dir diese Möglichkeit – sie kommt in den folgenden Aufgaben noch zum Einsatz.

Genauso lassen sich **alle Spalten aus mehreren Tabellen** ausgeben:

```sql
SELECT Payments.*, FamilyMembers.* FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

> In diesem Fall könntest du statt `Payments.*, FamilyMembers.*` einfach `*` verwenden, da in `FROM` nur diese beiden Tabellen stehen. Das Ergebnis ist in beiden Fällen identisch.

Und wenn man **einige Spalten aus der einen und alle aus der anderen Tabelle** ausgeben möchte? Auch das geht! Geben wir die Felder `payment_id` und `family_member` aus `Payments` und alle Felder aus `FamilyMembers` aus:

```sql
SELECT payment_id, family_member, FamilyMembers.* FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

## Tabellen-Aliase

Bei größeren Multi-Table-Queries lohnt es sich, Aliase für Tabellen zu verwenden. Das verbessert die Lesbarkeit und hilft, Fehler in komplexen Queries zu vermeiden.

Aliase werden nach dem Tabellennamen mit dem Schlüsselwort `AS` vergeben:

```sql
SELECT id, name
FROM Passenger AS pass
```

Jetzt kannst du die Spalten der Tabelle über den Alias ansprechen:

```sql
SELECT pass.id, pass.name
FROM Passenger AS pass
```

> Das Schlüsselwort `AS` ist optional, genau wie bei Spalten-Aliasen.

> Sobald du einer Tabelle einen Alias gegeben hast, sind ihre Spalten nicht mehr über den ursprünglichen Namen erreichbar. Statt `<tabelle>.<feld>` musst du `<tabellen_alias>.<feld>` verwenden.

Ein Beispiel mit mehreren Tabellen. Angenommen, wir wollen IDs und Namen aller Passagiere ausgeben, die mindestens einmal geflogen sind:

```sql
SELECT
    pass.id,
    pass.name
FROM Passenger AS pass
INNER JOIN Pass_in_trip AS pit
    ON pit.passenger = pass.id
```

In diesem Beispiel bekommen die Tabellen Passenger und Pass_in_trip die Aliase `pass` bzw. `pit`, mit denen anschließend die Spalten angesprochen werden. Beachte: In beiden Tabellen gibt es ein Feld mit dem Namen `id`. Wenn du nicht angibst, aus welcher Tabelle es kommen soll, kann das DBMS nicht selbst wählen und meldet einen Fehler wegen einer mehrdeutigen Spalte. Gleichnamige Spalten gibst du deshalb immer zusammen mit der Tabelle oder ihrem Alias an: `pass.id`.

Vergleich mit der Variante ohne Aliase – der Unterschied ist deutlich, oder?

```sql
SELECT
    Passenger.id,
    Passenger.name
FROM Passenger
INNER JOIN Pass_in_trip
    ON Pass_in_trip.passenger = Passenger.id
```

Beim Einsatz von Aliasen helfen ein paar einfache Regeln, damit deine Queries knapp und verständlich bleiben:

- Nutze sinnvolle Kurzformen (zum Beispiel die Anfangsbuchstaben des Tabellennamens).
- Vermeide zu kurze (Ein-Buchstaben-) oder nichtssagende Aliase.
