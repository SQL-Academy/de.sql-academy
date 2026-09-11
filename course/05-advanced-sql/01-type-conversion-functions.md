---
meta:
    title: "Funktionen zur Typkonvertierung, CAST: MySQL und PostgreSQL"
    description: "SQL-Typkonvertierung: die Funktion CAST in MySQL und PostgreSQL, CONVERT in MySQL. Datentypen für die Konvertierung, Syntax und Beispiele."
---

# Funktionen zur Typkonvertierung, CAST

Wenn wir Operationen mit Werten unterschiedlicher Datentypen ausführen, versucht das DBMS, eine Konvertierung durchzuführen und die verwendeten Werte in den passenden Typ zu überführen. Im Beispiel unten vergleichen wir zum Beispiel Werte mit den Typen `STRING` und `INT`. Um diesen Vergleich auszuführen, konvertiert das DBMS automatisch den Zeichenkettenwert in einen numerischen.

**MySQL**

```sql
SELECT '50' > 49 AS comparison_1, '50' > 51 AS comparison_2;
```

| comparison_1 | comparison_2 |
| ------------ | ------------ |
| 1            | 0            |

**PostgreSQL**

```sql
SELECT '50' > 49 AS comparison_1, '50' > 51 AS comparison_2;
```

| comparison_1 | comparison_2 |
| ------------ | ------------ |
| true         | false        |

Aber nicht alle Konvertierungen kann das DBMS automatisch vornehmen, dann ist eine explizite Typkonvertierung nötig.

**MySQL**

Dafür gibt es in MySQL zwei sehr ähnliche Funktionen: `CAST` und `CONVERT`.

**PostgreSQL**

Dafür gibt es in PostgreSQL die Funktion `CAST` und den Operator `::`.

## Syntax

**MySQL**

```sql
CAST(wert AS ziel_typ);
CONVERT(wert, ziel_typ);
```

Zum Beispiel,

```sql
SELECT CAST(12005.6 AS DECIMAL) AS cast_example, CONVERT(12005.4, DECIMAL) AS convert_example;
```

| cast_example | convert_example |
| ------------ | --------------- |
| 12006        | 12005           |

**PostgreSQL**

```sql
CAST(wert AS ziel_typ);
wert::ziel_typ;
```

Zum Beispiel,

```sql
SELECT CAST(12005.6 AS INTEGER) AS cast_example, 12005.4::INTEGER AS operator_example;
```

| cast_example | operator_example |
| ------------ | ---------------- |
| 12006        | 12005            |

**MySQL**

Die Funktion CAST kann den übergebenen Wert in einen der folgenden Typen umwandeln:

| Typ                | Beschreibung                                                                                                                                                                                         |
| :----------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DATE`             | Konvertiert den Wert in `DATE`. Format: "YYYY-MM-DD".                                                                                                                                                |
| `DATETIME`         | Konvertiert den Wert in `DATETIME`. Format: "YYYY-MM-DD hh:mm:ss".                                                                                                                                   |
| `TIME`             | Konvertiert den Wert in `TIME`. Format: "hh:mm:ss".                                                                                                                                                  |
| `DECIMAL[(M[,D])]` | Konvertiert den Wert in `DECIMAL`. Hat zwei optionale Argumente `M` und `D`, die die maximale Anzahl der Stellen vor und nach dem Komma festlegen. Standardmäßig ist `D` gleich 0 und `M` gleich 10. |
| `CHAR[(N)]`        | Konvertiert den Wert in `CHAR`. Als optionales Argument kann die maximale Länge der Zeichenkette übergeben werden.                                                                                   |
| `SIGNED`           | Konvertiert den Wert in einen `BIGINT`-Wert.                                                                                                                                                         |
| `UNSIGNED`         | Konvertiert den Wert in einen vorzeichenlosen `BIGINT`-Wert.                                                                                                                                         |
| `BINARY`           | Konvertiert den Wert in `BINARY`.                                                                                                                                                                    |
| `YEAR`             | Konvertiert den Wert in ein Jahr.                                                                                                                                                                    |

**PostgreSQL**

Die Funktion CAST kann den übergebenen Wert in einen der folgenden Typen umwandeln:

| Typ                | Beschreibung                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DATE`             | Konvertiert den Wert in `DATE`. Format: "YYYY-MM-DD".                                                                                              |
| `TIMESTAMP`        | Konvertiert den Wert in `TIMESTAMP`. Format: "YYYY-MM-DD hh:mm:ss".                                                                                |
| `TIME`             | Konvertiert den Wert in `TIME`. Format: "hh:mm:ss".                                                                                                |
| `NUMERIC[(M[,D])]` | Konvertiert den Wert in `NUMERIC`. Hat zwei optionale Argumente `M` und `D`, die die maximale Anzahl der Stellen vor und nach dem Komma festlegen. |
| `VARCHAR[(N)]`     | Konvertiert den Wert in `VARCHAR`. Als optionales Argument kann die maximale Länge der Zeichenkette übergeben werden.                              |
| `INTEGER`          | Konvertiert den Wert in eine ganze Zahl.                                                                                                           |
| `BIGINT`           | Konvertiert den Wert in eine große ganze Zahl.                                                                                                     |
| `BOOLEAN`          | Konvertiert den Wert in einen booleschen Typ.                                                                                                      |
| `TEXT`             | Konvertiert den Wert in einen Texttyp.                                                                                                             |

## Wenn eine Konvertierung nicht möglich ist

Die Verwendung der Funktion `CAST` stellt Anforderungen an das Format des Ausgangswerts. Und sofort drängt sich die Frage auf, was passiert, wenn dieses Format nicht zum erforderlichen passt? Was ist zum Beispiel, wenn man versucht, einen beliebigen Text in einen Zeit-Datentyp zu konvertieren:

**MySQL**

```sql
SELECT CAST('SQL Academy' AS DATETIME) AS invalid_cast;
```

| invalid_cast |
| ------------ |
| \<NULL>      |

In diesem Fall gibt MySQL `NULL` anstelle des konvertierten Werts zurück.

**PostgreSQL**

```sql
SELECT CAST('SQL Academy' AS TIMESTAMP) AS invalid_cast;
```

In diesem Fall gibt PostgreSQL einen Fehler zurück, da die Zeichenkette nicht in ein Datum konvertiert werden kann.

## Selbsttest

Also, wofür ist die Funktion `CAST` in SQL zuständig?

1. Die Funktion wird verwendet, wenn man den Datentyp eines übergebenen Wertes bestimmen muss — Die Funktion CAST ist für die Konvertierung eines Wertes zuständig, nicht für die Bestimmung seines Datentyps.

2. **Richtige Antwort:** Die Funktion ist für die Konvertierung eines Wertes von einem Datentyp in einen anderen zuständig — Die Funktion ist tatsächlich für die explizite Typkonvertierung zuständig.

3. Die Funktion wird für mathematische Berechnungen benötigt — Vielleicht solltest du diese Lektion noch einmal durchgehen. Die Funktion CAST ist für die Typkonvertierung zuständig, nicht für mathematische Berechnungen.
