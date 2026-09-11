---
meta:
    title: "Indizes in SQL: schnellere Datensuche in MySQL und PostgreSQL"
    description: "Vollständiger Leitfaden zu Indizes in SQL: wie sie die Suche in Datenbanktabellen in MySQL und PostgreSQL vereinfachen und beschleunigen. Lerne, wie du Indizes erstellst, verwaltest und für bessere Abfrageperformance optimierst."
---

# Indizes in SQL

Wenn du eine neue Zeile in eine Tabelle einfügst, legt das DBMS die Daten nicht in einer optimalen Reihenfolge ab. Wenn du zum Beispiel eine Zeile in die Tabelle `Users` einfügst, sortiert das DBMS die Zeilen weder nach den Werten in der Spalte `id` noch alphabetisch nach `last_name`.
Stattdessen legt es die Daten einfach an die nächste freie Stelle in der Datei (das DBMS führt für jede Tabelle eine Liste freier Plätze).

Für eine Abfrage wie:

```sql
SELECT email FROM Users WHERE email LIKE 'l%';
```

muss der Datenbankserver also jede Zeile der Tabelle prüfen, um die Treffer zu finden.
Bei kleinen Tabellen ist das in Ordnung, aber sobald die Datenmenge wächst, wird es zu langsam.

Zum Vergleich, wie eine Suche nach `email` mit und ohne Index abschneidet:

![Vergleich der Suchgeschwindigkeit mit und ohne Index](https://sql-academy.org/static/guidePage/indexes/statistic_de.png "Vergleich der Suchgeschwindigkeit mit und ohne Index")

Indizes funktionieren wie das Stichwortverzeichnis in einem Buch 📖 — du findest die gewünschte Information schnell, ohne den ganzen Text lesen zu müssen. Es sind spezielle
Tabellen, deren Zeilen anders als bei normalen Datentabellen in einer streng definierten Reihenfolge stehen.
Statt aber alle Daten eines Datensatzes zu enthalten, speichert ein Index nur die Spalte (oder Spalten), nach der gesucht wird, plus eine Information, wo die zugehörige Zeile physisch liegt. Indizes helfen also, eine Teilmenge von Zeilen und Spalten zu finden, ohne jede Zeile der Tabelle scannen zu müssen.

## Index erstellen ✨

Zurück zur Tabelle `Users`: du kannst einen Index auf der Spalte `email` anlegen, um alle Abfragen zu beschleunigen, die mit dieser Spalte arbeiten.

**MySQL**

In MySQL legst du so einen Index folgendermaßen an:

```sql
CREATE INDEX idx_email
    ON Users (email);
```

Diese Anweisung erstellt einen Index mit dem Namen `idx_email` für die Spalte `Users.email`. Wenn der Index vorhanden ist, kann der Query-Optimizer ihn nutzen, sofern es sich lohnt. Hat eine Tabelle mehrere Indizes, entscheidet der Optimizer für jede einzelne SQL-Anweisung, welcher davon am besten passt.

Alle Datenbanksysteme bieten eine Möglichkeit, vorhandene Indizes anzusehen.
In MySQL gibt es dafür den Befehl `SHOW`, der alle Indizes einer Tabelle anzeigt:

```sql
SHOW INDEX FROM Users;
```

| Table | Non_unique | Key_name  | Seq_in_index | Column_name |
| ----- | ---------- | --------- | ------------ | ----------- |
| users | 0          | PRIMARY   | 1            | id          |
| users | 1          | idx_email | 1            | email       |

Die Ausgabe zeigt, dass die Tabelle `Users` zwei Indizes hat: einen für die Spalte `id` mit dem Namen `PRIMARY` und einen weiteren für die Spalte `email`, den wir gerade angelegt haben.

Beim Anlegen der Tabelle hat MySQL automatisch einen Index für die Primärschlüsselspalte erzeugt — in diesem Fall `id` — und ihm den Namen `PRIMARY` gegeben.
Das ist ein spezieller Indextyp, der mit dem Primary-Key-Constraint einhergeht und sicherstellt, dass jeder Wert in der Spalte (oder Spaltengruppe) eindeutig ist und nicht `NULL` sein darf.

**PostgreSQL**

In PostgreSQL legst du so einen Index folgendermaßen an:

```sql
CREATE INDEX idx_email
    ON Users (email);
```

Diese Anweisung erstellt einen Index mit dem Namen `idx_email` für die Spalte `Users.email`. Wenn der Index vorhanden ist, kann der Query-Optimizer ihn nutzen, sofern es sich lohnt. Hat eine Tabelle mehrere Indizes, entscheidet der Optimizer für jede einzelne SQL-Anweisung, welcher davon am besten passt.

Alle Datenbanksysteme bieten eine Möglichkeit, vorhandene Indizes anzusehen.
In PostgreSQL kannst du dafür die Systemtabellen abfragen:

```sql
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'users';
```

| indexname  | indexdef                                                        |
| ---------- | --------------------------------------------------------------- |
| users_pkey | CREATE UNIQUE INDEX users_pkey ON public.users USING btree (id) |
| idx_email  | CREATE INDEX idx_email ON public.users USING btree (email)      |

PostgreSQL erstellt automatisch einen Index für die Primärschlüsselspalte — in diesem Fall `id`. Das ist ein spezieller Indextyp, der mit dem Primary-Key-Constraint einhergeht und sicherstellt, dass jeder Wert in der Spalte (oder Spaltengruppe) eindeutig ist und nicht `NULL` sein darf.

PostgreSQL unterstützt verschiedene Indextypen:

- **B-Tree** (Standard) — für Vergleichs- und Sortieroperationen
- **Hash** — für Gleichheitsvergleiche
- **GIN** — für zusammengesetzte Werte (Arrays, JSON)
- **GiST** — für geometrische Daten und Volltextsuche
- **BRIN** — für sehr große Tabellen mit natürlicher Sortierung

## Index löschen

**MySQL**

Wenn du nach dem Anlegen eines Index merkst, dass du ihn nicht mehr brauchst, kannst du ihn so entfernen:

```sql
DROP INDEX idx_email ON Users;
```

**PostgreSQL**

Wenn du nach dem Anlegen eines Index merkst, dass du ihn nicht mehr brauchst, kannst du ihn so entfernen:

```sql
DROP INDEX idx_email;
```

## UNIQUE-Indizes

Beim Design einer Datenbank ist es wichtig zu entscheiden, in welchen Spalten Werte mehrfach vorkommen dürfen und in welchen nicht.

In der Tabelle `Users` kann es zum Beispiel mehrere Nutzer mit demselben Namen geben, aber ihre IDs und E-Mail-Adressen müssen unterschiedlich sein, damit sie unterscheidbar bleiben.

Eindeutigkeit lässt sich garantieren, indem du einen UNIQUE-Index auf die Spalte `Users.email` legst.
Ein UNIQUE-Index erfüllt zwei Aufgaben:

- er bietet alle Vorteile eines normalen Index
- er verhindert doppelte Werte in der indizierten Spalte

Das DBMS prüft den UNIQUE-Index bei jedem Insert oder Update der indizierten Spalte und stellt sicher, dass der neue Wert noch nicht in der Tabelle existiert.

Einen UNIQUE-Index für die Spalte `Users.email` legst du so an:

```sql
CREATE UNIQUE INDEX idx_email
    ON Users (email);
```

**MySQL**

Mit diesem Index bekommst du eine Fehlermeldung, sobald du einen neuen Kunden mit einer bereits vorhandenen E-Mail-Adresse einfügen willst:

```sql
Error(1062) 23000: "Duplicate entry 'duplicate@gmail.com' for key 'users.idx_email'"
```

**PostgreSQL**

Mit diesem Index bekommst du eine Fehlermeldung, sobald du einen neuen Kunden mit einer bereits vorhandenen E-Mail-Adresse einfügen willst:

```sql
ERROR: duplicate key value violates unique constraint "idx_email"
DETAIL: Key (email)=(duplicate@gmail.com) already exists.
```

UNIQUE-Indizes für die Primärschlüsselspalte(n) anzulegen ist überflüssig, da das DBMS die Eindeutigkeit von Primärschlüsselwerten automatisch erzwingt.
Mehrere UNIQUE-Indizes in einer Tabelle sind aber erlaubt und können sinnvoll sein, wenn du den Bedarf siehst.

## Mehrspaltige Indizes

Neben einspaltigen Indizes gibt es auch die Möglichkeit, Indizes über mehrere Spalten zu legen.
Um zum Beispiel Studierende nach Vor- und Nachname zu suchen, kannst du einen gemeinsamen Index über diese beiden Felder anlegen:

```sql
CREATE INDEX idx_full_name
    ON Student (last_name, first_name);
```

So ein Index hilft Abfragen, bei denen sowohl Vor- als auch Nachname vorkommen — oder nur der Nachname.
Bei Abfragen, die nur den Vornamen filtern, bringt er dagegen nichts.
Das ist wie die Suche nach einer Telefonnummer im Telefonbuch: kennst du Vor- und Nachname, hilft dir die Sortierung (erst nach Nachname, dann nach Vorname).
Kennst du nur den Vornamen, musst du alle Einträge durchgehen.

Bei mehrspaltigen Indizes ist die Reihenfolge der Spalten wichtig für die Effizienz.
Du kannst aber jederzeit zusätzlich weitere Indizes über dieselben Spalten in anderer Reihenfolge anlegen, falls bestimmte Abfragen das brauchen.

## Wie Indizes verwendet werden

Indizes werden vom DBMS verwendet, um die gesuchten Zeilen schnell zu finden — und dann ggf. weitere Daten aus verknüpften Tabellen nachzuholen.
Nehmen wir als Beispiel diese Abfrage:

```sql
SELECT id, first_name, last_name
  FROM Student
  WHERE first_name LIKE 'A%' AND last_name LIKE 'L%'
```

Auf so eine Abfrage hat das DBMS mehrere Optionen:

- die ganze Tabelle Zeile für Zeile scannen.
- den Index auf `last_name` nutzen, um Studierende mit Nachnamen, die mit „L“ anfangen, zu finden, und dann jede dieser Zeilen prüfen, ob der Vorname mit „A“ beginnt.
- den zusammengesetzten Index auf `last_name` und `first_name` nutzen, um direkt die Studierenden zu finden, die beide Kriterien erfüllen.

Die letzte Variante ist am effizientesten: in einem Durchgang alle passenden Zeilen finden, ohne nochmal zur Tabelle gehen zu müssen.

**MySQL**

Wie findet man heraus, welche Methode der MySQL-Query-Optimizer wählt?
Dafür gibt es den Befehl `EXPLAIN` — er zeigt, wie das DBMS die Abfrage ausführen würde, ohne sie tatsächlich auszuführen:

```sql
EXPLAIN
  SELECT id, first_name, last_name
  FROM Student
  WHERE first_name LIKE 'A%'
  AND last_name LIKE 'L%';
```

| id  | select_type | table   | partitions | possible_keys               | key           |
| --- | ----------- | ------- | ---------- | --------------------------- | ------------- |
| 1   | SIMPLE      | Student | null       | idx_full_name,idx_last_name | idx_full_name |

In der Spalte `possible_keys` sieht man, dass theoretisch sowohl `idx_last_name` als auch `idx_full_name` in Frage kommen — und in der Spalte `key` steht, dass `idx_full_name` ausgewählt wurde.

**PostgreSQL**

Wie findet man heraus, welche Methode der PostgreSQL-Query-Optimizer wählt?
Dafür gibt es den Befehl `EXPLAIN` — er zeigt, wie das DBMS die Abfrage ausführen würde:

```sql
EXPLAIN
  SELECT id, first_name, last_name
  FROM Student
  WHERE first_name LIKE 'A%'
  AND last_name LIKE 'L%';
```

| QUERY PLAN                                                                  |
| --------------------------------------------------------------------------- |
| Index Scan using idx_full_name on student (cost=0.42..8.44 rows=1 width=68) |
| Index Cond: ((last_name >= 'L'::text) AND (last_name \< 'M'::text))         |
| Filter: ((first_name \~\~ 'A%'::text) AND (last_name \~\~ 'L%'::text))      |

Mit `EXPLAIN ANALYZE` bekommst du zusätzlich die tatsächlichen Ausführungs-Statistiken:

```sql
EXPLAIN ANALYZE
  SELECT id, first_name, last_name
  FROM Student
  WHERE first_name LIKE 'A%'
  AND last_name LIKE 'L%';
```

| QUERY PLAN                                                                                                            |
| --------------------------------------------------------------------------------------------------------------------- |
| Index Scan using idx_full_name on student (cost=0.42..8.44 rows=1 width=68) (actual time=0.025..0.027 rows=1 loops=1) |
| Index Cond: ((last_name >= 'L'::text) AND (last_name \< 'M'::text))                                                   |
| Filter: ((first_name \~\~ 'A%'::text) AND (last_name \~\~ 'L%'::text))                                                |
| Planning Time: 0.156 ms                                                                                               |
| Execution Time: 0.048 ms                                                                                              |

Aus dem `EXPLAIN`-Ergebnis erkennst du, welche Zugriffsmethode der Optimizer gewählt hat — ein vollständiger Tabellen-Scan (Seq Scan) oder ein Index-Scan (Index Scan).

## Die Kehrseite von Indizes

Wenn Indizes so effektiv sind — warum nicht einfach alles indizieren? 🧐

Die Antwort: jeder Index ist selbst eine Tabelle (eine spezielle, aber doch eine Tabelle). Jedes Mal, wenn eine Zeile in die Tabelle eingefügt oder daraus gelöscht wird, müssen alle Indizes dieser Tabelle aktualisiert werden. Bei einem Update werden alle Indizes auf den betroffenen Spalten ebenfalls angepasst.
Je mehr Indizes du hast, desto mehr Arbeit muss das DBMS leisten, um alle Schemaobjekte konsistent zu halten — und desto langsamer wird die Schreibarbeit.

Außerdem belegen Indizes zusätzlichen Plattenplatz und brauchen Aufmerksamkeit von der DBA-Seite. Die Faustregel: leg einen Index nur dann an, wenn du ihn wirklich brauchst. Wird er nur temporär benötigt — etwa für einen Monatsreport — leg ihn vor dem Job an und lösche ihn danach wieder.

Am Ende geht es um das Gleichgewicht: genug Indizes, damit alles schnell läuft, aber nicht so viele, dass es die Performance frisst.
Wenn du dir bei der richtigen Anzahl unsicher bist, fang mit wenigen an und ergänze nach Bedarf.

**Lass uns prüfen, ob du das Thema verstanden hast:**

Welche Aussage erklärt am besten, warum du **nicht** jede Spalte in einer Datenbanktabelle indizieren solltest?

1. Indizes erhöhen die Geschwindigkeit aller Datenbankoperationen — auch von Insert, Update und Delete. — Indizes beschleunigen zwar Leseabfragen, können aber Insert-, Update- und Delete-Operationen verlangsamen, weil dabei zusätzlich die Indexstrukturen aktualisiert werden müssen.

2. Indizes brauchen keinen zusätzlichen Plattenplatz und können daher ohne Nachteile angelegt werden. — Indizes belegen zusätzlichen Plattenplatz — einer der Gründe, warum man nicht blind jede Spalte indizieren sollte.

3. Indizes machen Abfrageoptimierung überflüssig, weil sie alle Abfragen automatisch optimieren. — Indizes können die Performance einzelner Abfragen deutlich verbessern, ersetzen aber weder sorgfältige Abfrageoptimierung noch durchdachtes Schemadesign.

4. **Richtige Antwort:** Indizes beschleunigen Leseoperationen, können aber Schreiboperationen verlangsamen, weil jeder Index bei Datenänderungen aktualisiert werden muss. — Genau der Trade-off, der mit Indizes einhergeht. Sie machen das Lesen schneller — auf Kosten potenziell langsamerer Schreibvorgänge. Deshalb ist die Balance zwischen Anzahl der Indizes und ihrem Einfluss auf die Gesamtperformance wichtig.
