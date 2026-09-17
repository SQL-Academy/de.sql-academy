---
meta:
    title: "Bedingte Logik in SQL: Funktionen IFNULL und NULLIF"
    description: "Bedingte Logik in SQL: die Funktion IF in MySQL und zusätzliche Funktionen in PostgreSQL (COALESCE, NULLIF)"
---

**MySQL**

# Die bedingte Funktion IF

Im vorherigen Kapitel haben wir den Operator `CASE` für bedingte Logik in SQL betrachtet.
Das ist allerdings nicht der einzige Mechanismus, mit dem du Verzweigungen in einer Query abbilden kannst.
Jetzt ist die Funktion `IF` an der Reihe.

## Syntax von IF

```sql
IF(bedingung, wert_1, wert_2);
```

Wenn die als erstes Argument übergebene Bedingung wahr ist,
gibt die Funktion `IF` den Wert des zweiten Arguments `wert_1` zurück, andernfalls den Wert des dritten Arguments `wert_2`.

**PostgreSQL**

# Zusätzliche Funktionen für bedingte Logik

Im vorherigen Kapitel haben wir den Operator `CASE` für bedingte Logik in SQL kennengelernt.
PostgreSQL stellt zusätzliche Funktionen bereit, die die Arbeit mit bedingter Logik in Spezialfällen vereinfachen.
Diese Funktionen sind besonders nützlich im Umgang mit `NULL`-Werten und machen den Code lesbarer.

## Funktionen für bedingte Logik

Neben dem universellen `CASE`-Operator bietet PostgreSQL:

1. **Funktion COALESCE** – für die Arbeit mit `NULL`-Werten
2. **Funktion NULLIF** – für Spezialfälle rund um `NULL`

Diese Funktionen gehören zum SQL-Standard und machen den Code in bestimmten Situationen deutlich lesbarer.

### Beispiele

**MySQL**

- Ein einfacher Vergleich zweier Zahlen. Weil 10 nicht größer als 20 ist, gibt die Funktion 'FALSE' zurück.

    ```sql
    SELECT IF(10 > 20, 'TRUE', 'FALSE');
    ```

    | IF(10 > 20, "TRUE", "FALSE") |
    | ---------------------------- |
    | FALSE                        |

**PostgreSQL**

- Ein einfaches Beispiel für bedingte Logik mit dem `CASE`-Operator aus dem vorherigen Kapitel:

    ```sql
    SELECT CASE WHEN 10 > 20 THEN 'TRUE' ELSE 'FALSE' END;
    ```

    | case  |
    | ----- |
    | FALSE |

**MySQL**

- Ein Beispiel mit einer echten Datenbank. Anhand des Preises soll bestimmt werden, ob eine Unterkunft zur "Komfort-Klasse" oder zur "Economy-Klasse" gehört.
  Liegt der Preis bei `150` oder darüber, zählt die Unterkunft zur "Komfort-Klasse".

    ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/if-function).

    ```sql
    SELECT id, price,
        IF(price >= 150, 'Komfort-Klasse', 'Economy-Klasse') AS category
        FROM Rooms
    ```

    | id  | price | category       |
    | --- | ----- | -------------- |
    | 1   | 149   | Economy-Klasse |
    | 2   | 225   | Komfort-Klasse |
    | 3   | 150   | Komfort-Klasse |
    | 4   | 89    | Economy-Klasse |
    | 5   | 80    | Economy-Klasse |
    | 6   | 200   | Komfort-Klasse |
    | 7   | 60    | Economy-Klasse |
    | 8   | 79    | Economy-Klasse |
    | 9   | 79    | Economy-Klasse |
    | 10  | 150   | Komfort-Klasse |
    | 11  | 135   | Economy-Klasse |
    | 12  | 85    | Economy-Klasse |
    | 13  | 89    | Economy-Klasse |
    | 14  | 85    | Economy-Klasse |
    | 15  | 120   | Economy-Klasse |
    | 16  | 140   | Economy-Klasse |
    | 17  | 215   | Komfort-Klasse |
    | 18  | 140   | Economy-Klasse |
    | 19  | 99    | Economy-Klasse |
    | 20  | 190   | Komfort-Klasse |
    | 21  | 299   | Komfort-Klasse |
    | 22  | 130   | Economy-Klasse |
    | 23  | 80    | Economy-Klasse |
    | 24  | 110   | Economy-Klasse |
    | 25  | 120   | Economy-Klasse |
    | 26  | 60    | Economy-Klasse |
    | 27  | 80    | Economy-Klasse |
    | 28  | 150   | Komfort-Klasse |
    | 29  | 44    | Economy-Klasse |
    | 30  | 180   | Komfort-Klasse |
    | 31  | 50    | Economy-Klasse |
    | 32  | 52    | Economy-Klasse |
    | 33  | 55    | Economy-Klasse |
    | 34  | 50    | Economy-Klasse |
    | 35  | 70    | Economy-Klasse |
    | 36  | 89    | Economy-Klasse |
    | 37  | 35    | Economy-Klasse |
    | 38  | 85    | Economy-Klasse |
    | 39  | 150   | Komfort-Klasse |
    | 40  | 40    | Economy-Klasse |
    | 41  | 68    | Economy-Klasse |
    | 42  | 120   | Economy-Klasse |
    | 43  | 120   | Economy-Klasse |
    | 44  | 135   | Economy-Klasse |
    | 45  | 150   | Komfort-Klasse |
    | 46  | 150   | Komfort-Klasse |
    | 47  | 130   | Economy-Klasse |
    | 48  | 110   | Economy-Klasse |
    | 49  | 115   | Economy-Klasse |
    | 50  | 80    | Economy-Klasse |

**PostgreSQL**

- Ein Beispiel mit echten Daten. Der `CASE`-Operator hilft dabei, Unterkünfte nach Preis zu kategorisieren:

    ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/if-function).

    ```sql
    SELECT id, price,
        CASE WHEN price >= 150 THEN 'Komfort-Klasse' ELSE 'Economy-Klasse' END AS category
        FROM Rooms
    ```

    | id  | price | category       |
    | --- | ----- | -------------- |
    | 1   | 149   | Economy-Klasse |
    | 2   | 225   | Komfort-Klasse |
    | 3   | 150   | Komfort-Klasse |
    | 4   | 89    | Economy-Klasse |
    | 5   | 80    | Economy-Klasse |
    | 6   | 200   | Komfort-Klasse |
    | 7   | 60    | Economy-Klasse |
    | 8   | 79    | Economy-Klasse |
    | 9   | 79    | Economy-Klasse |
    | 10  | 150   | Komfort-Klasse |
    | 11  | 135   | Economy-Klasse |
    | 12  | 85    | Economy-Klasse |
    | 13  | 89    | Economy-Klasse |
    | 14  | 85    | Economy-Klasse |
    | 15  | 120   | Economy-Klasse |
    | 16  | 140   | Economy-Klasse |
    | 17  | 215   | Komfort-Klasse |
    | 18  | 140   | Economy-Klasse |
    | 19  | 99    | Economy-Klasse |
    | 20  | 190   | Komfort-Klasse |
    | 21  | 299   | Komfort-Klasse |
    | 22  | 130   | Economy-Klasse |
    | 23  | 80    | Economy-Klasse |
    | 24  | 110   | Economy-Klasse |
    | 25  | 120   | Economy-Klasse |
    | 26  | 60    | Economy-Klasse |
    | 27  | 80    | Economy-Klasse |
    | 28  | 150   | Komfort-Klasse |
    | 29  | 44    | Economy-Klasse |
    | 30  | 180   | Komfort-Klasse |
    | 31  | 50    | Economy-Klasse |
    | 32  | 52    | Economy-Klasse |
    | 33  | 55    | Economy-Klasse |
    | 34  | 50    | Economy-Klasse |
    | 35  | 70    | Economy-Klasse |
    | 36  | 89    | Economy-Klasse |
    | 37  | 35    | Economy-Klasse |
    | 38  | 85    | Economy-Klasse |
    | 39  | 150   | Komfort-Klasse |
    | 40  | 40    | Economy-Klasse |
    | 41  | 68    | Economy-Klasse |
    | 42  | 120   | Economy-Klasse |
    | 43  | 120   | Economy-Klasse |
    | 44  | 135   | Economy-Klasse |
    | 45  | 150   | Komfort-Klasse |
    | 46  | 150   | Komfort-Klasse |
    | 47  | 130   | Economy-Klasse |
    | 48  | 110   | Economy-Klasse |
    | 49  | 115   | Economy-Klasse |
    | 50  | 80    | Economy-Klasse |

**MySQL**

- `IF`-Funktionen lassen sich auch ineinander verschachteln und so der `CASE`-Operator nachbilden.

    ```sql
    SELECT id, price,
        IF(price >= 200, 'Business-Klasse',
            IF(price >= 150,
                'Komfort-Klasse', 'Economy-Klasse')) AS category
        FROM Rooms
    ```

    | id  | price | category        |
    | --- | ----- | --------------- |
    | 1   | 149   | Economy-Klasse  |
    | 2   | 225   | Business-Klasse |
    | 3   | 150   | Komfort-Klasse  |
    | 4   | 89    | Economy-Klasse  |
    | 5   | 80    | Economy-Klasse  |
    | 6   | 200   | Business-Klasse |
    | 7   | 60    | Economy-Klasse  |
    | 8   | 79    | Economy-Klasse  |
    | 9   | 79    | Economy-Klasse  |
    | 10  | 150   | Komfort-Klasse  |
    | 11  | 135   | Economy-Klasse  |
    | 12  | 85    | Economy-Klasse  |
    | 13  | 89    | Economy-Klasse  |
    | 14  | 85    | Economy-Klasse  |
    | 15  | 120   | Economy-Klasse  |
    | 16  | 140   | Economy-Klasse  |
    | 17  | 215   | Business-Klasse |
    | 18  | 140   | Economy-Klasse  |
    | 19  | 99    | Economy-Klasse  |
    | 20  | 190   | Komfort-Klasse  |
    | 21  | 299   | Business-Klasse |
    | 22  | 130   | Economy-Klasse  |
    | 23  | 80    | Economy-Klasse  |
    | 24  | 110   | Economy-Klasse  |
    | 25  | 120   | Economy-Klasse  |
    | 26  | 60    | Economy-Klasse  |
    | 27  | 80    | Economy-Klasse  |
    | 28  | 150   | Komfort-Klasse  |
    | 29  | 44    | Economy-Klasse  |
    | 30  | 180   | Komfort-Klasse  |
    | 31  | 50    | Economy-Klasse  |
    | 32  | 52    | Economy-Klasse  |
    | 33  | 55    | Economy-Klasse  |
    | 34  | 50    | Economy-Klasse  |
    | 35  | 70    | Economy-Klasse  |
    | 36  | 89    | Economy-Klasse  |
    | 37  | 35    | Economy-Klasse  |
    | 38  | 85    | Economy-Klasse  |
    | 39  | 150   | Komfort-Klasse  |
    | 40  | 40    | Economy-Klasse  |
    | 41  | 68    | Economy-Klasse  |
    | 42  | 120   | Economy-Klasse  |
    | 43  | 120   | Economy-Klasse  |
    | 44  | 135   | Economy-Klasse  |
    | 45  | 150   | Komfort-Klasse  |
    | 46  | 150   | Komfort-Klasse  |
    | 47  | 130   | Economy-Klasse  |
    | 48  | 110   | Economy-Klasse  |
    | 49  | 115   | Economy-Klasse  |
    | 50  | 80    | Economy-Klasse  |

**PostgreSQL**

- `CASE` eignet sich hervorragend für mehrere Bedingungen:

    ```sql
    SELECT id, price,
        CASE
            WHEN price >= 200 THEN 'Business-Klasse'
            WHEN price >= 150 THEN 'Komfort-Klasse'
            ELSE 'Economy-Klasse'
        END AS category
        FROM Rooms
    ```

    | id  | price | category        |
    | --- | ----- | --------------- |
    | 1   | 149   | Economy-Klasse  |
    | 2   | 225   | Business-Klasse |
    | 3   | 150   | Komfort-Klasse  |
    | 4   | 89    | Economy-Klasse  |
    | 5   | 80    | Economy-Klasse  |
    | 6   | 200   | Business-Klasse |
    | 7   | 60    | Economy-Klasse  |
    | 8   | 79    | Economy-Klasse  |
    | 9   | 79    | Economy-Klasse  |
    | 10  | 150   | Komfort-Klasse  |
    | 11  | 135   | Economy-Klasse  |
    | 12  | 85    | Economy-Klasse  |
    | 13  | 89    | Economy-Klasse  |
    | 14  | 85    | Economy-Klasse  |
    | 15  | 120   | Economy-Klasse  |
    | 16  | 140   | Economy-Klasse  |
    | 17  | 215   | Business-Klasse |
    | 18  | 140   | Economy-Klasse  |
    | 19  | 99    | Economy-Klasse  |
    | 20  | 190   | Komfort-Klasse  |
    | 21  | 299   | Business-Klasse |
    | 22  | 130   | Economy-Klasse  |
    | 23  | 80    | Economy-Klasse  |
    | 24  | 110   | Economy-Klasse  |
    | 25  | 120   | Economy-Klasse  |
    | 26  | 60    | Economy-Klasse  |
    | 27  | 80    | Economy-Klasse  |
    | 28  | 150   | Komfort-Klasse  |
    | 29  | 44    | Economy-Klasse  |
    | 30  | 180   | Komfort-Klasse  |
    | 31  | 50    | Economy-Klasse  |
    | 32  | 52    | Economy-Klasse  |
    | 33  | 55    | Economy-Klasse  |
    | 34  | 50    | Economy-Klasse  |
    | 35  | 70    | Economy-Klasse  |
    | 36  | 89    | Economy-Klasse  |
    | 37  | 35    | Economy-Klasse  |
    | 38  | 85    | Economy-Klasse  |
    | 39  | 150   | Komfort-Klasse  |
    | 40  | 40    | Economy-Klasse  |
    | 41  | 68    | Economy-Klasse  |
    | 42  | 120   | Economy-Klasse  |
    | 43  | 120   | Economy-Klasse  |
    | 44  | 135   | Economy-Klasse  |
    | 45  | 150   | Komfort-Klasse  |
    | 46  | 150   | Komfort-Klasse  |
    | 47  | 130   | Economy-Klasse  |
    | 48  | 110   | Economy-Klasse  |
    | 49  | 115   | Economy-Klasse  |
    | 50  | 80    | Economy-Klasse  |

Für Spezialfälle bietet PostgreSQL allerdings noch zugeschnittenere Funktionen.

**MySQL**

## Die Funktionen IFNULL und NULLIF

Neben `IF` gibt es in MySQL auch die einfacheren, aber weniger universellen Funktionen `IFNULL` und `NULLIF`,
die speziell für die Behandlung von `NULL`-Werten gedacht sind.

### Syntax von IFNULL

```sql
IFNULL(wert, alternativ_wert);
```

Die Funktion `IFNULL` gibt den als erstes Argument übergebenen `wert` zurück, sofern dieser nicht `NULL` ist;
ansonsten wird `alternativ_wert` zurückgegeben.

**PostgreSQL**

## Die Funktion COALESCE

`COALESCE` ist eine elegante Lösung für den Umgang mit `NULL`-Werten.
Sie liefert den ersten Wert aus der Argumentliste, der nicht `NULL` ist.

### Syntax

```sql
COALESCE(wert1, wert2, ..., wertN);
```

Das ist deutlich angenehmer, als lange `CASE`-Ausdrücke für die `NULL`-Behandlung zu schreiben.

### Vergleich der Ansätze

Mit CASE:

```sql
CASE
    WHEN wert1 IS NOT NULL THEN wert1
    WHEN wert2 IS NOT NULL THEN wert2
    ELSE wert3
END
```

Mit COALESCE (deutlich einfacher):

```sql
COALESCE(wert1, wert2, wert3)
```

**MySQL**

### Beispiele zur Funktion IFNULL

- Ist das erste Argument ungleich `NULL`, wird genau dieser Wert zurückgegeben.

    ```sql
    SELECT IFNULL('SQL Academy', 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | SQL Academy |

**PostgreSQL**

### Beispiele zur Funktion COALESCE

- Ist das erste Argument ungleich `NULL`, wird genau dieser Wert zurückgegeben.

    ```sql
    SELECT COALESCE('SQL Academy', 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | SQL Academy |

**MySQL**

- Ist das erste Argument gleich `NULL`, wird der als zweites Argument übergebene Wert zurückgegeben.

    ```sql
    SELECT IFNULL(NULL, 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer                |
    | -------------------------- |
    | Alternative zu SQL Academy |

**PostgreSQL**

- Ist das erste Argument gleich `NULL`, wird der nächste Wert geliefert, der nicht `NULL` ist.

    ```sql
    SELECT COALESCE(NULL, 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer                |
    | -------------------------- |
    | Alternative zu SQL Academy |

- `COALESCE` kann beliebig viele Argumente entgegennehmen, was den Code sehr lesbar macht:

    ```sql
    SELECT COALESCE(NULL, NULL, 'SQL Academy', 'Notfallvariante') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | SQL Academy |

## Die Funktion NULLIF

`NULLIF` ist praktisch, wenn du einen bestimmten Wert durch `NULL` ersetzen willst.
Das hilft beim Filtern oder beim Umgang mit "leeren" Werten.

### Syntax von NULLIF

```sql
NULLIF(wert_1, wert_2);
```

Die Funktion `NULLIF` liefert `NULL`, wenn `wert_1` gleich `wert_2` ist; andernfalls gibt sie `wert_1` zurück.

### Beispiele zur Funktion NULLIF

**MySQL**

- Sind beide Argumente gleich, wird `NULL` zurückgegeben.

    ```sql
    SELECT NULLIF('SQL Academy', 'SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | null        |

**PostgreSQL**

- Sind beide Argumente gleich, wird `NULL` zurückgegeben.

    ```sql
    SELECT NULLIF('SQL Academy', 'SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | null        |

**MySQL**

- Unterscheiden sich die beiden Argumente, wird der Wert des ersten Arguments zurückgegeben.

    ```sql
    SELECT NULLIF('SQL Academy', 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | SQL Academy |

**PostgreSQL**

- Unterscheiden sich die beiden Argumente, wird der Wert des ersten Arguments zurückgegeben.

    ```sql
    SELECT NULLIF('SQL Academy', 'Alternative zu SQL Academy') AS sql_trainer;
    ```

    | sql_trainer |
    | ----------- |
    | SQL Academy |

**PostgreSQL**

### Wann welche Funktion sinnvoll ist:

- **CASE**: wenn du komplexe bedingte Logik mit mehreren Verzweigungen brauchst
- **COALESCE**: wenn du `NULL`-Werte durch Standardwerte ersetzen willst
- **NULLIF**: wenn du bestimmte Werte in `NULL` umwandeln möchtest

Diese Funktionen machen den Code lesbarer und sind Teil des SQL-Standards.
