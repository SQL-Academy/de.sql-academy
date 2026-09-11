---
meta:
    title: "Der WHERE-Operator"
    description: "Der WHERE-Operator in SQL-Queries. Logische Operatoren und Vergleichsoperatoren. Beispiele für SELECT FROM WHERE"
---

# Der WHERE-Operator

Die Situation, in der wir Daten nach einer bestimmten Bedingung filtern wollen,
kommt sehr häufig vor. Dafür gibt es im `SELECT`-Statement den Operator `WHERE`, gefolgt von
den Bedingungen, die die Zeilen einschränken. Erfüllt ein Datensatz die Bedingung,
landet er im Ergebnis – sonst wird er verworfen.

## Allgemeine Struktur einer Query mit WHERE

```sql
SELECT [DISTINCT] tabellen_felder FROM tabellen_name
WHERE bedingung_zur_zeilen_einschraenkung
[logischer_operator weitere_bedingung_zur_zeilen_einschraenkung];
```

So kann zum Beispiel eine Query mit `WHERE` aussehen:

**MySQL**

```sql
SELECT * FROM Student
WHERE first_name = "Grigorij" AND YEAR(birthday) > 2000;
```

**PostgreSQL**

```sql
SELECT * FROM Student
WHERE first_name = 'Grigorij' AND EXTRACT(YEAR FROM birthday) > 2000;
```

| id  | first_name | middle_name | last_name | birthday                 | address                         |
| --- | ---------- | ----------- | --------- | ------------------------ | ------------------------------- |
| 33  | Grigorij   | Gennadevich | Kapustin  | 2001-12-13T00:00:00.000Z | ul. Pervomajskaya, d. 45, kv. 6 |
| 65  | Grigorij   | Kirillovich | Kolobov   | 2003-07-17T00:00:00.000Z | ul. CHernova, d. 9, kv. 34      |

In diesem Beispiel verwenden wir gleich zwei Bedingungen:

**MySQL**

- `first_name = "Grigorij"` — der Vorname des Studenten soll „Grigorij" sein
- `YEAR(birthday) > 2000` — das Geburtsjahr liegt nach 2000

**PostgreSQL**

- `first_name = 'Grigorij'` — der Vorname des Studenten soll „Grigorij" sein
- `EXTRACT(YEAR FROM birthday) > 2000` — das Geburtsjahr liegt nach 2000

Zwischen den beiden steht der logische Operator `AND`, der verlangt, dass beide Bedingungen gleichzeitig erfüllt sind. Im Ergebnis bekommen wir nur Studenten, auf die beide Kriterien zutreffen.

## Vergleichsoperatoren

Um in SQL Werte zu vergleichen, gibt es spezielle Operatoren. Mit ihnen prüfst du, ob Werte gleich sind, ob einer größer ist als der andere, ob sie ungleich sind und so weiter. Das Ergebnis eines Vergleichs kann sein:

- `true` (entspricht 1)
- `false` (entspricht 0)
- `NULL` (wenn das Vergleichsergebnis undefiniert ist, z. B. beim Vergleich mit NULL)

**MySQL**

| Operator                | Symbol         | Beschreibung                                                                                                                                  |
| :---------------------- | :------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Gleichheit              | `=`            | Sind beide Werte gleich, ist das Ergebnis 1, sonst 0                                                                                          |
| NULL-sichere Gleichheit | `<=>`          | Wie der Gleichheitsoperator, aber das Ergebnis ist 1 beim Vergleich `NULL` mit `NULL` und 0 beim Vergleich eines beliebigen Wertes mit `NULL` |
| Ungleichheit            | `<>` oder `!=` | Sind beide Werte ungleich, ist das Ergebnis 1, sonst 0                                                                                        |
| Kleiner                 | `<`            | Ist ein Wert kleiner als der andere, ist das Ergebnis 1, sonst 0                                                                              |
| Kleiner oder gleich     | `<=`           | Ist ein Wert kleiner oder gleich dem anderen, ist das Ergebnis 1, sonst 0                                                                     |
| Größer                  | `>`            | Ist ein Wert größer als der andere, ist das Ergebnis 1, sonst 0                                                                               |
| Größer oder gleich      | `>=`           | Ist ein Wert größer oder gleich dem anderen, ist das Ergebnis 1, sonst 0                                                                      |

> Der Vergleich eines beliebigen Wertes mit `NULL` liefert `NULL`. Eine Ausnahme bildet der NULL-sichere Gleichheitsoperator.

**PostgreSQL**

| Operator            | Symbol         | Beschreibung                                                                     |
| :------------------ | :------------- | -------------------------------------------------------------------------------- |
| Gleichheit          | `=`            | Sind beide Werte gleich, ist das Ergebnis true, sonst false                      |
| Ungleichheit        | `<>` oder `!=` | Sind beide Werte ungleich, ist das Ergebnis true, sonst false                    |
| Kleiner             | `<`            | Ist ein Wert kleiner als der andere, ist das Ergebnis true, sonst false          |
| Kleiner oder gleich | `<=`           | Ist ein Wert kleiner oder gleich dem anderen, ist das Ergebnis true, sonst false |
| Größer              | `>`            | Ist ein Wert größer als der andere, ist das Ergebnis true, sonst false           |
| Größer oder gleich  | `>=`           | Ist ein Wert größer oder gleich dem anderen, ist das Ergebnis true, sonst false  |

> Der Vergleich eines beliebigen Wertes mit `NULL` liefert `NULL`.

Probier diese Operatoren ruhig selbst in der Sandbox aus und schau, welche Ergebnisse rauskommen:

**MySQL**

```sql
SELECT
    2 = 1,
	'a' = 'a',
    1 <=> NULL,
	NULL <=> NULL,
	2 <> 2,
	3 < 4,
	10 <= 10,
	7 > 1,
	8 >= 10;
```

| 2 = 1 | 'a' = 'a' | 1 \<=> NULL | NULL \<=> NULL | 2 \<> 2 | 3 \< 4 | 10 \<= 10 | 7 > 1 | 8 >= 10 |
| ----- | --------- | ----------- | -------------- | ------- | ------ | --------- | ----- | ------- |
| 0     | 1         | 0           | 1              | 0       | 1      | 1         | 1     | 0       |

**PostgreSQL**

```sql
SELECT
    2 = 1 AS "2 = 1",
	'a' = 'a' AS "'a' = 'a'",
	2 <> 2 AS "2 <> 2",
	3 < 4 AS "3 < 4",
	10 <= 10 AS "10 <= 10",
	7 > 1 AS "7 > 1",
	8 >= 10 AS "8 >= 10";
```

| 2 = 1 | 'a' = 'a' | 2 \<> 2 | 3 \< 4 | 10 \<= 10 | 7 > 1 | 8 >= 10 |
| ----- | --------- | ------- | ------ | --------- | ----- | ------- |
| false | true      | false   | true   | true      | true  | false   |

## Logische Operatoren

Logische Operatoren helfen dir, mehrere Bedingungen in einer SQL-Query zu kombinieren. Mit ihnen kannst du flexibel genau die Zeilen herauspicken, die du brauchst. Sehen wir uns das in der Praxis an:

- `AND` — beide Bedingungen müssen erfüllt sein.

    Stell dir vor, du suchst Flüge, die gleichzeitig zwei Anforderungen erfüllen: zum Beispiel ein bestimmtes Flugzeugmodell und ein bestimmter Abflugort. `AND` verknüpft diese Bedingungen.

    **MySQL**

    ```sql
    SELECT * FROM Trip
    WHERE plane = 'Boeing' AND town_from = 'London';
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Trip
    WHERE plane = 'Boeing' AND town_from = 'London';
    ```

    | id   | company | plane  | town_from | town_to   | time_out                 | time_in                  |
    | ---- | ------- | ------ | --------- | --------- | ------------------------ | ------------------------ |
    | 7771 | 5       | Boeing | London    | Singapore | 1900-01-01T01:00:00.000Z | 1900-01-01T11:00:00.000Z |
    | 7773 | 5       | Boeing | London    | Singapore | 1900-01-01T03:00:00.000Z | 1900-01-01T13:00:00.000Z |
    | 7775 | 5       | Boeing | London    | Singapore | 1900-01-01T09:00:00.000Z | 1900-01-01T20:00:00.000Z |
    | 7777 | 5       | Boeing | London    | Singapore | 1900-01-01T18:00:00.000Z | 1900-01-02T06:00:00.000Z |
    | 8881 | 5       | Boeing | London    | Paris     | 1900-01-01T03:00:00.000Z | 1900-01-01T04:00:00.000Z |

    Diese Query liefert nur Flüge, bei denen das Flugzeugmodell `Boeing` und der Abflugort `London` ist.

    Wenn auch nur eine der Bedingungen nicht erfüllt ist (z. B. das Flugzeug ist kein `Boeing` oder der Abflug ist nicht aus `London`), landet der Flug nicht im Ergebnis.

- `OR` — es reicht, wenn mindestens eine Bedingung erfüllt ist.

    `OR` funktioniert wie ein „oder". Ist mindestens eine Bedingung wahr, landet die Zeile im Ergebnis. Praktisch, wenn du alle Flüge sehen willst, die mindestens eines deiner Kriterien erfüllen.

    **MySQL**

    ```sql
    SELECT * FROM Trip
    WHERE town_to = 'Paris' OR plane = 'Airbus';
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Trip
    WHERE town_to = 'Paris' OR plane = 'Airbus';
    ```

    | id   | company | plane  | town_from | town_to | time_out                 | time_in                  |
    | ---- | ------- | ------ | --------- | ------- | ------------------------ | ------------------------ |
    | 1100 | 4       | Boeing | Rostov    | Paris   | 1900-01-01T14:30:00.000Z | 1900-01-01T17:50:00.000Z |
    | 8881 | 5       | Boeing | London    | Paris   | 1900-01-01T03:00:00.000Z | 1900-01-01T04:00:00.000Z |

    Das Ergebnis enthält alle Flüge nach `Paris` sowie alle Flüge mit einem `Airbus` (selbst wenn sie nicht nach `Paris` fliegen).

    Ist ein Flug sowohl mit `Airbus` als auch nach `Paris`, landet er ebenfalls im Ergebnis.

- `NOT` — kehrt die Bedingung um.

    `NOT` invertiert die Bedingung: War sie wahr, wird sie falsch – und umgekehrt. Praktisch, wenn du bestimmte Werte ausschließen willst.

    **MySQL**

    ```sql
    SELECT * FROM Trip WHERE NOT town_to = 'Moscow';
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Trip WHERE NOT town_to = 'Moscow';
    ```

    Diese Query liefert alle Flüge, deren Zielort **nicht** `Moscow` ist.

    Ist der Zielort also `Moscow`, landet der Flug nicht im Ergebnis. Alles andere wird zurückgegeben.

- `XOR` — wählt Zeilen aus, bei denen genau eine von zwei Bedingungen erfüllt ist, aber nicht beide gleichzeitig.

    Angenommen, du willst Flüge finden, die entweder aus `Moscow` starten oder in `Paris` landen, aber nicht beides zusammen. Schauen wir uns alle Fälle an:

    | Startet <br />in Moscow | Landet <br />in Paris | Im Ergebnis | Erklärung                                        |
    | :---------------------: | :-------------------: | :---------: | ------------------------------------------------ |
    |           Ja            |         Nein          |     Ja      | Nur die erste Bedingung ist erfüllt              |
    |          Nein           |          Ja           |     Ja      | Nur die zweite Bedingung ist erfüllt             |
    |           Ja            |          Ja           |    Nein     | Beide Bedingungen erfüllt — XOR schließt das aus |
    |          Nein           |         Nein          |    Nein     | Keine Bedingung ist erfüllt                      |

    **MySQL**

    ```sql
    SELECT * FROM trip
    WHERE town_from = 'Moscow' XOR town_to = 'Paris';
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM trip
    WHERE (town_from = 'Moscow' AND town_to != 'Paris')
       OR (town_from != 'Moscow' AND town_to = 'Paris');
    ```

    **MySQL**

    > Hinweis: Den Operator XOR gibt es nicht in allen Datenbanken. Falls er fehlt, kann man ihn mit einer Kombination aus AND und OR nachbauen.

    **PostgreSQL**

    > PostgreSQL hat keinen XOR-Operator, deshalb wird hier eine Kombination aus AND und OR verwendet, um dasselbe Ergebnis zu erzielen.

## Priorität logischer Operatoren

Wenn du eine Query mit mehreren Bedingungen schreibst, muss SQL entscheiden, in welcher Reihenfolge es sie prüft. Das ist wie in der Mathematik: erst Punkt vor Strich. Für die logischen Operatoren in SQL gibt es ebenfalls eine feste Reihenfolge – die sogenannte Priorität.

- Zuerst — `NOT`
- Dann — `AND`
- Danach — `XOR`
- Zum Schluss — `OR`

**Warum ist das wichtig?**

Es mag so wirken, als würden die Bedingungen einfach von links nach rechts geprüft – ist aber nicht so! Wenn du die Reihenfolge ignorierst, bekommst du leicht unerwartete Ergebnisse.

Sieh dir dieses Beispiel an:

**MySQL**

```sql
SELECT *
FROM Trip
WHERE town_to = 'Paris'
	OR plane = 'Boeing'
	AND NOT town_from = 'Moscow';
```

**PostgreSQL**

```sql
SELECT *
FROM Trip
WHERE town_to = 'Paris'
	OR plane = 'Boeing'
	AND NOT town_from = 'Moscow';
```

| id   | company | plane  | town_from | town_to   | time_out                 | time_in                  |
| ---- | ------- | ------ | --------- | --------- | ------------------------ | ------------------------ |
| 1100 | 4       | Boeing | Rostov    | Paris     | 1900-01-01T14:30:00.000Z | 1900-01-01T17:50:00.000Z |
| 1101 | 4       | Boeing | Paris     | Rostov    | 1900-01-01T08:12:00.000Z | 1900-01-01T11:45:00.000Z |
| 7771 | 5       | Boeing | London    | Singapore | 1900-01-01T01:00:00.000Z | 1900-01-01T11:00:00.000Z |
| 7772 | 5       | Boeing | Singapore | London    | 1900-01-01T12:00:00.000Z | 1900-01-02T02:00:00.000Z |
| 7773 | 5       | Boeing | London    | Singapore | 1900-01-01T03:00:00.000Z | 1900-01-01T13:00:00.000Z |
| 7774 | 5       | Boeing | Singapore | London    | 1900-01-01T14:00:00.000Z | 1900-01-02T06:00:00.000Z |
| 7775 | 5       | Boeing | London    | Singapore | 1900-01-01T09:00:00.000Z | 1900-01-01T20:00:00.000Z |
| 7776 | 5       | Boeing | Singapore | London    | 1900-01-01T18:00:00.000Z | 1900-01-02T08:00:00.000Z |
| 7777 | 5       | Boeing | London    | Singapore | 1900-01-01T18:00:00.000Z | 1900-01-02T06:00:00.000Z |
| 7778 | 5       | Boeing | Singapore | London    | 1900-01-01T22:00:00.000Z | 1900-01-02T12:00:00.000Z |
| 8881 | 5       | Boeing | London    | Paris     | 1900-01-01T03:00:00.000Z | 1900-01-01T04:00:00.000Z |
| 8882 | 5       | Boeing | Paris     | London    | 1900-01-01T22:00:00.000Z | 1900-01-01T23:00:00.000Z |

Was passiert hier:

- Zuerst wird geprüft, ob der Flug **nicht** aus `Moscow` startet (`NOT town_from = 'Moscow'`).

- Dann wird geschaut, ob das Flugzeug ein `Boeing` ist, und dies wird per `AND` mit der ersten Bedingung kombiniert. Es werden also Flüge gesucht, die **nicht** aus `Moscow` starten und ein `Boeing` sind (`plane = 'Boeing' AND NOT town_from = 'Moscow'`).

- Danach werden zum Ergebnis alle Flüge hinzugefügt, die in `Paris` landen, auch wenn sie **nicht** mit einem `Boeing` fliegen oder aus `Moscow` starten (`town_to = 'Paris' OR ...`).

Am Ende bekommst du folgende Ergebnismenge:

- Alle Flüge, die **nicht** aus `Moscow` starten **und** ein `Boeing` sind
- Plus alle Flüge, die in `Paris` landen

Wenn du die Reihenfolge der Prüfungen ändern oder den Code verständlicher machen willst, nutze **Klammern**. Was in Klammern steht, wird zuerst ausgewertet. Setzt man zum Beispiel die Klammern entsprechend der Priorität der Operatoren von Hand, wird sofort klar, wie die Query abgearbeitet wird.

**MySQL**

```sql
SELECT *
FROM Trip
WHERE (
		town_to = 'Paris'
		OR (
			plane = 'Boeing'
			AND (NOT town_from = 'Moscow')
		)
	);
```

**PostgreSQL**

```sql
SELECT *
FROM Trip
WHERE (
		town_to = 'Paris'
		OR (
			plane = 'Boeing'
			AND (NOT town_from = 'Moscow')
		)
	);
```

Jetzt bist du dran: Angenommen, du möchtest alle Flüge erhalten, die aus `Paris` starten oder mit einem `Boeing` unterwegs sind, dabei aber **nicht** nach `Moscow` fliegen. Das schaffst du, indem du die Klammern in der Ursprungs-Query richtig setzt. Wie würdest du es machen?

1. town_from = 'Paris' OR (plane = 'Boeing' AND NOT town_to = 'Moscow'); — Diese Variante liefert alle Flüge aus Paris sowie alle Boeing-Flüge, die nicht nach Moscow gehen. Die Klammern sind falsch gesetzt: Flüge aus Paris landen auch dann im Ergebnis, wenn sie nach Moscow fliegen.

2. **Richtige Antwort:** (town_from = 'Paris' OR plane = 'Boeing') AND NOT town_to = 'Moscow'; — Diese Query liefert Flüge, die entweder aus Paris kommen oder mit einem Boeing fliegen und dabei nicht nach Moscow gehen.

3. town_from = 'Paris' OR plane = 'Boeing' AND NOT town_to = 'Moscow'; — Diese Variante ohne Klammern verhält sich nicht wie erwartet: Zuerst wird AND ausgewertet, danach OR. Das führt zu einem falschen Ergebnis.
