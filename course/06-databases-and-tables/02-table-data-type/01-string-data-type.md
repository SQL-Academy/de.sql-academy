---
meta:
    title: "String-Datentypen in SQL: MySQL und PostgreSQL"
    description: "Mit Strings in MySQL und PostgreSQL arbeiten. Die wichtigsten Datentypen für Textinformationen."
---

# String-Datentypen

Der String-Datentyp ist der am häufigsten verwendete Datentyp. Mit ihm speicherst du in der Datenbank sowohl Texte als auch verschiedene binäre Daten (zum Beispiel Bilder).

**MySQL**

In MySQL wird er durch folgende Typen repräsentiert:

## CHAR und VARCHAR

| Typ          | Beschreibung                                                                                                                                                                             | Wertebereich                |
| :----------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------- |
| `CHAR(X)`    | Enthält Textstrings. Die Länge ist fest und wird bei der Deklaration angegeben. Ist der String kürzer als angegeben, wird er rechts mit Leerzeichen auf die angegebene Länge aufgefüllt. | Länge zwischen 0 und 255    |
| `VARCHAR(X)` | Enthält Textstrings mit dynamischer Länge.                                                                                                                                               | Länge zwischen 0 und 65.535 |

## BINARY und VARBINARY

Die Datentypen `BINARY` und `VARBINARY` entsprechen `CHAR` und `VARCHAR`, speichern aber Binärstrings.

| Typ            | Beschreibung                                                                     | Wertebereich                |
| :------------- | :------------------------------------------------------------------------------- | :-------------------------- |
| `BINARY(X)`    | Enthält Binärstrings. Die Länge ist fest und wird bei der Deklaration angegeben. | Länge zwischen 0 und 255    |
| `VARBINARY(X)` | Enthält Binärstrings mit dynamischer Länge.                                      | Länge zwischen 0 und 65.535 |

## BLOB und TEXT

`BLOB` dient zum Speichern großer Binärdaten wie Bilder. `TEXT` ist ebenfalls für große Datenmengen gedacht, aber für Textinhalte.

Der Unterschied: Bei `BLOB` sind Sortierung und Vergleich der gespeicherten Daten case-sensitive, bei `TEXT` nicht.

| Typ    | Beschreibung          | Wertebereich      |
| :----- | :-------------------- | :---------------- |
| `BLOB` | Enthält Binärstrings. | Max. Länge 65.535 |
| `TEXT` | Enthält Textstrings.  | Max. Länge 65.535 |

`BLOB` und `TEXT` haben weitere Untertypen, die sich in der maximal speicherbaren Datenmenge unterscheiden.

| Typ          | Wertebereich             |
| :----------- | :----------------------- |
| `TINYBLOB`   | Max. Länge 255           |
| `MEDIUMBLOB` | Max. Länge 16.777.215    |
| `LONGBLOB`   | Max. Länge 4.294.967.295 |
| `TINYTEXT`   | Max. Länge 255           |
| `MEDIUMTEXT` | Max. Länge 16.777.215    |
| `LONGTEXT`   | Max. Länge 4.294.967.295 |

**PostgreSQL**

In PostgreSQL wird er durch folgende Typen repräsentiert:

## CHARACTER und VARCHAR

| Typ          | Beschreibung                                                                                | Wertebereich                       |
| :----------- | :------------------------------------------------------------------------------------------ | :--------------------------------- |
| `CHAR(n)`    | Enthält Textstrings fester Länge. Wird mit Leerzeichen auf die angegebene Länge aufgefüllt. | Länge zwischen 1 und 10.485.760    |
| `VARCHAR(n)` | Enthält Textstrings variabler Länge mit Begrenzung.                                         | Länge zwischen 1 und 10.485.760    |
| `TEXT`       | Enthält Textstrings unbegrenzter variabler Länge.                                           | Praktisch unbegrenzt (bis zu 1 GB) |

Wichtig: In PostgreSQL wird der Typ `TEXT` üblicherweise einem `VARCHAR` ohne Längenbegrenzung vorgezogen, da beide die gleiche Performance haben.
