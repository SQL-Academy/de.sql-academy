---
meta:
    title: "Literale in SQL"
    description: 'Ein Literal ist ein fest angegebener Wert, zum Beispiel die Zahl 12 oder die Zeichenkette "SQL". In MySQL gibt es folgende Literaltypen: Zahl, Zeichenkette, Wahrheitswert, NULL, Bit, Hexadezimalwert sowie Datum/Uhrzeit.'
---

# Literale in SQL

In der letzten Lektion haben wir eine Zeichenkette ausgegeben – formal gesprochen ein sogenanntes String-Literal.

```sql
SELECT 'Hello world'
```

> Ein Literal ist ein fest angegebener Wert, zum Beispiel die Zahl `12` oder die Zeichenkette `'SQL'`.

Die wichtigsten Literaltypen in SQL sind:

- String
- Zahl
- Wahrheitswert
- NULL
- Datum/Uhrzeit

## String-Literale

**MySQL**

Ein String ist eine Folge von Zeichen, eingeschlossen in einfache (') oder doppelte (") Anführungszeichen.
Zum Beispiel `'das ist ein String'` oder `"das ist ein String"`.

**PostgreSQL**

Ein String ist eine Folge von Zeichen, eingeschlossen in einfache Anführungszeichen (').
Zum Beispiel `'das ist ein String'`.

In PostgreSQL werden doppelte Anführungszeichen (") für Bezeichner (Tabellen- und Spaltennamen) verwendet, für String-Literale dürfen sie nicht eingesetzt werden.

**MySQL**

Strings können spezielle Zeichenfolgen enthalten, die mit `"\"` (dem Escape-Zeichen) beginnen. Damit gibt das DBMS gewöhnlichen Zeichen (Buchstaben oder anderen Symbolen) eine neue Sonderbedeutung. Die Sequenz `"\n"` bedeutet zum Beispiel "Zeilenumbruch" – ohne den vorangestellten Backslash wäre es einfach der Buchstabe `"n"`.

**PostgreSQL**

Strings können spezielle Zeichenfolgen enthalten, die mit `"\"` (dem Escape-Zeichen) beginnen. Damit gibt das DBMS gewöhnlichen Zeichen (Buchstaben oder anderen Symbolen) eine neue Sonderbedeutung. Die Sequenz `"\n"` bedeutet zum Beispiel "Zeilenumbruch" – ohne den vorangestellten Backslash wäre es einfach der Buchstabe `"n"`.

Damit Escape-Sequenzen in PostgreSQL funktionieren, musst du E-Strings verwenden.

- ```sql
  SELECT 'String Anderer String' as String
  ```

    | String                |
    | --------------------- |
    | String Anderer String |

- **MySQL**

    ```sql
    SELECT 'String \n Anderer String' as String
    ```

    **PostgreSQL**

    ```sql
    SELECT E'String \n Anderer String' as String
    ```

    | String                     |
    | -------------------------- |
    | String <br> Anderer String |

## Zahlenliterale

|                                                                                                                | Beispiel                      |
| :------------------------------------------------------------------------------------------------------------- | :---------------------------- |
| Umfasst ganze Zahlen und Dezimalzahlen. Trennzeichen für Dezimalzahlen ist der Punkt ".".                      | `1`, `2.9`, `0.01`            |
| Es können nur der ganzzahlige, nur der gebrochene Teil oder beide angegeben werden.                            | `.2`, `1.1`, `10`             |
| Zahlen können positiv oder negativ sein (bei positiven Zahlen ist das Vorzeichen nicht zwingend erforderlich). | `+1`, `-10`, `-2.2`           |
| Zahlen lassen sich auch in Exponentialschreibweise darstellen.                                                 | `1e3` (=1000) `1e-3` (=0.001) |

### Arithmetische Operatoren

Für Zahlenliterale stehen in SQL alle gewohnten arithmetischen Operatoren zur Verfügung:

**MySQL**

|  Operator  | Beschreibung         | Beispiel        |
| :--------: | :------------------- | :-------------- |
| `%`, `MOD` | Modulo-Division      | `11 % 5 = 1`    |
|    `*`     | Multiplikation       | `10 * 16 = 160` |
|    `+`     | Addition             | `98 + 2 = 100`  |
|    `-`     | Subtraktion          | `50 - 51 = -1`  |
|    `/`     | Division             | `1 / 2 = 0.5`   |
|   `DIV`    | Ganzzahlige Division | `10 DIV 4 = 2`  |

**PostgreSQL**

| Operator | Beschreibung    | Beispiel        |
| :------: | :-------------- | :-------------- |
|   `%`    | Modulo-Division | `11 % 5 = 1`    |
|   `*`    | Multiplikation  | `10 * 16 = 160` |
|   `+`    | Addition        | `98 + 2 = 100`  |
|   `-`    | Subtraktion     | `50 - 51 = -1`  |
|   `/`    | Division        | `1 / 2 = 0.5`   |

Mit diesen Operatoren baust du beliebige arithmetische Ausdrücke und nutzt die üblichen Rechenregeln.

Zum Beispiel:

```sql
SELECT (5 * 2 - 6) / 2 AS Result;
```

| Result |
| ------ |
| 2      |

## Datum- und Zeitliterale

Werte für Datum und Uhrzeit lassen sich als String oder als Zahl darstellen.

**MySQL**

Wenn wir zum Beispiel in einer Query ein bestimmtes Datum angeben wollen, geht das per String `"1970-12-30"`, `"19701230"` oder als Zahl `19701230`.
In allen Fällen wird der Wert als Datum "30. Dezember 1970" interpretiert.

**PostgreSQL**

Wenn wir zum Beispiel in einer Query ein bestimmtes Datum angeben wollen, geht das per String `'1970-12-30'` oder `'19701230'`.

Ein Beispiel für die Verwendung eines Datumsliterals:

```sql
SELECT * FROM FamilyMembers WHERE birthday > '1970-12-30'
```

Was die Query genau macht, ist hier nicht wichtig – das schauen wir uns später an. Wichtig ist die Syntax, mit der wir das Datum in der Query angeben.

Neben dem reinen Datum können wir auch eine Uhrzeit allein oder Datum und Uhrzeit zusammen angeben.

|              | Beschreibung                                                | Format                                                                                                                                                                              |
| :----------- | :---------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Datum        | Wird als Datum mit Uhrzeit gleich null interpretiert.       | `YYYY-MM-DD`, `YYYYMMDD` <br /><br /> Statt dem Trennzeichen "-" kannst du beliebige Satzzeichen verwenden. <br /><br /> Beispiel: <br /> `'2020-01-01'` = 1. Januar 2020, 00:00:00 |
| Uhrzeit      | Enthält nur die Uhrzeit ohne konkretes Datum.               | `hh:mm:ss`, `hh:mm`, `hh`, `ss` <br /><br /> Das Trennzeichen kannst du auch weglassen. <br /><br /> Beispiel: <br /> `12:11` = 12:11:00                                            |
| Datum + Zeit | Datum mit der Möglichkeit, eine konkrete Uhrzeit anzugeben. | `YYYY-MM-DD hh:mm:ss`, `YYYYMMDDhhmmss` <br /><br /> Beispiel: <br /> `'20200101183030'` = 1. Januar 2020, 18:30:30                                                                 |

## Wahrheitswert-Literale

Ein Wahrheitswert-Literal sind die Werte `TRUE` und `FALSE`. Sie stehen für die Wahrheit oder Unwahrheit einer Aussage.

**MySQL**

Beim Interpretieren der Query wandelt MySQL sie in Zahlen um: `TRUE` und `FALSE`
werden zu `1` bzw. `0`.

## NULL

`NULL` bedeutet "keine Daten" oder "kein Wert". Damit lässt sich unterscheiden zwischen leeren Werten – etwa einem String der Länge null oder einem Leerzeichen – und dem Fall, dass überhaupt kein Wert vorhanden ist.
