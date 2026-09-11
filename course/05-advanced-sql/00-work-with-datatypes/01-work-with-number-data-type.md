---
meta:
    title: "Numerischer Datentyp in SQL: Mathematische Funktionen, Rundung, ROUND, TRUNCATE"
    description: "Arbeiten mit Zahlen in SQL: mathematische Funktionen ROUND, TRUNCATE, CEILING, FLOOR, ABS, SIGN. Zahlen runden, Genauigkeit steuern in MySQL und PostgreSQL."
---

# Numerischer Datentyp in SQL

Numerische Daten in SQL zu erzeugen ist recht einfach: Man kann eine Zahl als Literal eingeben, sie aus einer Tabellenspalte holen oder sie durch Berechnungen erzeugen.

Bei Berechnungen kannst du alle Standard-Rechenoperationen verwenden (`+`, `-`, `*`, `/` usw.) und die Auswertungsreihenfolge mit Klammern ändern.

```sql
SELECT 2 * ((22 - 16) / (2 + 1)) AS calc_example;
```

| calc_example |
| ------------ |
| 4            |

## Mathematische Funktionen

Für die meisten mathematischen Berechnungen, etwa zum Potenzieren einer Zahl oder zum Ziehen der Quadratwurzel, gibt es in SQL eingebaute numerische Funktionen. Hier einige Beispiele für solche Funktionen:

**MySQL**

| Funktionsname                                                                                 | Beschreibung                                               |
| :-------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| <a href="https://sql-academy.org/de/handbook/mysql/pow" target="_blank">`POW(num, power)`</a> | Berechnet eine Zahl in der angegebenen Potenz              |
| <a href="https://sql-academy.org/de/handbook/mysql/sqrt" target="_blank">`SQRT(num)`</a>      | Berechnet die Quadratwurzel einer Zahl                     |
| <a href="https://sql-academy.org/de/handbook/mysql/log" target="_blank">`LOG(base, num)`</a>  | Berechnet den Logarithmus einer Zahl zur angegebenen Basis |
| <a href="https://sql-academy.org/de/handbook/mysql/exp" target="_blank">`EXP(num)`</a>        | Berechnet e<sup>num</sup>                                  |
| <a href="https://sql-academy.org/de/handbook/mysql/sin" target="_blank">`SIN(num)`</a>        | Berechnet den Sinus einer Zahl                             |
| <a href="https://sql-academy.org/de/handbook/mysql/cos" target="_blank">`COS(num)`</a>        | Berechnet den Kosinus einer Zahl                           |
| <a href="https://sql-academy.org/de/handbook/mysql/tan" target="_blank">`TAN(num)`</a>        | Berechnet den Tangens einer Zahl                           |

Eine Liste aller numerischen Funktionen mit Beschreibung und Beispielen findest du im <a href="https://sql-academy.org/de/handbook/mysql/abs" target="_blank">Handbuch</a>.

**PostgreSQL**

| Funktionsname                                                                                          | Beschreibung                                               |
| :----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| <a href="https://sql-academy.org/de/handbook/postgresql/power" target="_blank">`POWER(num, power)`</a> | Berechnet eine Zahl in der angegebenen Potenz              |
| <a href="https://sql-academy.org/de/handbook/postgresql/sqrt" target="_blank">`SQRT(num)`</a>          | Berechnet die Quadratwurzel einer Zahl                     |
| <a href="https://sql-academy.org/de/handbook/postgresql/log" target="_blank">`LOG(base, num)`</a>      | Berechnet den Logarithmus einer Zahl zur angegebenen Basis |
| <a href="https://sql-academy.org/de/handbook/postgresql/exp" target="_blank">`EXP(num)`</a>            | Berechnet e<sup>num</sup>                                  |
| <a href="https://sql-academy.org/de/handbook/postgresql/sin" target="_blank">`SIN(num)`</a>            | Berechnet den Sinus einer Zahl                             |
| <a href="https://sql-academy.org/de/handbook/postgresql/cos" target="_blank">`COS(num)`</a>            | Berechnet den Kosinus einer Zahl                           |
| <a href="https://sql-academy.org/de/handbook/postgresql/tan" target="_blank">`TAN(num)`</a>            | Berechnet den Tangens einer Zahl                           |

Eine Liste aller numerischen Funktionen mit Beschreibung und Beispielen findest du im <a href="https://sql-academy.org/de/handbook/postgresql/ceil" target="_blank">Handbuch</a>.

## Zahlen runden

Beim Arbeiten mit Gleitkommazahlen muss man die Zahlen nicht immer mit voller Genauigkeit speichern oder anzeigen. So lassen sich Geldtransaktionen mit einer Genauigkeit von 6 Nachkommastellen speichern, aber mit 2 Nachkommastellen anzeigen, also bis auf den Cent genau.

**MySQL**

Zum Runden numerischer Daten gibt es in SQL die folgenden 4 Funktionen: `CEILING`, `FLOOR`, `ROUND`, `TRUNCATE`.

Die Funktionen `CEILING` und `FLOOR` runden eine Zahl auf die nächste ganze Zahl auf bzw. ab.

```sql
SELECT CEILING(69.69) AS ceiling, FLOOR(69.69) AS floor;
```

**PostgreSQL**

Zum Runden numerischer Daten gibt es in SQL die folgenden 4 Funktionen: `CEIL`, `FLOOR`, `ROUND`, `TRUNC`.

Die Funktionen `CEIL` und `FLOOR` runden eine Zahl auf die nächste ganze Zahl auf bzw. ab.

```sql
SELECT CEIL(69.69) AS ceiling, FLOOR(69.69) AS floor;
```

| ceiling | floor |
| ------- | ----- |
| 70      | 69    |

Zum Runden auf die nächste ganze Zahl gibt es die Funktion `ROUND`, die jede Zahl, deren Nachkommateil größer oder gleich 0,5 ist, aufrundet und andernfalls abrundet.

```sql
SELECT ROUND(69.499), ROUND(69.5), ROUND(69.501);
```

| ROUND(69.499) | ROUND(69.5) | ROUND(69.501) |
| ------------- | ----------- | ------------- |
| 69            | 70          | 70            |

Die Funktion `ROUND` erlaubt es außerdem, eine Zahl auf eine bestimmte Anzahl von Nachkommastellen zu runden. Dafür nimmt die Funktion als zweites optionales Argument die Anzahl der zu behaltenden Nachkommastellen entgegen.

```sql
SELECT ROUND(69.7171,1), ROUND(69.7171,2), ROUND(69.7171,3);
```

| ROUND(69.7171,1) | ROUND(69.7171,2) | ROUND(69.7171,3) |
| ---------------- | ---------------- | ---------------- |
| 69.7             | 69.72            | 69.717           |

Das zweite Argument der Funktion `ROUND` kann auch negative Werte annehmen. In diesem Fall werden die Ziffern links vom Dezimalpunkt in der durch das Argument angegebenen Anzahl auf Null gesetzt, und der Nachkommateil wird abgeschnitten.

```sql
SELECT ROUND(1691.7,-1), ROUND(1691.7,-2), ROUND(1691.7,-3);
```

| ROUND(1691.7,-1) | ROUND(1691.7,-2) | ROUND(1691.7,-3) |
| ---------------- | ---------------- | ---------------- |
| 1690             | 1700             | 2000             |

**MySQL**

Die Funktion `TRUNCATE` ist analog zur Funktion `ROUND`, sie kann ebenfalls einen 2. optionalen Parameter entgegennehmen, nur dass sie statt zu runden einfach die nicht benötigten Ziffern abschneidet.

```sql
SELECT TRUNCATE(69.7979,1), TRUNCATE(69.7979,2), TRUNCATE(69.7979,3);
```

| TRUNCATE(69.7979,1) | TRUNCATE(69.7979,2) | TRUNCATE(69.7979,3) |
| ------------------- | ------------------- | ------------------- |
| 69.7                | 69.79               | 69.797              |

Und was liefert der folgende Ausdruck?

```sql
SELECT TRUNCATE(69.7979, -1);
```

**PostgreSQL**

Die Funktion `TRUNC` ist analog zur Funktion `ROUND`, sie kann ebenfalls einen 2. optionalen Parameter entgegennehmen, nur dass sie statt zu runden einfach die nicht benötigten Ziffern abschneidet.

```sql
SELECT TRUNC(69.7979,1), TRUNC(69.7979,2), TRUNC(69.7979,3);
```

| TRUNC(69.7979,1) | TRUNC(69.7979,2) | TRUNC(69.7979,3) |
| ---------------- | ---------------- | ---------------- |
| 69.7             | 69.79            | 69.797           |

Und was liefert der folgende Ausdruck?

```sql
SELECT TRUNC(69.7979, -1);
```

1. 69.7 — Bekommt die Funktion einen negativen Wert als zweites Argument, werden die Ziffern links vom Dezimalpunkt in der durch das Argument angegebenen Anzahl auf Null gesetzt, und der Nachkommateil wird abgeschnitten.

2. 69 — Bekommt die Funktion einen negativen Wert als zweites Argument, werden die Ziffern links vom Dezimalpunkt in der durch das Argument angegebenen Anzahl auf Null gesetzt, und der Nachkommateil wird abgeschnitten.

3. **Richtige Antwort:** 60 — Gut gemacht, du hast vollkommen recht!

## Arbeiten mit vorzeichenbehafteten Zahlen

Beim Arbeiten mit numerischen Daten, in denen negative Werte vorkommen können, können die Funktionen `SIGN` und `ABS` hilfreich sein.

Die Funktion `SIGN` gibt den Wert `-1` zurück, wenn die Zahl negativ ist, `0`, wenn die Zahl null ist, und `1`, wenn die Zahl positiv ist.

```sql
SELECT SIGN(-69), SIGN(0), SIGN(69);
```

| SIGN(-69) | SIGN(0) | SIGN(69) |
| --------- | ------- | -------- |
| -1        | 0       | 1        |

Die Funktion `ABS` gibt den Absolutwert einer Zahl zurück.

```sql
SELECT ABS(-69), ABS(0), ABS(69);
```

| ABS(-69) | ABS(0) | ABS(69) |
| -------- | ------ | ------- |
| 69       | 0      | 69      |
