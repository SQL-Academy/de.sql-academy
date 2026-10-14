---
meta:
    title: "Daten löschen: der DELETE-Operator"
    description: "So löschst du Datensätze aus einer SQL-Tabelle mit DELETE: Syntax mit WHERE, Löschen anhand einer anderen Tabelle per Subquery und JOIN, Unterschiede zwischen DELETE und TRUNCATE. Beispiele für MySQL und PostgreSQL."
---

# Daten löschen: der DELETE-Operator

Wenn Daten nicht mehr gebraucht werden, löschst du sie: Eine Ware fliegt aus der Liste, eine stornierte Reservierung wird entfernt. Zeilen löscht der Operator `DELETE`, eine ganze Tabelle leerst du außerdem mit `TRUNCATE`. Allerdings lässt sich das Leeren mit `TRUNCATE` nicht immer rückgängig machen.

## Allgemeine Struktur einer DELETE-Query

```sql
DELETE FROM tabellen_name
[WHERE auswahl_bedingung];
```

- `tabellen_name` — Die Tabelle, aus der Datensätze gelöscht werden.
- `auswahl_bedingung` (optional) — Welche Zeilen gelöscht werden. Ohne `WHERE` werden alle Zeilen der Tabelle gelöscht.

Für den Staubsauger gibt es zum Beispiel keine einzige Zahlung in der Tabelle `Payments`, also kannst du ihn bedenkenlos aus der Warenliste `Goods` entfernen:

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-delete).

```sql
DELETE FROM Goods
WHERE good_name = 'vacuum cleaner';
```

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
| ~~12~~  | ~~vacuum cleaner~~   | ~~8~~ |
| 13      | jacket               | 7     |
| 14      | fur coat             | 7     |
| 15      | music school fee     | 6     |
| 16      | english school fee   | 6     |
| 17      | airsoft game session | 4     |
| 18      | netflix subscription | 4     |

Die Bedingung in `WHERE` schreibst du genauso wie in einer `SELECT`-Query: Du kannst Werte vergleichen, Bedingungen mit `AND` und `OR` verknüpfen und `IN`, `LIKE` sowie Subqueries verwenden.

> Sei vorsichtig beim Löschen von Daten. Wenn du den `WHERE`-Operator weglässt, werden alle Datensätze in der Tabelle gelöscht.
> Bevor du `DELETE` ausführst, lohnt sich ein `SELECT` mit derselben `WHERE`-Bedingung: So prüfst du, dass genau die Zeilen zurückkommen, die du löschen willst.

Probier es selbst aus: Wähl eine Bedingung, sieh dir an, welche Zeilen gelöscht würden, und führ dann die Query aus.

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/operator-delete).

Eine Query ohne Bedingung leert die gesamte Tabelle, die Tabelle selbst bleibt dabei bestehen:

```sql
DELETE FROM Payments;
```

Alle Datensätze einer Tabelle kannst du auch mit `TRUNCATE` löschen.
Dieser Operator geht die Zeilen nicht einzeln durch wie `DELETE`, sondern leert die gesamte Tabelle auf einmal — das geht deutlich schneller, insbesondere bei großen Tabellen.

## Allgemeine Struktur einer TRUNCATE-Query

```sql
TRUNCATE TABLE tabellen_name;
```

- `tabellen_name` — Die Tabelle, die komplett geleert wird.

Dabei verhält sich `TRUNCATE` anders als `DELETE`:

**MySQL**

- Eine `WHERE`-Bedingung ist nicht möglich — die gesamte Tabelle wird geleert
- Delete-Trigger werden nicht ausgelöst
- Die Aktion lässt sich nicht rückgängig machen: `TRUNCATE` bestätigt (committet) die aktuelle [Transaktion](https://sql-academy.org/de/guide/transactions) implizit, und `ROLLBACK` holt die Zeilen nicht zurück
- Setzt den `AUTO_INCREMENT`-Zähler auf den Startwert zurück
- Funktioniert nicht, wenn Foreign Keys anderer Tabellen auf die Tabelle verweisen
- Zur Nutzung brauchst du das Recht `DROP` auf der Tabelle

**PostgreSQL**

- Eine `WHERE`-Bedingung ist nicht möglich — die gesamte Tabelle wird geleert
- Trigger für das Löschen von Zeilen werden nicht ausgelöst — es feuern nur `TRUNCATE`-Trigger, falls welche definiert sind
- Die Aktion lässt sich rückgängig machen: Innerhalb einer [Transaktion](https://sql-academy.org/de/guide/transactions) holt `ROLLBACK` die gelöschten Zeilen zurück
- Kann den ID-Zähler zurücksetzen, wenn die Option `RESTART IDENTITY` verwendet wird (Standard ist `CONTINUE IDENTITY`, der Zähler bleibt erhalten)
- Verweisen Foreign Keys anderer Tabellen auf die Tabelle, musst du die Option `CASCADE` ergänzen — dann werden auch diese Tabellen geleert
- Zur Nutzung brauchst du das Recht `TRUNCATE` auf der Tabelle

Mehr dazu, wie sich `DELETE` von `TRUNCATE` und `DROP` unterscheidet, liest du im Artikel [TRUNCATE vs DELETE vs DROP in SQL: Unterschiede und richtige Wahl](https://sql-academy.org/de/blog/sql-truncate-delete-drop).

## Löschen anhand der Daten einer anderen Tabelle

Manchmal müssen die zu löschenden Zeilen anhand der Daten einer anderen Tabelle ausgewählt werden.
Angenommen, der Service arbeitet nicht mehr mit Unterkünften ohne Küche, und ihre Reservierungen sollen gelöscht werden. Ob es eine Küche gibt, steht in der Tabelle `Rooms`, die Reservierungen liegen in der Tabelle `Reservations`.

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-delete).

Am einfachsten geht das mit einer Subquery in `WHERE`, die die IDs der passenden Unterkünfte zurückgibt:

```sql
DELETE FROM Reservations
WHERE room_id IN (
    SELECT id
    FROM Rooms
    WHERE has_kitchen = false
);
```

**MySQL**

Dasselbe erreichst du mit `JOIN`. Dann gibst du direkt nach `DELETE` die Tabellen an, aus denen Datensätze gelöscht werden.

```sql
DELETE tabellen_name_1 [, tabellen_name_2] FROM
tabellen_name_1 JOIN tabellen_name_2
ON verbund_bedingung
[WHERE auswahl_bedingung];
```

- `tabellen_name_1` — Die Tabelle, aus der Datensätze gelöscht werden. Ihr Name steht sowohl nach `DELETE`
  als auch im `JOIN`.
- `tabellen_name_2` — Die verknüpfte Tabelle, anhand deren Daten die zu löschenden Datensätze ausgewählt
  werden. Gibst du sie auch nach `DELETE` an, werden Datensätze aus beiden Tabellen
  gelöscht.
- `verbund_bedingung` — Wie die Zeilen verknüpft sind, z. B. `tabellen_name_1.feld = tabellen_name_2.feld`.
- `auswahl_bedingung` (optional) — Welche Datensätze gelöscht werden. Die Bedingung kann Spalten beider Tabellen prüfen.

**PostgreSQL**

Dasselbe erreichst du mit `USING`. Danach gibst du die zusätzlichen Tabellen an, anhand derer die zu löschenden Datensätze ausgewählt werden.

```sql
DELETE FROM tabellen_name_1
USING tabellen_name_2
WHERE verbund_bedingung
[AND auswahl_bedingung];
```

- `tabellen_name_1` — Die Tabelle, aus der Datensätze gelöscht werden.
- `tabellen_name_2` — Eine zusätzliche Tabelle, anhand deren Daten die zu löschenden Datensätze ausgewählt
  werden. Aus ihr selbst wird nichts gelöscht.
- `verbund_bedingung` — Wie die Zeilen verknüpft sind, z. B. `tabellen_name_1.feld = tabellen_name_2.feld`.
- `auswahl_bedingung` (optional) — Eine zusätzliche Bedingung: welche der verknüpften Datensätze gelöscht werden. Sie
  kann Spalten beider Tabellen prüfen.

Für unsere Aufgabe sieht die Query so aus:

**MySQL**

```sql
DELETE Reservations FROM
Reservations JOIN Rooms ON
Reservations.room_id = Rooms.id
WHERE Rooms.has_kitchen = false;
```

**PostgreSQL**

```sql
DELETE FROM Reservations
USING Rooms
WHERE Reservations.room_id = Rooms.id
AND Rooms.has_kitchen = false;
```

Löschen wir jetzt zusammen mit den Reservierungen auch die Unterkünfte selbst.

**MySQL**

```sql
DELETE Reservations, Rooms FROM
Reservations JOIN Rooms ON
Reservations.room_id = Rooms.id
WHERE Rooms.has_kitchen = false;
```

Diese Query löscht nur Unterkünfte, für die es Reservierungen gab: Zeilen aus `Rooms` ohne passenden Eintrag in `Reservations` landen nicht im Join.

**PostgreSQL**

Der `DELETE`-Operator löscht Zeilen immer nur aus einer Tabelle, deshalb löschen wir nacheinander: zuerst die Buchungen, dann die Unterkünfte selbst.
Damit sie gemeinsam oder gar nicht ausgeführt werden, fasse sie in einer [Transaktion](https://sql-academy.org/de/guide/transactions) zusammen:

```sql
BEGIN;
DELETE FROM Reservations
USING Rooms
WHERE Reservations.room_id = Rooms.id
AND Rooms.has_kitchen = false;

DELETE FROM Rooms
WHERE Rooms.has_kitchen = false;
COMMIT;
```

Die zweite Query löscht alle Unterkünfte ohne Küche, auch die, die nie gebucht wurden.

**MySQL**

**Subquery auf die Tabelle, aus der gelöscht wird**

Eine Subquery darf nicht dieselbe Tabelle lesen, aus der `DELETE` Zeilen löscht.
Reservierungen unter dem Durchschnittspreis kannst du so zum Beispiel nicht löschen:

```sql
DELETE FROM Reservations
WHERE price < (SELECT AVG(price) FROM Reservations);
```

Die Query bricht mit einem Fehler ab:

```sql
ERROR 1093 (HY000): You can't specify target table 'Reservations' for update in FROM clause
```

Umgehen kannst du diese Einschränkung, indem du die Subquery in ein weiteres `SELECT` einbettest. Dann wird der Durchschnittspreis zuerst berechnet und in einer temporären Tabelle abgelegt, und das Löschen liest aus ihr:

```sql
DELETE FROM Reservations
WHERE price < (
    SELECT avg_price
    FROM (SELECT AVG(price) AS avg_price FROM Reservations) AS stats
);
```

## Prüf dich selbst

Aus der Tabelle `Reservations` sollen nur die Reservierungen gelöscht werden, die günstiger als 100 sind. Welche Query erledigt das?

1. TRUNCATE TABLE Reservations WHERE price \< 100; — TRUNCATE akzeptiert keine Bedingung und leert immer die gesamte Tabelle, deshalb bricht diese Query mit einem Fehler ab.

2. **Richtige Antwort:** DELETE FROM Reservations WHERE price \< 100; — DELETE mit einer WHERE-Bedingung löscht nur die Reservierungen mit einem Preis unter 100, die übrigen bleiben in der Tabelle.

3. DELETE FROM Reservations; — Ohne WHERE-Bedingung löscht die Query alle Reservierungen, nicht nur die günstigen.
