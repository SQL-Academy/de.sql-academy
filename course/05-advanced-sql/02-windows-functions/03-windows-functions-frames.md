---
meta:
    title: "Fensterrahmen, RANGE und ROWS"
    description: "Fensterrahmen in SQL — ein Konzept, das die Teilmenge der Zeilen festlegt, die in Window-Funktionen betrachtet werden. Erfahre den Unterschied zwischen Fensterrahmen und Partition und wie man die Grenzen des Rahmens mit ROWS und RANGE definiert. Verständnis der Unterschiede zwischen ROWS und RANGE im Kontext der SQL-Window-Funktionen."
---

# Fensterrahmen, RANGE und ROWS

Im Kontext der SQL-Window-Funktionen legt der „Fensterrahmen“ die Teilmenge der Zeilen fest, die von der SQL-Funktion bei den Berechnungen betrachtet werden.

Anders gesagt ist der Fensterrahmen eine dynamische Menge von Zeilen, die über dein Abfrageergebnis "gleitet" und für jede Zeile unterschiedliche Datenmengen bildet, abhängig von der von dir festgelegten Rahmendefinition.

## Fensterrahmen vs. Partitionen

Auch wenn die Begriffe "Fensterrahmen" und "Partition" ähnlich klingen, stehen sie für unterschiedliche Konzepte:

- Partition (`PARTITION BY`). Das ist die Aufteilung der gesamten Ergebnismenge in nicht überlappende Teilmengen, wobei jede Teilmenge Zeilen mit gleichen Werten in einer oder mehreren Spalten enthält. Window-Funktionen werden auf jede Partition separat angewendet, so als wäre jede von ihnen eine eigene Datenmenge.

    ![Schema der Aufteilung in Partitionen](https://sql-academy.org/static/guidePage/windows-functions-frames/partitions_visualisation_de.png "Schema der Aufteilung in Partitionen")

- Fensterrahmen. Er legt fest, welche konkreten Zeilen in jeder Partition zur Berechnung der Window-Funktion für jede Zeile verwendet werden. Der Rahmen kann sich von Zeile zu Zeile ändern.

    Wird zum Beispiel die Regel `ROWS BETWEEN 1 PRECEDING AND CURRENT ROW` verwendet, besteht der Rahmen für jede Zeile aus ihr selbst und einer vorhergehenden Zeile. Das ist sozusagen eine "Unter-Partition" innerhalb der bereits existierenden Partition.

    ![Schema zur Bildung von Fensterrahmen](https://sql-academy.org/static/guidePage/windows-functions-frames/windows_visualisation_de.png "Schema zur Bildung von Fensterrahmen")

    Das heißt:

    - Unser erster Rahmen besteht nur aus 1 Datensatz, weil es keinen vorhergehenden Datensatz gibt. Dieser einzige Datensatz wird an die Aggregatfunktion `AVG(price)` übergeben und das Ergebnis wird in das Feld `avg_price` eingetragen.
    - Unser zweiter Rahmen enthält bereits den 1. und 2. Datensatz, die an `AVG(price)` übergeben werden und `(170 + 220) / 2 = 195` zurückgeben.
    - Unser dritter Rahmen enthält den 2. und 3. Datensatz, das Ergebnis ist `(220 + 150) / 2 = 185`.
    - und so weiter.

### Anmerkung zum Fensterrahmen ohne ROWS/RANGE

Fehlt in der Definition der Window-Funktion `ROWS/RANGE`, hängt der Standardrahmen davon ab, ob `ORDER BY` vorhanden ist.

Ohne `ORDER BY` fällt der Rahmen mit der Partition zusammen. In diesem Fall verarbeitet die Window-Funktion alle Zeilen innerhalb der Partition und beschränkt sich nicht auf eine Teilmenge. Das bedeutet, dass das Ergebnis der Funktion für alle Zeilen innerhalb derselben Partition gleich ist.

Mit `ORDER BY` wird standardmäßig die Regel `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` angewendet. In diesem Fall beginnt der Fensterrahmen mit der ersten Zeile und endet mit der aktuellen Zeile, einschließlich aller Zeilen mit denselben Werten in den Sortierspalten wie die aktuelle Zeile.

## Definition der Grenzen des Fensterrahmens

Mit der Syntax `ROWS` oder `RANGE` können wir festlegen, welcher Fensterrahmen genau an die Window-Funktion zur Berechnung des Werts für die aktuelle Zeile übergeben wird.

Die Syntax zur Definition der Grenzen des Fensterrahmens sieht aus wie die Angabe eines Bereichs relativ zur aktuellen Zeile.

```sql
SELECT <window_funktion>(<tabellenfeld>)
OVER (
      ...
      ROWS|RANGE BETWEEN <anfang des rahmens> AND <ende des rahmens>
)
```

Wenn wir zum Beispiel wollen, dass bei den Berechnungen nur die zwei vorhergehenden Datensätze und die aktuelle Zeile in die Window-Funktion einfließen, sieht die Syntax so aus:

```sql
... ROWS|RANGE BETWEEN 2 PRECEDING AND CURRENT ROW
```

Wenn wir wollen, dass die aktuelle Zeile und alle nachfolgenden an die Window-Funktion übergeben werden, sieht die Syntax so aus:

```sql
... ROWS|RANGE BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
```

### Mögliche Definitionen der Rahmengrenzen

- `UNBOUNDED PRECEDING`, alle Zeilen, die der aktuellen vorangehen
- `N PRECEDING`, N Zeilen vor der aktuellen Zeile
- `CURRENT ROW`, die aktuelle Zeile
- `N FOLLOWING`, N Zeilen nach der aktuellen Zeile
- `UNBOUNDED FOLLOWING`, alle nachfolgenden Zeilen

### Schema zur Definition der Rahmengrenzen

![Definition der Grenzen des Fensterrahmens](https://sql-academy.org/static/guidePage/windows-functions-frames/window-definition.png "Definition der Grenzen des Fensterrahmens")

## Unterschied zwischen ROWS und RANGE

Zur Definition der Rahmengrenzen werden die Schlüsselwörter `ROWS` und `RANGE` verwendet. Sie funktionieren unterschiedlich:

### ROWS

- Basiert auf physischen Zeilen:

    Bei der Verwendung von `ROWS` basiert die Rahmendefinition auf der physischen Position der Zeilen relativ zur aktuellen Zeile. Zum Beispiel bedeutet `1 PRECEDING` eine Zeile vor der aktuellen.

- Exakte Grenze:

    Die Definition des Rahmens mit `ROWS` begrenzt die Anzahl der in den Rahmen aufgenommenen Zeilen exakt und macht ihn vorhersagbar und konkret.

![Definition der Rahmengrenzen mit ROWS](https://sql-academy.org/static/guidePage/windows-functions-frames/rows_example_de.png "Definition der Rahmengrenzen mit ROWS")

### RANGE

- Basiert auf Werten:

    `RANGE` legt im Gegensatz zu `ROWS` die Rahmengrenzen auf Basis der Spaltenwerte fest, die gemäß `ORDER BY` in der Window-Funktion geordnet sind.

- Dynamische Grenzen:

    Die mit `RANGE` festgelegten Grenzen können je nach Daten variieren, was den Rahmen flexibel macht, aber möglicherweise weniger vorhersagbar.

![Definition der Rahmengrenzen mit RANGE](https://sql-academy.org/static/guidePage/windows-functions-frames/range_example_de.png "Definition der Rahmengrenzen mit RANGE")

Lass uns prüfen, ob du den Unterschied zwischen `ROWS` und `RANGE` richtig verstanden hast:

**Welche der folgenden Aussagen beschreibt den Unterschied zwischen der Verwendung von `ROWS` und `RANGE` im Kontext der SQL-Window-Funktionen am besten?**

1. ROWS und RANGE sind austauschbar, da beide die physischen Zeilen im Fensterrahmen festlegen — Das ist falsch, denn ROWS basiert auf der physischen Position der Zeilen, während RANGE sich an den Spaltenwerten orientiert.

2. **Richtige Antwort:** ROWS legt die Rahmengrenzen auf Basis der physischen Position der Zeilen fest, RANGE auf Basis der Spaltenwerte. — ROWS konzentriert sich auf die physische Position der Zeilen, während RANGE die Spaltenwerte verwendet, um die Rahmengrenzen festzulegen.

3. RANGE verwendet die physische Anordnung der Zeilen, ROWS hingegen die Spaltenwerte. — Das ist falsch, denn ROWS basiert auf der physischen Position der Zeilen, im Gegensatz zu RANGE, das sich an den Spaltenwerten orientiert.

4. Sowohl ROWS als auch RANGE legen die Rahmengrenzen ausschließlich auf Basis der Spaltenwerte fest. — Das ist falsch, denn nur RANGE verwendet die Spaltenwerte, während ROWS sich an der physischen Anordnung der Zeilen orientiert.
