---
meta:
    title: "Funktionen verwenden"
    description: "Beispiele für skalare SQL-Funktionen und ihre Anwendung auf Literale und auf Werte aus Tabellenfeldern"
---

# Funktionen verwenden

Beim Schreiben von SQL-Abfragen können wir eingebaute Funktionen nutzen. Wenn wir zum Beispiel eine Zeichenkette in Großbuchstaben ausgeben wollen, können wir dafür die Funktion `UPPER` verwenden.

```sql
SELECT UPPER('Hello world') AS upper_string;
```

| upper_string |
| ------------ |
| HELLO WORLD  |

## Was ist eine eingebaute Funktion?

Eine eingebaute Funktion ist ein im DBMS implementiertes Stück Code, mit dem man in Abfragen Umwandlungen von Zeichenketten-, Zahlen- und anderen Daten durchführen kann.

Jede Funktion nimmt eine Reihe von Argumenten eines bestimmten Typs entgegen, führt die in ihr festgelegten Operationen aus und gibt zwingend eines der möglichen Literale zurück. Anzumerken ist, dass Funktionen sowohl null als auch mehrere Argumente entgegennehmen können.

Zum Beispiel nimmt die Funktion `NOW()` null Argumente entgegen und gibt ein Literal im Datumsformat zurück, und `LENGTH('sql-academy')` nimmt ein Zeichenkettenargument entgegen und gibt das numerische Literal „11“ zurück.

## Beispiele für Funktionen

Es gibt ziemlich viele Funktionen, die wichtigsten findest du jedoch immer über die Suche im Kopfbereich oder auf der <a href="https://sql-academy.org/de/handbook" target="_blank">Seite des Funktionsnachschlagewerks</a>.

Hier sind einige davon:

**MySQL**

- <a href="https://sql-academy.org/de/handbook/mysql/lower" target="_blank">
    **LOWER**
  </a>

    Gibt eine Zeichenkette zurück, in der alle Zeichen in Kleinbuchstaben geschrieben sind

    ```sql
    SELECT LOWER('SQL Academy') AS lower_string;
    ```

    | lower_string |
    | ------------ |
    | sql academy  |

- <a href="https://sql-academy.org/de/handbook/mysql/year" target="_blank">
    **YEAR**
  </a>

    Gibt das Jahr des angegebenen Datums zurück

    ```sql
    SELECT YEAR('2022-06-16') AS year;
    ```

    | year |
    | ---- |
    | 2022 |

- <a href="https://sql-academy.org/de/handbook/mysql/instr" target="_blank">
    **INSTR**
  </a>

    Sucht nach einer Teilzeichenkette innerhalb einer Zeichenkette und gibt die Position
    ihres ersten Zeichens zurück. Dabei beginnt die Zählung bei eins, nicht bei null wie in
    den meisten Programmiersprachen.

    Die Funktion arbeitet, indem sie die Ausgangszeichenkette zeichenweise mit der gesuchten vergleicht. Zum Beispiel kommt in der Zeichenkette `sql-academy` die Teilzeichenkette `academy` ab dem fünften Zeichen vor.

    ```sql
    SELECT INSTR('sql-academy', 'academy') AS idx;
    ```

    | idx |
    | --- |
    | 5   |

- <a href="https://sql-academy.org/de/handbook/mysql/length" target="_blank">
    **LENGTH**
  </a>

    Gibt die Länge der angegebenen Zeichenkette zurück.

    ```sql
    SELECT LENGTH('sql-academy') AS str_length;
    ```

    | str_length |
    | ---------- |
    | 11         |

**PostgreSQL**

- <a href="https://sql-academy.org/de/handbook/postgresql/lower" target="_blank">
    **LOWER**
  </a>

    Gibt eine Zeichenkette zurück, in der alle Zeichen in Kleinbuchstaben geschrieben sind

    ```sql
    SELECT LOWER('SQL Academy') AS lower_string;
    ```

    | lower_string |
    | ------------ |
    | sql academy  |

- <a href="https://sql-academy.org/de/handbook/postgresql/extract" target="_blank">
    **EXTRACT**
  </a>

    Extrahiert einen Teil des Datums (Jahr, Monat, Tag usw.) aus dem angegebenen Datum

    ```sql
    SELECT EXTRACT(YEAR FROM DATE '2022-06-16') AS year;
    ```

    | year |
    | ---- |
    | 2022 |

- <a href="https://sql-academy.org/de/handbook/postgresql/position" target="_blank">
    **POSITION**
  </a>

    Sucht nach einer Teilzeichenkette innerhalb einer Zeichenkette und gibt die Position
    ihres ersten Zeichens zurück. Dabei beginnt die Zählung bei eins, nicht bei null wie in
    den meisten Programmiersprachen.

    Die Funktion arbeitet, indem sie die Ausgangszeichenkette zeichenweise mit der gesuchten vergleicht. Zum Beispiel kommt in der Zeichenkette `sql-academy` die Teilzeichenkette `academy` ab dem fünften Zeichen vor.

    ```sql
    SELECT POSITION('academy' IN 'sql-academy') AS idx;
    ```

    | idx |
    | --- |
    | 5   |

- <a href="https://sql-academy.org/de/handbook/postgresql/length" target="_blank">
    **LENGTH**
  </a>

    Gibt die Länge der angegebenen Zeichenkette zurück.

    ```sql
    SELECT LENGTH('sql-academy') AS str_length;
    ```

    | str_length |
    | ---------- |
    | 11         |

## Funktionen auf Werte aus Tabellenfeldern anwenden

Funktionen lassen sich nicht nur auf Literale anwenden, sondern auch auf Werte aus einer Tabelle. Die Funktion führt die Umwandlung dabei für jede Zeile einzeln aus.

Kehren wir zum Beispiel zu unserer Datenbank zurück und schauen uns die Tabelle `FamilyMembers` an: Sie enthält Name, Status und Geburtsdatum der Personen.

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/using-functions).

Jeden Wert dieser Felder können wir bei der Ausgabe verändern. So berechnet die folgende Abfrage die Länge des vollen Namens für jedes Familienmitglied.

```sql
SELECT member_name, LENGTH(member_name) AS fullname_length FROM FamilyMembers;
```

| member_name       | fullname_length |
| ----------------- | --------------- |
| Headley Quincey   | 15              |
| Flavia Quincey    | 14              |
| Andie Quincey     | 13              |
| Lela Quincey      | 12              |
| Annie Quincey     | 13              |
| Ernest Forrest    | 14              |
| Constance Forrest | 17              |
| Wednesday Addams  | 16              |

## Operationen mit dem Ergebnis einer Funktion

Da wir wissen, dass jede Funktion eines der möglichen Literale zurückgeben muss, kann ihr Ergebnis auch in weiteren Berechnungen und Umwandlungen verwendet werden.

Nehmen wir zum Beispiel an, wir wollen die ersten drei Buchstaben einer Zeichenkette holen und in Großbuchstaben umwandeln. Dafür reicht es, zwei Funktionen zu kombinieren: `LEFT` und `UPPER`, wobei das Ergebnis der einen Funktion als Argument für die zweite dient.

```sql
SELECT UPPER(LEFT('sql-academy', 3)) AS str;
```

| str |
| --- |
| SQL |

Oder wir wollen die Länge des Nachnamens einer Person ermitteln, wenn wir eine Zeichenkette im Format `Vorname<Leerzeichen>Nachname` haben. Eine mögliche Vorgehensweise ist die Verwendung der Funktion `LENGTH` und die Bestimmung der Position des Leerzeichens nach der Formel `<Länge des Nachnamens> = <Länge der gesamten Zeichenkette> - (<Länge des Vornamens> + <Länge des Leerzeichens>)`:

- Den Wert `<Länge der gesamten Zeichenkette>` bekommt man mit der Funktion `LENGTH`

**MySQL**

- Für `<Länge des Vornamens> + <Länge des Leerzeichens>` muss die Position des Zeichens berechnet werden, an der der Vorname endet, und eins addiert werden, da das Leerzeichen die Länge „1“ hat. Das können wir allein mit der Funktion `INSTR` machen, indem wir uns am Zeichen „Leerzeichen“ orientieren

Da beide Funktionen numerische Literale zurückgeben, können wir arithmetische Operationen mit ihnen durchführen. Lass uns das eine vom anderen abziehen und so die Länge des Nachnamens (lastname_length) ermitteln:

```sql
SELECT
    member_name,
    LENGTH(member_name) AS full_length,
    INSTR(member_name, ' ') AS firstname_with_space_length,
    LENGTH(member_name) - INSTR(member_name, ' ') AS lastname_length
FROM FamilyMembers;
```

**PostgreSQL**

- Für `<Länge des Vornamens> + <Länge des Leerzeichens>` muss die Position des Zeichens berechnet werden, an der der Vorname endet, und eins addiert werden, da das Leerzeichen die Länge „1“ hat. Das können wir allein mit der Funktion `POSITION` machen, indem wir uns am Zeichen „Leerzeichen“ orientieren

Da beide Funktionen numerische Literale zurückgeben, können wir arithmetische Operationen mit ihnen durchführen. Lass uns das eine vom anderen abziehen und so die Länge des Nachnamens (lastname_length) ermitteln:

```sql
SELECT
    member_name,
    LENGTH(member_name) AS full_length,
    POSITION(' ' IN member_name) AS firstname_with_space_length,
    LENGTH(member_name) - POSITION(' ' IN member_name) AS lastname_length
FROM FamilyMembers;
```

| member_name       | full_length | firstname_with_space_length | lastname_length |
| ----------------- | ----------- | --------------------------- | --------------- |
| Headley Quincey   | 15          | 8                           | 7               |
| Flavia Quincey    | 14          | 7                           | 7               |
| Andie Quincey     | 13          | 6                           | 7               |
| Lela Quincey      | 12          | 5                           | 7               |
| Annie Quincey     | 13          | 6                           | 7               |
| Ernest Forrest    | 14          | 7                           | 7               |
| Constance Forrest | 17          | 10                          | 7               |
| Wednesday Addams  | 16          | 10                          | 6               |
