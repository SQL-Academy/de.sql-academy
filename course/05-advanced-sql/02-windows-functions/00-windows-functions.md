---
meta:
    title: "Window-Funktionen in SQL: MySQL und PostgreSQL"
    description: "Window-Funktionen in MySQL und PostgreSQL, Syntax von OVER, Datenfenster, Beispiel für die Verwendung einer Window-Funktion, Ausführungsreihenfolge von Window-Funktionen in einer SELECT-Abfrage"
---

# Window-Funktionen in SQL

Window-Funktionen sind ein mächtiges Werkzeug der Sprache SQL, mit dem sich komplexe Berechnungen über Gruppen von Zeilen durchführen lassen, die mit der aktuellen Zeile in Beziehung stehen.

## Funktionsweise

Vielleicht fragst du dich: „Was bedeutet eigentlich Window?“.

In einer Standard-SQL-Abfrage werden alle Zeilenmengen als ein einziger zusammenhängender Datenblock betrachtet, für den die Aggregatwerte berechnet werden.

Wenn aber Window-Funktionen verwendet werden, wird die Abfrage in Gruppen von Zeilen segmentiert (sogenannte „Fenster“), und für jedes dieser Segmente werden individuelle Aggregatwerte berechnet.

Dieses Fenster, das an die Window-Funktion übergeben wird, kann sein:

- die gesamte Tabelle
- einzelne Partitionen der Tabelle, also eine Gruppe von Zeilen auf Basis eines oder mehrerer Felder
- oder sogar ein konkreter Zeilenbereich innerhalb einer Tabelle oder Partition. Wir können zum Beispiel ein Fenster definieren, das an die Window-Funktion übergeben wird, als die vorherige + die aktuelle Zeile der Tabelle. Dann wird für jede Zeile der Wert der Aggregatfunktion auf eine eigene Weise berechnet, weil sich die Daten, die in die Funktion fließen, dynamisch von Zeile zu Zeile ändern. Das Fenster „gleitet“ dann sozusagen über die Tabelle.

### Visualisierung

Window-Funktionen erhalten als Eingabe immer ein Datenfenster, das der Nutzer angibt, und geben das Ergebnis in einer eigenen Spalte zurück.

Schauen wir uns an, wie das aussehen kann. Dafür nehmen wir die Window-Funktion `AVG` zur Berechnung des Durchschnittswerts und folgende kleine Tabelle:

![Ausgangstabelle](https://sql-academy.org/static/guidePage/windows-functions/schema_table_de.png "Ausgangstabelle")

Und jetzt schauen wir uns an, wie die Window-Funktion bei verschiedenen übergebenen Fenstern arbeitet:

- Wenn als Fenster die gesamte Tabelle angegeben wird, ist das Fenster für alle Zeilen identisch und der Funktion `AVG` wird derselbe Datensatz übergeben, entsprechend ist das Ergebnis gleich.

    ![Schema der Aufteilung in Partitionen](https://sql-academy.org/static/guidePage/windows-functions/2_de.png "Schema der Aufteilung in Partitionen")

- Wenn als Fenster eine Partition nach dem Feld `home_type` angegeben wird, wird der Funktion `AVG` eine Menge von Unterkünften mit demselben Typ übergeben, und entsprechend wird in der neuen Spalte der durchschnittliche Preis für Unterkünfte angezeigt, deren Typ mit dem Typ in der aktuellen Zeile der Tabelle übereinstimmt.

    ![Schema der Aufteilung in Partitionen](https://sql-academy.org/static/guidePage/windows-functions/3_de.png "Schema der Aufteilung in Partitionen")

- Als Fenster kann auch eine spezifischere Menge von Zeilen angegeben werden. Zum Beispiel kann das Fenster als "vorherige + aktuelle Zeile" der Tabelle definiert werden. Dann sieht es folgendermaßen aus:

    ![Schema der Aufteilung in Partitionen](https://sql-academy.org/static/guidePage/windows-functions/4_de.png "Schema der Aufteilung in Partitionen")

    Anzumerken ist, dass das Fenster für die erste Zeile nur aus 1 Datensatz besteht, weil es keine vorherige Zeile gibt.

## Syntax einer Window-Funktion

```sql
SELECT <window_funktion>(<tabellenfeld>)
OVER (
      [PARTITION BY <spalten_zum_partitionieren>]
      [ORDER BY <spalten_zum_sortieren>]
      [ROWS|RANGE <definition_des_zeilenbereichs>]
)
```

Wobei:

- `<window_funktion>(<tabellenfeld>)` — die verwendete Window-Funktion. Zum Beispiel `AVG(price)`.
- Danach folgt `OVER`, das das Fenster (die Gruppe von Zeilen) definiert, das an die Window-Funktion übergeben wird. Bleibt die Konstruktion `OVER ()` ohne Parameter, dient die gesamte Tabelle als Fenster.

Innerhalb von `OVER` folgen dann 3 optionale Parameter, mit denen sich das Fenster flexibel konfigurieren lässt:

- mit `PARTITION BY <spalten_zum_partitionieren>` wird die Ergebnismenge in nicht überlappende Teilmengen aufgeteilt, wobei jede Teilmenge Zeilen mit gleichen Werten in einer oder mehreren Spalten enthält. So entstehen Partitionen.
- mit `ORDER BY <spalten_zum_sortieren>` wird die Reihenfolge der Zeilen innerhalb des Fensters festgelegt. Das spielt besonders bei Rang-Window-Funktionen eine wichtige Rolle.
- mit `ROWS|RANGE <definition_des_zeilenbereichs>` werden Zeilenbereiche gebildet. Mit diesem Parameter lässt sich angeben, wie viele Zeilen vor und nach der aktuellen ins Fenster aufgenommen werden.

Auf jeden dieser Parameter gehen wir in den folgenden Artikeln genauer ein.

## Beispiel für die Verwendung einer Window-Funktion

Versuchen wir, mit Hilfe von Window-Funktionen eine Liste der Namen der Schüler und die Anzahl der Personen in ihrer Klasse zu erhalten.

ER-Diagramm der Datenbank Schedule: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/windows-functions).

Holen wir uns zunächst die Liste der Schüler und die ID der Klasse, in der sie lernen:

```sql
SELECT
    Student.first_name,
    Student.last_name,
    Student_in_class.class
FROM
    Student_in_class
JOIN
    Student ON Student_in_class.student = Student.id;
```

| first_name | last_name    | class |
| ---------- | ------------ | ----- |
| Nikolaj    | Sokolov      | 9     |
| Vyacheslav | Eliseev      | 9     |
| Ivan       | Efremov      | 9     |
| Anatolij   | ZHdanov      | 9     |
| Georgij    | Noskov       | 9     |
| Artyom     | Sergeev      | 9     |
| Arina      | Evseeva      | 9     |
| Angelina   | Voroncova    | 9     |
| Ekaterina  | Ustinova     | 9     |
| Raisa      | Lapina       | 9     |
| Leonid     | Ignatov      | 9     |
| Snezhana   | Seliverstova | 9     |
| Semyon     | Biryukov     | 9     |
| Georgij    | Baranov      | 8     |
| YUliya     | Vishnyakova  | 8     |
| Valentina  | Bolshakova   | 8     |
| Leonid     | Kryukov      | 8     |
| Vladislav  | Cvetkov      | 8     |
| Snezhana   | Morozova     | 8     |
| Lyubov     | Borisova     | 8     |
| Anfisa     | Kalashnikova | 8     |
| Anna       | Osipova      | 8     |
| Kristina   | Myasnikova   | 8     |
| Kristina   | Smirnova     | 8     |
| Boris      | Simonov      | 7     |
| Dmitrij    | Trofimov     | 7     |
| YAkov      | Rozhkov      | 7     |
| Fyodor     | Drozdov      | 7     |
| Gleb       | Strelkov     | 7     |
| Angelina   | Lukina       | 7     |
| Nina       | Odincova     | 7     |
| Valeriya   | Novikova     | 7     |
| Grigorij   | Kapustin     | 7     |
| Vitalij    | Panfilov     | 7     |
| Svyatoslav | Tarasov      | 6     |
| Matvej     | YAkushev     | 6     |
| Ilya       | Alekseev     | 6     |
| Lyubov     | Zaharova     | 6     |
| Polina     | Sidorova     | 6     |
| Elizaveta  | Samojlova    | 6     |
| YUliya     | Avdeeva      | 6     |
| Matvej     | Bogdanov     | 6     |
| Ilya       | Filippov     | 6     |
| Denis      | Mel          | 6     |
| Svyatoslav | Muravyov     | 6     |
| Anna       | Kulagina     | 5     |
| ZHanna     | Fokina       | 5     |
| Valeriya   | Lapina       | 5     |
| Valentina  | Sazonova     | 5     |
| Nataliya   | Myasnikova   | 5     |
| Viktoriya  | Makarova     | 5     |
| Stanislav  | Lazarev      | 5     |
| Gennadij   | Ovchinnikov  | 5     |
| Roman      | SHilov       | 4     |
| Timur      | Subbotin     | 4     |
| Danila     | Osipov       | 4     |
| Arina      | Silina       | 4     |
| Nadezhda   | Zaharova     | 4     |
| Larisa     | SHCHerbakova | 4     |
| Aleksandra | Belozyorova  | 4     |
| Natalya    | Davydova     | 4     |
| Mariya     | Fadeeva      | 4     |
| YUrij      | Markov       | 3     |
| Kirill     | SHubin       | 3     |
| Grigorij   | Kolobov      | 3     |
| Semyon     | Trofimov     | 3     |
| Vasilij    | Ustinov      | 3     |
| Valentina  | SHarova      | 3     |
| Larisa     | Savina       | 3     |
| Galina     | Orekhova     | 3     |
| Arina      | SHarapova    | 2     |
| Viktoriya  | Sergeeva     | 2     |
| Vasilij    | Krasilnikov  | 2     |
| Timur      | Rusakov      | 2     |
| Gleb       | Nesterov     | 2     |
| Denis      | Makarov      | 2     |
| Elizaveta  | SHilova      | 2     |
| Vera       | Evseeva      | 1     |
| Margarita  | Kabanova     | 1     |
| Angelina   | Lazareva     | 1     |
| Semyon     | Voronov      | 1     |
| Innokentij | Nekrasov     | 1     |
| Artyom     | Nikitin      | 1     |
| Egor       | Belyakov     | 1     |

Und jetzt, um zu berechnen, wie viele Schüler in jeder Klasse lernen, und um diese Information in einer neuen Spalte auszugeben, können wir eine Window-Funktion verwenden:

```sql
SELECT
    Student.first_name,
    Student.last_name,
    Student_in_class.class,
    COUNT(*) OVER (PARTITION BY Student_in_class.class) AS student_count_in_class
FROM
    Student_in_class
JOIN
    Student ON Student_in_class.student = Student.id;
```

| first_name | last_name    | class | student_count_in_class |
| ---------- | ------------ | ----- | ---------------------- |
| Egor       | Belyakov     | 1     | 7                      |
| Artyom     | Nikitin      | 1     | 7                      |
| Innokentij | Nekrasov     | 1     | 7                      |
| Semyon     | Voronov      | 1     | 7                      |
| Angelina   | Lazareva     | 1     | 7                      |
| Margarita  | Kabanova     | 1     | 7                      |
| Vera       | Evseeva      | 1     | 7                      |
| Denis      | Makarov      | 2     | 7                      |
| Arina      | SHarapova    | 2     | 7                      |
| Viktoriya  | Sergeeva     | 2     | 7                      |
| Vasilij    | Krasilnikov  | 2     | 7                      |
| Timur      | Rusakov      | 2     | 7                      |
| Gleb       | Nesterov     | 2     | 7                      |
| Elizaveta  | SHilova      | 2     | 7                      |
| Kirill     | SHubin       | 3     | 8                      |
| YUrij      | Markov       | 3     | 8                      |
| Grigorij   | Kolobov      | 3     | 8                      |
| Semyon     | Trofimov     | 3     | 8                      |
| Valentina  | SHarova      | 3     | 8                      |
| Larisa     | Savina       | 3     | 8                      |
| Galina     | Orekhova     | 3     | 8                      |
| Vasilij    | Ustinov      | 3     | 8                      |
| Timur      | Subbotin     | 4     | 9                      |
| Roman      | SHilov       | 4     | 9                      |
| Danila     | Osipov       | 4     | 9                      |
| Arina      | Silina       | 4     | 9                      |
| Nadezhda   | Zaharova     | 4     | 9                      |
| Larisa     | SHCHerbakova | 4     | 9                      |
| Aleksandra | Belozyorova  | 4     | 9                      |
| Natalya    | Davydova     | 4     | 9                      |
| Mariya     | Fadeeva      | 4     | 9                      |
| Gennadij   | Ovchinnikov  | 5     | 8                      |
| Stanislav  | Lazarev      | 5     | 8                      |
| Viktoriya  | Makarova     | 5     | 8                      |
| Nataliya   | Myasnikova   | 5     | 8                      |
| Valentina  | Sazonova     | 5     | 8                      |
| Valeriya   | Lapina       | 5     | 8                      |
| ZHanna     | Fokina       | 5     | 8                      |
| Anna       | Kulagina     | 5     | 8                      |
| Ilya       | Filippov     | 6     | 11                     |
| Svyatoslav | Muravyov     | 6     | 11                     |
| Denis      | Mel          | 6     | 11                     |
| Matvej     | Bogdanov     | 6     | 11                     |
| YUliya     | Avdeeva      | 6     | 11                     |
| Elizaveta  | Samojlova    | 6     | 11                     |
| Polina     | Sidorova     | 6     | 11                     |
| Lyubov     | Zaharova     | 6     | 11                     |
| Ilya       | Alekseev     | 6     | 11                     |
| Matvej     | YAkushev     | 6     | 11                     |
| Svyatoslav | Tarasov      | 6     | 11                     |
| Nina       | Odincova     | 7     | 10                     |
| Boris      | Simonov      | 7     | 10                     |
| Dmitrij    | Trofimov     | 7     | 10                     |
| YAkov      | Rozhkov      | 7     | 10                     |
| Fyodor     | Drozdov      | 7     | 10                     |
| Gleb       | Strelkov     | 7     | 10                     |
| Angelina   | Lukina       | 7     | 10                     |
| Valeriya   | Novikova     | 7     | 10                     |
| Grigorij   | Kapustin     | 7     | 10                     |
| Vitalij    | Panfilov     | 7     | 10                     |
| Anna       | Osipova      | 8     | 11                     |
| Georgij    | Baranov      | 8     | 11                     |
| YUliya     | Vishnyakova  | 8     | 11                     |
| Valentina  | Bolshakova   | 8     | 11                     |
| Leonid     | Kryukov      | 8     | 11                     |
| Vladislav  | Cvetkov      | 8     | 11                     |
| Lyubov     | Borisova     | 8     | 11                     |
| Anfisa     | Kalashnikova | 8     | 11                     |
| Snezhana   | Morozova     | 8     | 11                     |
| Kristina   | Myasnikova   | 8     | 11                     |
| Kristina   | Smirnova     | 8     | 11                     |
| Vyacheslav | Eliseev      | 9     | 13                     |
| Ivan       | Efremov      | 9     | 13                     |
| Anatolij   | ZHdanov      | 9     | 13                     |
| Georgij    | Noskov       | 9     | 13                     |
| Artyom     | Sergeev      | 9     | 13                     |
| Arina      | Evseeva      | 9     | 13                     |
| Angelina   | Voroncova    | 9     | 13                     |
| Ekaterina  | Ustinova     | 9     | 13                     |
| Raisa      | Lapina       | 9     | 13                     |
| Leonid     | Ignatov      | 9     | 13                     |
| Snezhana   | Seliverstova | 9     | 13                     |
| Semyon     | Biryukov     | 9     | 13                     |
| Nikolaj    | Sokolov      | 9     | 13                     |

### Was unsere Window-Funktion macht

Der Ausdruck `PARTITION BY Student_in_class.class` teilt alle Zeilen der Tabelle in Partitionen nach dem Feld `class` auf. Für jede Zeile werden der Window-Funktion also nur die Zeilen der Tabelle übergeben, in denen das Feld `class` mit dem Feld `class` der aktuellen Zeile übereinstimmt.

Die Funktion `COUNT` gibt die Anzahl der ihr übergebenen Zeilen zurück, und so bekommen wir, wie viele Schüler in jeder Klasse lernen.

## Ausführungsreihenfolge von Window-Funktionen in SELECT

Bei der Verwendung von Window-Funktionen ist es wichtig zu verstehen, in welcher Reihenfolge sie ausgeführt werden. Wie wir im Schema unten sehen können, werden die Fenster im vorletzten Schritt verarbeitet, also nach dem Filtern und Gruppieren, aber vor dem finalen Sortieren der Ergebnisse.

![Ausführungsreihenfolge der Window-Funktion in einer SELECT-Abfrage](https://sql-academy.org/static/guidePage/windows-functions/query-order_de.png "Ausführungsreihenfolge der Window-Funktion in einer SELECT-Abfrage")

## Fazit

In diesem Artikel haben wir das Konzept der Window-Funktionen, ihre Möglichkeiten und ihren praktischen Nutzen kurz betrachtet. In den nächsten Artikeln gehen wir auf jeden Aspekt der Window-Funktionen ausführlicher ein.

Und zum Schluss prüfen wir, ob wir alles verstanden haben:

**Was ist der zentrale Unterschied zwischen Window-Funktionen und Aggregatfunktionen mit Gruppierung in SQL?**

1. Window-Funktionen und Aggregatfunktionen mit Gruppierung führen dieselben Berechnungen durch, nur mit unterschiedlicher Syntax. — Window-Funktionen und Aggregatfunktionen mit Gruppierung haben unterschiedliche Funktionalitäten und können nicht austauschbar verwendet werden.

2. **Richtige Antwort:** Window-Funktionen werden für jede Zeile unabhängig berechnet und liefern das Ergebnis in einer separaten Spalte. Aggregatfunktionen mit Gruppierung gruppieren hingegen die Zeilen und werden auf die gebildeten Gruppen angewendet. — Window-Funktionen liefern Berechnungen für jede Zeile unter Berücksichtigung einer Menge von Zeilen (eines Fensters), die mit der aktuellen Zeile in Beziehung steht, während Aggregatfunktionen mit Gruppierung ein einzelnes Ergebnis pro Gruppe liefern, die nach dem Gruppierungskriterium gebildet wurde.

3. In Window-Funktionen wird PARTITION BY verwendet, in Aggregatfunktionen mit Gruppierung nicht. — Obwohl PARTITION BY tatsächlich eine Besonderheit der Window-Funktionen ist, liegt der zentrale Unterschied darin, wie die Funktionen auf die Daten angewendet werden (zeilenweise vs. gruppenweise).
