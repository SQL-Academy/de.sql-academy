---
meta:
    title: "Daten löschen: der DELETE-Operator"
    description: "Datensätze in SQL löschen. SQL-Operatoren DELETE und TRUNCATE und ihre Unterschiede. DELETE-Query mit JOIN."
---

# Daten löschen: der DELETE-Operator

Hin und wieder kommt die Aufgabe, Datensätze aus einer Tabelle zu löschen. Dafür gibt es in SQL die Operatoren `DELETE` und `TRUNCATE`,
wobei der erste die universellere und sicherere Variante ist.

## Allgemeine Struktur einer DELETE-Query

```sql
DELETE FROM tabellen_name
[WHERE auswahl_bedingung];
```

Fehlt die Auswahlbedingung `WHERE`, werden alle Datensätze der angegebenen Tabelle gelöscht.

Die gleiche Operation (alle Datensätze löschen) lässt sich auch über `TRUNCATE` erledigen.
Dieser Operator löscht die Tabelle und legt sie neu an – das geht deutlich schneller als das Löschen aller Datensätze nacheinander (wie bei `DELETE`), insbesondere bei großen Tabellen.

## Allgemeine Struktur einer TRUNCATE-Query

```sql
TRUNCATE TABLE tabellen_name;
```

**MySQL**

> Der Query-Optimierer von MySQL nutzt automatisch `TRUNCATE`, wenn die `DELETE`-Anweisung weder eine `WHERE`-Bedingung noch ein `LIMIT` enthält.

`TRUNCATE` hat allerdings einige Besonderheiten:

**MySQL**

- Trigger werden nicht ausgelöst, insbesondere keine Delete-Trigger.
- Löscht alle Zeilen, ohne das Löschen einzelner Zeilen im Transaktionslog zu protokollieren.
- Setzt den ID-Zähler auf den Startwert zurück.
- Zur Nutzung sind Schreibrechte auf der Tabelle nötig.

**PostgreSQL**

- Trigger werden nicht ausgelöst, insbesondere keine Delete-Trigger.
- Löscht alle Zeilen, ohne das Löschen einzelner Zeilen im Transaktionslog zu protokollieren.
- Kann den ID-Zähler zurücksetzen, wenn die Option `RESTART IDENTITY` verwendet wird (Standard ist `CONTINUE IDENTITY`, der Zähler bleibt erhalten).
- Zur Nutzung sind Schreibrechte auf der Tabelle nötig.

## Datensätze in Multi-Table-Queries löschen

Verwendet eine `DELETE`-Query einen `JOIN`, musst du angeben, aus welchen Tabellen Datensätze gelöscht werden sollen.

**MySQL**

```sql
DELETE tabellen_name_1 FROM
tabellen_name_1 JOIN tabellen_name_2
ON tabellen_name_1.feld = tabellen_name_2.feld
[WHERE auswahl_bedingung];
```

**PostgreSQL**

```sql
DELETE FROM tabellen_name_1
USING tabellen_name_2
WHERE tabellen_name_1.feld = tabellen_name_2.feld
[AND auswahl_bedingung];
```

Angenommen, wir wollen alle Reservierungen für Unterkünfte ohne Küche löschen. Die Query sieht dann so aus:

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

Wenn wir zusätzlich zur Reservierung auch die Unterkunft selbst löschen wollten, sähe die Query so aus:

**MySQL**

```sql
DELETE Reservations, Rooms FROM
Reservations JOIN Rooms ON
Reservations.room_id = Rooms.id
WHERE Rooms.has_kitchen = false;
```

**PostgreSQL**

In PostgreSQL nutzt du zum gleichzeitigen Löschen aus mehreren Tabellen separate DELETE-Queries oder eine Transaktion:

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
