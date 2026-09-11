---
meta:
    title: "Datum und Uhrzeit in SQL: Funktionen YEAR, MONTH, TIMESTAMPDIFF, STR_TO_DATE"
    description: "Arbeiten mit Datum und Uhrzeit in SQL: Funktionen YEAR, MONTH, DAY, TIMESTAMPDIFF, STR_TO_DATE, EXTRACT. Datentypen DATE, TIME, DATETIME, TIMESTAMP in MySQL und PostgreSQL."
---

# Datum und Uhrzeit in SQL

Von allen Datentypen in SQL sind die Zeit-Datentypen die kompliziertesten. Die Komplexität ergibt sich aus mehreren Gründen, und hier sind einige davon:

- eine Vielzahl von Möglichkeiten, Datum und Uhrzeit anzugeben
- das Vorhandensein von Zeitzonen
- die Unklarheit bei der Berechnung bestimmter Werte auf Basis von Zeitdaten. Zum Beispiel die Schwierigkeit, das Alter zu berechnen.

## Zeitdaten erzeugen

Zeitdaten lassen sich auf eine der folgenden Arten gewinnen:

- Daten aus einer vorhandenen Spalte mit einem Zeit-Datentyp kopieren
- Datum und Uhrzeit über eine Zeichenkettendarstellung angeben
- Zeitdaten durch den Aufruf eingebauter Funktionen erhalten, die einen Zeittyp zurückgeben

### Zeichenkettendarstellung von Zeitdaten

Für die Angabe von Datum und Uhrzeit werden folgende Formate verwendet:

**MySQL**

| Typ         | Standardformat                                                                                                                                                                     |
| :---------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DATE`      | `YYYY-MM-DD`                                                                                                                                                                       |
| `DATETIME`  | `YYYY-MM-DD hh:mm:ss`                                                                                                                                                              |
| `TIMESTAMP` | `YYYY-MM-DD hh:mm:ss`                                                                                                                                                              |
| `TIME`      | `hhh:mm:sss`                                                                                                                                                                       |
| `YEAR`      | `YYYY` - vollständiges Format <br /> `YY` oder `Y` - Kurzformat, das für Werte 0-69 ein Jahr zwischen 2000 und 2069 und für Werte 70-99 ein Jahr zwischen 1970 und 1999 zurückgibt |

**PostgreSQL**

| Typ         | Standardformat        |
| :---------- | :-------------------- |
| `DATE`      | `YYYY-MM-DD`          |
| `TIMESTAMP` | `YYYY-MM-DD hh:mm:ss` |
| `TIME`      | `hh:mm:ss`            |

Dabei darf bei der Angabe des Datums jedes beliebige Satzzeichen als Trennzeichen zwischen den Teilen des Datums oder der Uhrzeit verwendet werden. Es ist auch möglich, das Datum ganz ohne Trennzeichen zusammengeschrieben anzugeben.

Beispiele für gültige Angaben von Zeitwerten über die Zeichenkettendarstellung:

**MySQL**

```sql
SELECT  CAST("2022-06-16 16:37:23" AS DATETIME) AS datetime_1,
        CAST("2014/02/22 16*37*22" AS DATETIME) AS datetime_2,
        CAST("20220616163723" AS DATETIME) AS datetime_3,
        CAST("2021-02-12" AS DATE) AS date_1,
        CAST("160:23:13" AS TIME) AS time_1,
        CAST("89" AS YEAR) AS year
```

| datetime_1               | datetime_2               | datetime_3               | date_1                   | time_1    | year |
| ------------------------ | ------------------------ | ------------------------ | ------------------------ | --------- | ---- |
| 2022-06-16T16:37:23.000Z | 2014-02-22T16:37:22.000Z | 2022-06-16T16:37:23.000Z | 2021-02-12T00:00:00.000Z | 160:23:13 | 1989 |

**PostgreSQL**

```sql
SELECT  CAST('2022-06-16 16:37:23' AS TIMESTAMP) AS timestamp_1,
        CAST('2014/02/22 16:37:22' AS TIMESTAMP) AS timestamp_2,
        CAST('20220616163723' AS TIMESTAMP) AS timestamp_3,
        CAST('2021-02-12' AS DATE) AS date_1,
        CAST('16:23:13' AS TIME) AS time_1
```

| timestamp_1              | timestamp_2              | timestamp_3              | date_1                   | time_1   |
| ------------------------ | ------------------------ | ------------------------ | ------------------------ | -------- |
| 2022-06-16T16:37:23.000Z | 2014-02-22T16:37:22.000Z | 2022-06-16T16:37:23.000Z | 2021-02-12T00:00:00.000Z | 16:23:13 |

In der Abfrage oben wurde die Funktion `CAST` verwendet, um die Zeichenkette zwangsweise in ein Datum und eine Uhrzeit umzuwandeln. Sie ist nötig, wenn der Server keinen Zeitwert erwartet und die Zeichenkette deshalb nicht automatisch in den gewünschten Typ umwandelt. Mit der Typkonvertierung beschäftigen wir uns ausführlicher im Artikel <a href="https://sql-academy.org/de/guide/type-conversion-functions" target="_blank">„Funktionen zur Typkonvertierung, CAST“</a>.

### Funktionen zur Erzeugung von Datumswerten

Wenn man Zeitdaten aus einer Zeichenkette gewinnen muss, die keinem der Formate entspricht, die die Funktion `CAST` akzeptiert, kann man spezielle Funktionen zum Parsen von Datumswerten verwenden.

**MySQL**

In MySQL gibt es die eingebaute Funktion `STR_TO_DATE`, die eine beliebige Zeichenkette mit einem Datum und ein Format, das sie beschreibt, entgegennimmt.

```sql
SELECT STR_TO_DATE('November 13, 1998', '%M %d, %Y') AS date;
```

| date                     |
| ------------------------ |
| 1998-11-13T00:00:00.000Z |

Eine ausführlichere Beschreibung der Funktion `STR_TO_DATE` und ihrer Argumente findest du <a href="https://sql-academy.org/de/handbook/mysql/str_to_date" target="_blank">im Handbuch</a>.

**PostgreSQL**

In PostgreSQL gibt es die eingebaute Funktion `TO_DATE`, die eine beliebige Zeichenkette mit einem Datum und ein Format, das sie beschreibt, entgegennimmt.

```sql
SELECT TO_DATE('November 13, 1998', 'Month DD, YYYY') AS date;
```

| date                     |
| ------------------------ |
| 1998-11-13T00:00:00.000Z |

Eine ausführlichere Beschreibung der Funktion `TO_DATE` und ihrer Argumente findest du <a href="https://sql-academy.org/de/handbook/postgresql/to_date" target="_blank">im Handbuch</a>.

Für die Erzeugung des aktuellen Datums oder der aktuellen Uhrzeit braucht man keine Zeichenkette zu erstellen, die anschließend in ein Datum umgewandelt wird, denn es gibt eingebaute Funktionen, um diese Werte zu bekommen.

**MySQL**

In MySQL sind das die Funktionen `CURDATE`, `CURTIME` und `NOW`.

```sql
SELECT CURDATE(), CURTIME(), NOW();
```

**PostgreSQL**

In PostgreSQL sind das die Funktionen `CURRENT_DATE`, `CURRENT_TIME` und `NOW`.

```sql
SELECT CURRENT_DATE, CURRENT_TIME, NOW();
```

## Funktionen zum Extrahieren von Zeitdaten

Manchmal brauchen wir nicht das gesamte Datum, sondern nur einen bestimmten Teil davon, zum Beispiel den Monat oder das Jahr.

**MySQL**

Dafür gibt es in SQL folgende Funktionen:

| Funktion                                                                                | Beschreibung                                                     |
| :-------------------------------------------------------------------------------------- | :--------------------------------------------------------------- |
| <a href="https://sql-academy.org/de/handbook/mysql/year" target="_blank">`YEAR`</a>     | Gibt das Jahr des angegebenen Datums zurück                      |
| <a href="https://sql-academy.org/de/handbook/mysql/month" target="_blank">`MONTH`</a>   | Gibt den numerischen Monatswert (von 1 bis 12) des Datums zurück |
| <a href="https://sql-academy.org/de/handbook/mysql/day" target="_blank">`DAY`</a>       | Gibt die Tagesnummer im Monat (von 1 bis 31) zurück              |
| <a href="https://sql-academy.org/de/handbook/mysql/hour" target="_blank">`HOUR`</a>     | Gibt den Stundenwert (von 0 bis 23) der Uhrzeit zurück           |
| <a href="https://sql-academy.org/de/handbook/mysql/minute" target="_blank">`MINUTE`</a> | Gibt den Minutenwert (von 0 bis 59) der Uhrzeit zurück           |

**PostgreSQL**

Dafür wird in PostgreSQL die Funktion `EXTRACT` verwendet:

| Funktion                                                                                                         | Beschreibung                                                     |
| :--------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------- |
| <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT(YEAR FROM date)`</a>   | Gibt das Jahr des angegebenen Datums zurück                      |
| <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT(MONTH FROM date)`</a>  | Gibt den numerischen Monatswert (von 1 bis 12) des Datums zurück |
| <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT(DAY FROM date)`</a>    | Gibt die Tagesnummer im Monat (von 1 bis 31) zurück              |
| <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT(HOUR FROM time)`</a>   | Gibt den Stundenwert (von 0 bis 23) der Uhrzeit zurück           |
| <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT(MINUTE FROM time)`</a> | Gibt den Minutenwert (von 0 bis 59) der Uhrzeit zurück           |

**MySQL**

## Unterschied zwischen DATETIME und TIMESTAMP

Es gibt zwei einander sehr ähnliche Datentypen: `DATETIME` und `TIMESTAMP`. Beide sind für die Speicherung von Datum und Uhrzeit gedacht, haben aber einige Unterschiede, die ihre Einsatzgebiete bestimmen.

| Kriterium | `DATETIME`                                                                      | `TIMESTAMP`                                                                                                |
| :-------- | :------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------- |
| Bereich   | von `1000-01-01 00:00:00` <br /> bis `9999-12-31 23:59:59`                      | von `1970-01-01 00:00:00` <br /> bis `2038-01-19 03:14:07`                                                 |
| Zeitzone  | Wird nicht berücksichtigt <br /> Wird so angezeigt, wie das Datum gesetzt wurde | Wird berücksichtigt <br /> Wird bei Abfragen unter Berücksichtigung der aktuellen Serverzeitzone angezeigt |

**PostgreSQL**

## Unterschied zwischen TIMESTAMP und TIMESTAMPTZ

Die wichtigsten Typen für die Speicherung von Datum und Uhrzeit sind `TIMESTAMP` (ohne Zeitzone) und `TIMESTAMPTZ` (mit Zeitzone).

| Kriterium | `TIMESTAMP`                                                                     | `TIMESTAMPTZ`                                                                                              |
| :-------- | :------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------- |
| Bereich   | von `4713 BC` bis `294276 AD`                                                   | von `4713 BC` bis `294276 AD`                                                                              |
| Zeitzone  | Wird nicht berücksichtigt <br /> Wird so angezeigt, wie das Datum gesetzt wurde | Wird berücksichtigt <br /> Wird bei Abfragen unter Berücksichtigung der aktuellen Serverzeitzone angezeigt |

## Zeitzonen

Da Menschen auf der ganzen Welt wollen, dass der Mittag in etwa dem höchsten Sonnenstand entspricht, gab es nie das Ziel, eine universelle Zeit zu nutzen, und die Welt wurde in 24 Zeitzonen aufgeteilt.

Als Bezugspunkt der Zeit wird UTC (Coordinated Universal Time) verwendet. Alle anderen Zeitzonen lassen sich über die Anzahl der Stunden Versatz zu UTC beschreiben. So lässt sich die Zeitzone von Moskau zum Beispiel als UTC+3 beschreiben.

Die Zeitzone ist eine der Einstellungen des Datenbankservers und kann festgelegt werden:

- global
- für den aktuellen Benutzer
- für die aktuelle Benutzersitzung

**MySQL**

```sql
SET GLOBAL time_zone = '+03:00';    // global
SET time_zone = '+03:00';           // für den aktuellen Benutzer
SET @@session.time_zone = '+03:00'; // für die aktuelle Benutzersitzung
```

Wird die Zeitzone geändert, werden entsprechend alle Werte vom Typ `TIMESTAMP` unter Berücksichtigung der aktuell aktiven Zeitzone ausgegeben.

**PostgreSQL**

```sql
ALTER DATABASE mydb SET timezone = 'Europe/Moscow';  // global für die Datenbank
ALTER USER myuser SET timezone = 'Europe/Moscow';    // für einen bestimmten Benutzer
SET TIME ZONE 'Europe/Moscow';                       // für die aktuelle Sitzung
SET TIME ZONE '+03:00';                              // für die aktuelle Sitzung
```

Wird die Zeitzone geändert, werden entsprechend alle Werte vom Typ `TIMESTAMPTZ` unter Berücksichtigung der aktuell aktiven Zeitzone ausgegeben.

## Beispiele für Aufgaben mit Datum und Uhrzeit

Wir möchten gesondert auf die häufigsten Aufgaben rund um den Zeit-Datentyp eingehen, bei denen oft Fehler gemacht werden.

### Alter bestimmen

Wenn die Aufgabe lautet, das Alter einer Person anhand ihres Geburtsdatums zu ermitteln, ist man oft versucht, die Differenz zwischen dem aktuellen Jahr und dem Geburtsjahr zu berechnen:

**MySQL**

```sql
SELECT YEAR(NOW()) - YEAR('2003-07-03 14:10:26');
```

**PostgreSQL**

```sql
SELECT EXTRACT(YEAR FROM NOW()) - EXTRACT(YEAR FROM TIMESTAMP '2003-07-03 14:10:26');
```

Das Problem dieses Ansatzes ist, dass er nicht berücksichtigt, ob die Person ihren Geburtstag in diesem Jahr schon gehabt hat oder nicht. Das heißt, ist zum Zeitpunkt der Abfrage der 3. Juli (07-03) bereits eingetreten, hat die Person ihren Geburtstag gefeiert und ist bereits 20 Jahre alt, andernfalls ist sie nach wie vor 19. Die Differenz der Funktionen ist also nicht hilfreich — in beiden Fällen ergibt sie 20 Jahre.

Wenn das Alter über die Jahresdifferenz zu bestimmen also nicht funktioniert, könnte der Wunsch entstehen, das Alter über die Tagesdifferenz zwischen zwei Daten zu ermitteln, diese Differenz dann durch die Anzahl der Tage im Jahr zu teilen und abzurunden:

**MySQL**

```sql
SELECT FLOOR(DATEDIFF(NOW(), '2003-07-03 14:10:26') / 365);
```

**PostgreSQL**

```sql
SELECT FLOOR(EXTRACT(DAY FROM NOW() - TIMESTAMP '2003-07-03 14:10:26') / 365);
```

Diese Lösung ist deutlich genauer als die vorherige. Sie ist aber nicht absolut genau, weil es Schaltjahre gibt, in denen das Jahr 366 Tage hat. Auch wenn der Fehler bei der Altersberechnung für eine einzelne Person aufgrund der Schaltjahre recht gering ist, kann sich dieser Fehler bei Berechnungen, etwa der Bestimmung des Durchschnittsalters für eine bestimmte Liste von Personen, summieren und die tatsächlichen Werte verzerren.

Wie bestimmt man das Alter dann korrekt?

**MySQL**

Dafür gibt es eine fertige eingebaute Funktion — <a href="https://sql-academy.org/de/handbook/mysql/timestampdiff" target="_blank">`TIMESTAMPDIFF`</a>, die als erstes Argument die Maßeinheit entgegennimmt, in der die Differenz zwischen zwei Zeitwerten zurückgegeben werden soll.

```sql
SELECT TIMESTAMPDIFF(YEAR, '2003-07-03 14:10:26', NOW());
```

**PostgreSQL**

Dafür wird die Funktion <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">`EXTRACT`</a> zusammen mit der Funktion <a href="https://sql-academy.org/de/handbook/postgresql/age" target="_blank">`AGE`</a> verwendet, die das genaue Intervall zwischen zwei Datumswerten berechnet.

```sql
SELECT EXTRACT(YEAR FROM AGE(NOW(), TIMESTAMP '2003-07-03 14:10:26'));
```
