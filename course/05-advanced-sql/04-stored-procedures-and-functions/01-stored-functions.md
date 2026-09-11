---
meta:
    title: "Stored Functions in SQL"
    description: "Stored Functions in SQL erstellen und einsetzen. Syntax, Parameter, Rückgabetypen und praktische Beispiele."
---

# Stored Functions in SQL

Stored Functions sind ein mächtiges Werkzeug in SQL, mit dem du wiederverwendbare Codeblöcke für Berechnungen und Datenaufbereitung anlegen kannst.
Anders als eingebaute Funktionen schreibst du Stored Functions selbst, um spezifische Probleme zu lösen.

> **Eine Stored Function** ist ein benannter Block aus SQL-Code, der Parameter entgegennimmt, Berechnungen ausführt und stets einen Wert eines bestimmten Typs zurückgibt.

## Allgemeine Struktur einer Stored Function

**MySQL**

```sql
CREATE FUNCTION funktions_name(parameter1 TYP, parameter2 TYP, ...)
RETURNS rueckgabe_typ
BEGIN
    -- Logik der Funktion
    RETURN ergebnis_der_berechnung;
END;
```

**PostgreSQL**

```sql
CREATE OR REPLACE FUNCTION funktions_name(parameter1 TYP, parameter2 TYP, ...)
RETURNS rueckgabe_typ
LANGUAGE plpgsql
AS $$
BEGIN
    -- Logik der Funktion
    RETURN ergebnis_der_berechnung;
END;
$$;
```

`LANGUAGE plpgsql` — gibt an, dass die Funktion in **PL/pgSQL** geschrieben ist (der prozeduralen Sprache von PostgreSQL).

`AS $$ ... $$` — sogenanntes **Dollar-Quoting**, eine spezielle Art, den Funktionskörper zu umschließen. Damit musst du Zeichen innerhalb der Funktion nicht escapen.

## Einfaches Beispiel

Bauen wir eine Funktion, die anhand des Geburtsdatums prüft, ob jemand volljährig ist:

**MySQL**

```sql
CREATE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) >= 18;
END;
```

**PostgreSQL**

```sql
CREATE OR REPLACE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN EXTRACT(YEAR FROM AGE(CURRENT_DATE, birth_date)) >= 18;
END;
$$;
```

Diese Funktion kannst du jetzt in beliebigen Queries verwenden:

**MySQL**

```sql
-- Funktion anlegen
CREATE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) >= 18;
END;

-- Funktion verwenden
SELECT
    is_adult('2010-05-15') AS child_status,
    is_adult('2000-03-20') AS adult_status;
```

**PostgreSQL**

```sql
-- Funktion anlegen
CREATE OR REPLACE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN EXTRACT(YEAR FROM AGE(CURRENT_DATE, birth_date)) >= 18;
END;
$$;

-- Funktion verwenden
SELECT
    is_adult('2010-05-15') AS child_status,
    is_adult('2000-03-20') AS adult_status;
```

**MySQL**

| child_status | adult_status |
| ------------ | ------------ |
| 0            | 1            |

**PostgreSQL**

| child_status | adult_status |
| ------------ | ------------ |
| false        | true         |

## Funktionen in Queries auf Tabellen einsetzen

Stored Functions sind besonders praktisch bei der Arbeit mit echten Daten. Zum Beispiel können wir unsere Funktion verwenden, um Studierende nach Alter zu filtern:

**MySQL**

```sql
-- Funktion anlegen
CREATE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
BEGIN
    RETURN TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) >= 18;
END;

-- Funktion in einer Query auf einer Tabelle nutzen
SELECT
    first_name,
    last_name,
    birthday,
    is_adult(birthday) AS is_adult
FROM Student
WHERE is_adult(birthday) = TRUE
LIMIT 5;
```

**PostgreSQL**

```sql
-- Funktion anlegen
CREATE OR REPLACE FUNCTION is_adult(birth_date DATE)
RETURNS BOOLEAN
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN EXTRACT(YEAR FROM AGE(CURRENT_DATE, birth_date)) >= 18;
END;
$$;

-- Funktion in einer Query auf einer Tabelle nutzen
SELECT
    first_name,
    last_name,
    birthday,
    is_adult(birthday) AS is_adult
FROM Student
WHERE is_adult(birthday) = TRUE
LIMIT 5;
```

**MySQL**

| first_name | last_name | birthday                 | is_adult |
| ---------- | --------- | ------------------------ | -------- |
| Nikolaj    | Sokolov   | 2000-10-01T00:00:00.000Z | 1        |
| Vyacheslav | Eliseev   | 2000-11-21T00:00:00.000Z | 1        |
| Ivan       | Efremov   | 2000-09-19T00:00:00.000Z | 1        |
| Anatolij   | ZHdanov   | 2007-07-15T00:00:00.000Z | 1        |
| Georgij    | Noskov    | 2000-03-03T00:00:00.000Z | 1        |

**PostgreSQL**

| first_name | last_name | birthday                 | is_adult |
| ---------- | --------- | ------------------------ | -------- |
| Nikolaj    | Sokolov   | 2000-10-01T00:00:00.000Z | true     |
| Vyacheslav | Eliseev   | 2000-11-21T00:00:00.000Z | true     |
| Ivan       | Efremov   | 2000-09-19T00:00:00.000Z | true     |
| Anatolij   | ZHdanov   | 2007-07-15T00:00:00.000Z | true     |
| Georgij    | Noskov    | 2000-03-03T00:00:00.000Z | true     |

## Funktionen mit SQL-Queries

Stored Functions können in ihrem Inneren selbst SQL-Queries ausführen, um die benötigten Daten zu holen:

**MySQL**

```sql
CREATE FUNCTION get_student_lessons_count(student_id INT, target_date DATE)
RETURNS INT
BEGIN
    DECLARE lessons_count INT;

    SELECT COUNT(*) INTO lessons_count
    FROM Schedule s
    INNER JOIN Student_in_class sic ON s.class = sic.class
    WHERE sic.student = student_id
      AND s.date = target_date;

    RETURN lessons_count;
END;
```

**PostgreSQL**

```sql
CREATE OR REPLACE FUNCTION get_student_lessons_count(student_id INT, target_date DATE)
RETURNS INT
LANGUAGE plpgsql
AS $$
DECLARE
    lessons_count INT;
BEGIN
    SELECT COUNT(*) INTO lessons_count
    FROM Schedule s
    INNER JOIN Student_in_class sic ON s.class = sic.class
    WHERE sic.student = student_id
      AND s.date = target_date;

    RETURN lessons_count;
END;
$$;
```

Diese Funktion zählt die Anzahl der Unterrichtsstunden eines bestimmten Studenten an einem bestimmten Tag:

**MySQL**

```sql
SELECT get_student_lessons_count(1, '2019-09-01') AS lessons_today;
```

**PostgreSQL**

```sql
SELECT get_student_lessons_count(1, '2019-09-01') AS lessons_today;
```

| lessons_today |
| ------------- |
| 3             |

## Variablen Schritt für Schritt erklärt

Bislang sind wir Variablen in SQL noch nicht begegnet — bei Stored Functions und Procedures sind sie aber ein wichtiges Konzept.
Schauen wir uns das vorige Beispiel deshalb Schritt für Schritt an:

```sql
DECLARE lessons_count INT;
```

Diese Zeile **deklariert die Variable** `lessons_count` vom Typ `INT`. Die Variable nimmt das Ergebnis unserer Query auf.

**PostgreSQL**

> **Wichtig für PostgreSQL:** Alle Variablen müssen im `DECLARE`-Block vor dem Funktionskörper (also vor `BEGIN`) deklariert werden. Im Funktionskörper selbst geht das nicht.

```sql
SELECT COUNT(*) INTO lessons_count
FROM Schedule s
INNER JOIN Student_in_class sic ON s.class = sic.class
WHERE sic.student = student_id
  AND s.date = target_date;
```

Hier wird das **Ergebnis der Query in einer Variablen gespeichert**:

- `SELECT COUNT(*)` — zählt die Datensätze
- `INTO lessons_count` — speichert das Ergebnis in der Variablen `lessons_count`
- Der Rest ist eine ganz normale SQL-Query mit JOIN und Bedingungen

```sql
RETURN lessons_count;
```

Wir **geben den Wert** der Variablen als Ergebnis der Funktion zurück.

> **Wichtig:** Die Konstruktion `INTO` speichert das Ergebnis einer SELECT-Query in einer Variablen. Das ist die Grundlage für die Datenarbeit innerhalb von Stored Functions.

## Stored Functions verwalten

- **Vorhandene Funktionen anzeigen**

    **MySQL**

    ```sql
    SHOW FUNCTION STATUS WHERE Db = 'your_database_name';
    ```

    **PostgreSQL**

    ```sql
    SELECT routine_name, routine_type
    FROM information_schema.routines
    WHERE routine_type = 'FUNCTION' AND routine_schema = 'public';
    ```

- **Funktion löschen**

    **MySQL**

    ```sql
    DROP FUNCTION IF EXISTS is_adult;
    ```

    **PostgreSQL**

    ```sql
    DROP FUNCTION IF EXISTS is_adult(DATE);
    ```

- **Funktion ändern**

    **MySQL**

    Zum Ändern einer Funktion in MySQL musst du die alte Version erst löschen und dann eine neue anlegen:

    ```sql
    DROP FUNCTION IF EXISTS is_adult;
    -- Neue Version der Funktion anlegen
    CREATE FUNCTION is_adult(birth_date DATE) ...
    ```

    **PostgreSQL**

    In PostgreSQL kannst du `CREATE OR REPLACE FUNCTION` verwenden:

    ```sql
    CREATE OR REPLACE FUNCTION is_adult(birth_date DATE)
    RETURNS BOOLEAN
    -- neue Implementierung
    ```

Stored Functions sind ein mächtiges Werkzeug, um wiederverwendbare Business-Logik direkt in der Datenbank zu bündeln. Sie helfen, Berechnungen zu zentralisieren und die Datenkonsistenz in der gesamten Anwendung sicherzustellen! 🚀
