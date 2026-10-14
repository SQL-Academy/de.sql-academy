---
meta:
    title: "Daten aktualisieren, der UPDATE-Operator"
    description: "So änderst du Daten in einer SQL-Tabelle mit UPDATE: Syntax von SET und WHERE, mehrere Spalten in einer Query ändern, Aktualisieren anhand einer anderen Tabelle, typische Fehler. Beispiele für MySQL und PostgreSQL."
---

# Daten aktualisieren, der UPDATE-Operator

Einmal eingetragene Daten bleiben nicht immer richtig: Menschen ändern ihren Nachnamen, in Zahlungen tauchen Tippfehler auf, Preise müssen neu berechnet werden. Um eine Zeile zu korrigieren, ohne sie zu löschen, verwendest du den Operator `UPDATE`.

## Allgemeine Struktur einer Query mit UPDATE

```sql
UPDATE tabellen_name
SET tabellen_feld = wert [, ...]
[WHERE auswahl_bedingung];
```

- `tabellen_name` — Die Tabelle, deren Datensätze geändert werden.
- `tabellen_feld` — Die Spalte, die einen neuen Wert bekommt. Du kannst mehrere Paare `tabellen_feld =
    wert` durch Kommas getrennt angeben.
- `wert` — Der neue Wert: eine Zahl, ein String, ein Ausdruck wie `price * 1.1` oder `NULL`.
- `auswahl_bedingung` (optional) — Welche Zeilen geändert werden. Ohne `WHERE` werden alle Zeilen der Tabelle geändert.

Andie (Andie Quincey) hat zum Beispiel seinen Nachnamen geändert und heißt jetzt Andie Anthony.

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-update).

```sql
UPDATE FamilyMembers
SET member_name = 'Andie Anthony'
WHERE member_name = 'Andie Quincey';
```

| member_id | status   | member_name                     | birthday                 |
| --------- | -------- | ------------------------------- | ------------------------ |
| 1         | father   | Headley Quincey                 | 1960-05-13T00:00:00.000Z |
| 2         | mother   | Flavia Quincey                  | 1963-02-16T00:00:00.000Z |
| 3         | son      | ~~Andie Quincey~~ Andie Anthony | 1983-06-05T00:00:00.000Z |
| 4         | daughter | Lela Quincey                    | 1985-06-07T00:00:00.000Z |
| 5         | daughter | Annie Quincey                   | 1988-04-10T00:00:00.000Z |
| 6         | father   | Ernest Forrest                  | 1961-09-11T00:00:00.000Z |
| 7         | mother   | Constance Forrest               | 1968-09-06T00:00:00.000Z |
| 8         | daughter | Wednesday Addams                | 2005-01-13T00:00:00.000Z |

> Sei vorsichtig beim Aktualisieren von Daten. Wenn du den `WHERE`-Operator weglässt, werden alle Datensätze in der Tabelle aktualisiert.

## Mehrere Spalten ändern

Um mehrere Spalten auf einmal zu ändern, gib nach `SET` mehrere Paare `tabellen_feld = wert` durch Kommas getrennt an.
Andie hat zum Beispiel 4 Packungen Milch zu je 25 gekauft, aber in der Zahlung mit der ID 3 wurden versehentlich 5 zu je 20 eingetragen. Korrigieren wir beide Spalten mit einer Query:

```sql
UPDATE Payments
SET amount = 4, unit_price = 25
WHERE payment_id = 3;
```

| payment_id | date                     | family_member | good | amount  | unit_price |
| ---------- | ------------------------ | ------------- | ---- | ------- | ---------- |
| 1          | 2005-02-12T00:00:00.000Z | 1             | 1    | 1       | 2000       |
| 2          | 2005-03-23T00:00:00.000Z | 2             | 1    | 1       | 2100       |
| 3          | 2005-05-14T00:00:00.000Z | 3             | 4    | ~~5~~ 4 | ~~20~~ 25  |
| 4          | 2005-07-22T00:00:00.000Z | 4             | 5    | 1       | 350        |
| 5          | 2005-07-26T00:00:00.000Z | 4             | 7    | 2       | 150        |
| 6          | 2005-02-20T00:00:00.000Z | 5             | 6    | 1       | 100        |
| 7          | 2005-07-30T00:00:00.000Z | 2             | 6    | 1       | 120        |
| 8          | 2005-09-12T00:00:00.000Z | 2             | 16   | 1       | 5500       |
| 9          | 2005-09-30T00:00:00.000Z | 5             | 15   | 1       | 230        |
| 10         | 2005-10-27T00:00:00.000Z | 5             | 15   | 1       | 230        |
| 11         | 2005-11-28T00:00:00.000Z | 5             | 15   | 1       | 250        |
| 12         | 2005-12-22T00:00:00.000Z | 5             | 15   | 1       | 250        |
| 13         | 2005-08-11T00:00:00.000Z | 3             | 13   | 1       | 2200       |
| 14         | 2005-10-23T00:00:00.000Z | 2             | 14   | 1       | 66000      |
| 15         | 2005-02-03T00:00:00.000Z | 1             | 9    | 5       | 8          |
| 16         | 2005-03-11T00:00:00.000Z | 1             | 9    | 5       | 7          |
| 17         | 2005-03-18T00:00:00.000Z | 2             | 9    | 3       | 8          |
| 18         | 2005-04-20T00:00:00.000Z | 1             | 9    | 8       | 8          |
| 19         | 2005-05-13T00:00:00.000Z | 1             | 9    | 5       | 7          |
| 20         | 2005-06-11T00:00:00.000Z | 2             | 9    | 3       | 150        |
| 21         | 2006-01-12T00:00:00.000Z | 3             | 10   | 1       | 100        |
| 22         | 2006-03-12T00:00:00.000Z | 1             | 5    | 3       | 10         |
| 23         | 2005-06-05T00:00:00.000Z | 1             | 8    | 1       | 300        |
| 24         | 2005-06-20T00:00:00.000Z | 3             | 6    | 8       | 150        |
| 25         | 2005-06-21T00:00:00.000Z | 2             | 9    | 3       | 150        |
| 26         | 2005-06-22T00:00:00.000Z | 5             | 18   | 1       | 16         |
| 27         | 2005-06-22T00:00:00.000Z | 3             | 17   | 1       | 59         |
| 28         | 2006-01-15T00:00:00.000Z | 1             | 11   | 1       | 10000      |

Alle aufgeführten Spalten ändern sich in denselben Zeilen — in denen, die die `WHERE`-Bedingung erfüllen.

## Berechnete Werte

Einen neuen Wert kannst du aus dem alten berechnen, zum Beispiel alle Preise verdoppeln:

```sql
UPDATE Payments
SET unit_price = unit_price * 2;
```

Im Ausdruck kannst du auch andere Spalten derselben Zeile verwenden, solange ihre Datentypen kompatibel sind.
Erfassen wir zum Beispiel die Zahlung mit der ID 3 als eine einzige Position: In `unit_price` steht dann der Preis des gesamten Einkaufs und in `amount` eine Eins.

```sql
UPDATE Payments
SET unit_price = unit_price * amount,
    amount = 1
WHERE payment_id = 3;
```

**Reihenfolge der Zuweisungen in SET**

**MySQL**

Bei einem `UPDATE` auf eine einzelne Tabelle werden die Zuweisungen in `SET` von links nach rechts ausgeführt, und der nächste Ausdruck sieht bereits den neuen Wert der Spalte.
Vertauschst du die Zuweisungen, wird `unit_price` mit der gerade geschriebenen Eins multipliziert und bleibt unverändert:

```sql
UPDATE Payments
SET amount = 1,
    unit_price = unit_price * amount
WHERE payment_id = 3;
```

Ändere deshalb eine Spalte, von der andere Ausdrücke abhängen, immer als Letztes.

**PostgreSQL**

Alle Ausdrücke in `SET` werden mit den Werten berechnet, die die Zeile vor der Änderung hatte. Die Reihenfolge der Zuweisungen spielt deshalb keine Rolle.
Diese Query liefert dasselbe Ergebnis:

```sql
UPDATE Payments
SET amount = 1,
    unit_price = unit_price * amount
WHERE payment_id = 3;
```

## Aktualisieren anhand der Daten einer anderen Tabelle

Manchmal müssen die zu ändernden Zeilen anhand der Daten einer anderen Tabelle ausgewählt werden. Zum Beispiel hat sich herausgestellt, dass in allen Zahlungen für Kaviar nur der halbe Preis eingetragen wurde.
Am einfachsten geht das mit einer Subquery in `WHERE`:

```sql
UPDATE Payments
SET unit_price = unit_price * 2
WHERE good IN (
    SELECT good_id
    FROM Goods
    WHERE good_name LIKE '%caviar%'
);
```

**MySQL**

Dasselbe erreichst du, indem du die Tabellen direkt in der `UPDATE`-Query mit `JOIN` verknüpfst:

```sql
UPDATE tabellen_name_1
JOIN tabellen_name_2 ON verbund_bedingung
SET tabellen_name_1.tabellen_feld = wert [, ...]
[WHERE auswahl_bedingung];
```

- `tabellen_name_1` — Die Tabelle, deren Datensätze geändert werden.
- `tabellen_name_2` — Die verknüpfte Tabelle, anhand deren Daten die Zeilen ausgewählt und die neuen Werte
  berechnet werden.
- `verbund_bedingung` — Wie die Zeilen verknüpft sind, z. B. `tabellen_name_1.feld = tabellen_name_2.feld`.
- `auswahl_bedingung` (optional) — Welche Zeilen geändert werden. Die Bedingung kann Spalten beider Tabellen prüfen.

```sql
UPDATE Payments
JOIN Goods ON Payments.good = Goods.good_id
SET Payments.unit_price = Payments.unit_price * 2
WHERE Goods.good_name LIKE '%caviar%';
```

**PostgreSQL**

Dasselbe erreichst du, indem du die zusätzlichen Tabellen nach `FROM` angibst:

```sql
UPDATE tabellen_name_1
SET tabellen_feld = wert [, ...]
FROM tabellen_name_2
WHERE verbund_bedingung
[AND auswahl_bedingung];
```

- `tabellen_name_1` — Die Tabelle, deren Datensätze geändert werden. Nach `FROM` wird sie nicht wiederholt.
- `tabellen_feld` — Eine Spalte von `tabellen_name_1`. Sie wird ohne Tabellennamen angegeben.
- `tabellen_name_2` — Eine zusätzliche Tabelle, anhand deren Daten die Zeilen ausgewählt und die neuen Werte
  berechnet werden.
- `verbund_bedingung` — Wie die Zeilen verknüpft sind, z. B. `tabellen_name_1.feld = tabellen_name_2.feld`.
- `auswahl_bedingung` (optional) — Eine zusätzliche Bedingung: welche der verknüpften Zeilen geändert werden.

```sql
UPDATE Payments
SET unit_price = unit_price * 2
FROM Goods
WHERE Payments.good = Goods.good_id
    AND Goods.good_name LIKE '%caviar%';
```

**MySQL**

**Subquery auf die geänderte Tabelle**

Eine Subquery darf nicht dieselbe Tabelle lesen, die `UPDATE` ändert.
Den Preis in Zahlungen unter dem Durchschnitt kannst du so zum Beispiel nicht verdoppeln:

```sql
UPDATE Payments
SET unit_price = unit_price * 2
WHERE unit_price < (SELECT AVG(unit_price) FROM Payments);
```

Die Query bricht mit einem Fehler ab:

```sql
ERROR 1093 (HY000): You can't specify target table 'Payments' for update in FROM clause
```

Umgehen kannst du diese Einschränkung, indem du die Subquery in ein weiteres `SELECT` einbettest. Dann wird der Durchschnittspreis zuerst berechnet und in einer temporären Tabelle abgelegt, und das Update liest aus ihr:

```sql
UPDATE Payments
SET unit_price = unit_price * 2
WHERE unit_price < (
    SELECT avg_price
    FROM (SELECT AVG(unit_price) AS avg_price FROM Payments) AS stats
);
```

## Typische Fehler

### Vergessenes WHERE

Eine Query ohne Bedingung ändert alle Zeilen der Tabelle. Prüfe die Bedingung zuerst mit einer `SELECT`-Query und setze sie erst dann in `UPDATE` ein.

### AND statt Komma in SET

Die folgende Query ändert nicht zwei Spalten: Alles rechts vom ersten `=` gilt als ein einziger logischer Ausdruck.

```sql
UPDATE Payments
SET amount = 4 AND unit_price = 25
WHERE payment_id = 3;
```

**MySQL**

Die Query läuft ohne Fehler durch und schreibt das Ergebnis dieses Ausdrucks — `0` oder `1` — in `amount`, während `unit_price` unverändert bleibt.

**PostgreSQL**

Die Query bricht mit einem Fehler ab:

```sql
ERROR: argument of AND must be type boolean, not type integer
```

Um beide Spalten zu ändern, trennst du die Zuweisungen durch ein Komma. Daran erkennt `SET`, wo eine endet und die nächste beginnt:

```sql
UPDATE Payments
SET amount = 4, unit_price = 25
WHERE payment_id = 3;
```

### Vergleich mit NULL über =

Die Bedingung `WHERE feld = NULL` trifft auf keine Zeile zu, deshalb ändert die Query nichts. Um auf einen leeren Wert zu prüfen, verwende `IS NULL`.

## Prüf dich selbst

Bei einigen Nutzern in der Tabelle `Users` fehlt das Datum der E-Mail-Bestätigung: Das Feld `email_verified_at` ist `NULL`. Wie viele Zeilen ändert diese Query?

```sql
UPDATE Users
SET email_verified_at = '2024-01-01'
WHERE email_verified_at = NULL;
```

1. Alle Zeilen, in denen email_verified_at NULL ist — Ein Vergleich mit NULL über = ergibt nie true, deshalb trifft die Bedingung nicht einmal auf diese Zeilen zu. Um sie zu finden, schreib IS NULL.

2. Alle Zeilen der Tabelle — Die Query hat eine WHERE-Bedingung, sie trifft nur auf keine Zeile zu. Alle Zeilen würde eine Query ganz ohne WHERE ändern.

3. **Richtige Antwort:** Keine — Die Bedingung email_verified_at = NULL trifft auf keine Zeile zu, auch nicht auf die, in denen der Wert tatsächlich leer ist. Für diese Prüfung brauchst du IS NULL.
