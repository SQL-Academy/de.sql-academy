---
meta:
    title: "IF, CASE und WHILE in Stored Procedures und Functions"
    description: "Lerne Verzweigungen und Schleifen in Stored Procedures und Functions kennen. Syntax und Beispiele für IF, CASE und WHILE in MySQL und PostgreSQL."
---

**MySQL**

# IF, CASE und WHILE in Stored Procedures

**PostgreSQL**

# IF, CASE und WHILE in Stored Functions

Stored Procedures und Functions sind nicht nur bequeme Container für eine Gruppe von Queries. Sie erlauben dir auch, recht komplexe Logik mit Verzweigungen und Schleifen umzusetzen.

In diesem Artikel schauen wir uns die wichtigsten Kontrollstrukturen an: die Verzweigungen `IF` und `CASE` sowie die `WHILE`-Schleife.

## Die IF-Anweisung

`IF` führt Code abhängig davon aus, ob eine Bedingung erfüllt ist.

### Syntax von IF

**MySQL**

```sql
IF bedingung THEN
    anweisungen
[ELSEIF bedingung THEN
    anweisungen] ...
[ELSE
    anweisungen]
END IF;
```

- `bedingung` — Ein logischer Ausdruck, zum Beispiel `student_age < 18`. Es greift der erste Zweig,
  dessen Bedingung wahr ist, die übrigen werden übersprungen.
- `anweisungen` — Eine oder mehrere SQL-Anweisungen, jede endet mit `;`. Sie laufen, wenn dieser Zweig
  gewählt wird.
- `ELSEIF` (Schlüsselwort, optional) — Ein weiterer Zweig mit eigener Bedingung. Er wird nur geprüft, wenn alle Bedingungen
  darüber falsch sind. Du kannst mehrere solcher Zweige anlegen.
- `ELSE` (Schlüsselwort, optional) — Der Zweig, der läuft, wenn keine der Bedingungen wahr war.

**PostgreSQL**

```sql
IF bedingung THEN
    anweisungen
[ELSIF bedingung THEN
    anweisungen] ...
[ELSE
    anweisungen]
END IF;
```

- `bedingung` — Ein logischer Ausdruck, zum Beispiel `student_age < 18`. Es greift der erste Zweig,
  dessen Bedingung wahr ist, die übrigen werden übersprungen.
- `anweisungen` — Eine oder mehrere SQL-Anweisungen, jede endet mit `;`. Sie laufen, wenn dieser Zweig
  gewählt wird.
- `ELSIF` (Schlüsselwort, optional) — Ein weiterer Zweig mit eigener Bedingung. Er wird nur geprüft, wenn alle Bedingungen
  darüber falsch sind. Du kannst mehrere solcher Zweige anlegen.
- `ELSE` (Schlüsselwort, optional) — Der Zweig, der läuft, wenn keine der Bedingungen wahr war.

### Beispiel für IF

**MySQL**

Wir bauen eine Procedure, die einen Studenten anhand seines Alters in eine Kategorie einordnet:

```sql
CREATE PROCEDURE categorize_student_by_age(
    IN student_id INT,
    OUT category VARCHAR(20)
)
BEGIN
    DECLARE student_age INT;

    -- Alter des Studenten ermitteln
    SELECT TIMESTAMPDIFF(YEAR, birthday, CURDATE())
    INTO student_age
    FROM Student
    WHERE id = student_id;

    -- Kategorie anhand des Alters bestimmen
    IF student_age < 18 THEN
        SET category = 'Minderjährig';
    ELSEIF student_age BETWEEN 18 AND 25 THEN
        SET category = 'Jung';
    ELSE
        SET category = 'Erwachsen';
    END IF;
END;

-- Aufruf der Procedure
CALL categorize_student_by_age(1, @category);
SELECT @category AS age_category;
```

**PostgreSQL**

Wir bauen eine Function, die einen Studenten anhand seines Alters in eine Kategorie einordnet:

```sql
CREATE OR REPLACE FUNCTION categorize_student_by_age(student_id INT)
RETURNS VARCHAR(20)
LANGUAGE plpgsql
AS $$
DECLARE
    student_age INT;
    category VARCHAR(20);
BEGIN
    -- Alter des Studenten ermitteln
    SELECT EXTRACT(YEAR FROM AGE(CURRENT_DATE, birthday))
    INTO student_age
    FROM Student
    WHERE id = student_id;

    -- Kategorie anhand des Alters bestimmen
    IF student_age < 18 THEN
        category := 'Minderjährig';
    ELSIF student_age BETWEEN 18 AND 25 THEN
        category := 'Jung';
    ELSE
        category := 'Erwachsen';
    END IF;

    RETURN category;
END;
$$;

-- Aufruf der Function
SELECT categorize_student_by_age(1) AS age_category;
```

**MySQL**

| age_category |
| ------------ |
| Jung         |

## Die CASE-Anweisung

`CASE` bietet eine elegantere Möglichkeit, mehrere Bedingungen zu verarbeiten.

### Syntax von CASE

**MySQL**

```sql
CASE
    WHEN bedingung THEN ergebnis
    [WHEN ...]
    [ELSE standard_ergebnis]
END
```

- `bedingung` — Ein logischer Ausdruck, der in seinem `WHEN`-Zweig geprüft wird. Es kann mehrere
  Zweige geben: Es greift der erste, dessen Bedingung wahr ist.
- `ergebnis` — Der Wert, den `CASE` annimmt, wenn die Bedingung dieses Zweigs wahr ist.
- `standard_ergebnis` (optional) — Der Wert für den Fall, dass keine Bedingung zutrifft. Ohne `ELSE` kommt `NULL` zurück.

**PostgreSQL**

```sql
CASE
    WHEN bedingung THEN ergebnis
    [WHEN ...]
    [ELSE standard_ergebnis]
END
```

- `bedingung` — Ein logischer Ausdruck, der in seinem `WHEN`-Zweig geprüft wird. Es kann mehrere
  Zweige geben: Es greift der erste, dessen Bedingung wahr ist.
- `ergebnis` — Der Wert, den `CASE` annimmt, wenn die Bedingung dieses Zweigs wahr ist.
- `standard_ergebnis` (optional) — Der Wert für den Fall, dass keine Bedingung zutrifft. Ohne `ELSE` kommt `NULL` zurück.

### Beispiel für CASE

**MySQL**

Wir bauen dieselbe Kategorisierungs-Procedure, diesmal mit `CASE`:

```sql
CREATE PROCEDURE categorize_student_with_case(
    IN student_id INT,
    OUT category VARCHAR(20)
)
BEGIN
    DECLARE student_age INT;

    -- Alter des Studenten ermitteln
    SELECT TIMESTAMPDIFF(YEAR, birthday, CURDATE())
    INTO student_age
    FROM Student
    WHERE id = student_id;

    -- Kategorie mit CASE bestimmen
    SET category = CASE
        WHEN student_age < 18 THEN 'Minderjährig'
        WHEN student_age BETWEEN 18 AND 25 THEN 'Jung'
        ELSE 'Erwachsen'
    END;
END;

-- Aufruf der Procedure
CALL categorize_student_with_case(1, @category);
SELECT @category AS age_category;
```

**PostgreSQL**

Wir bauen dieselbe Kategorisierungs-Function, diesmal mit `CASE`:

```sql
CREATE OR REPLACE FUNCTION categorize_student_with_case(student_id INT)
RETURNS VARCHAR(20)
LANGUAGE plpgsql
AS $$
DECLARE
    student_age INT;
    category VARCHAR(20);
BEGIN
    -- Alter des Studenten ermitteln
    SELECT EXTRACT(YEAR FROM AGE(CURRENT_DATE, birthday))
    INTO student_age
    FROM Student
    WHERE id = student_id;

    -- Kategorie mit CASE bestimmen
    category := CASE
        WHEN student_age < 18 THEN 'Minderjährig'
        WHEN student_age BETWEEN 18 AND 25 THEN 'Jung'
        ELSE 'Erwachsen'
    END;

    RETURN category;
END;
$$;

-- Aufruf der Function
SELECT categorize_student_with_case(1) AS age_category;
```

**MySQL**

| age_category |
| ------------ |
| Jung         |

## Die WHILE-Schleife

Mit `WHILE` kannst du Code wiederholt ausführen, solange eine Bedingung erfüllt ist.

### Syntax von WHILE

**MySQL**

```sql
WHILE bedingung DO
    anweisungen
END WHILE;
```

- `bedingung` — Ein logischer Ausdruck, der vor jedem Schleifendurchlauf geprüft wird. Solange er wahr
  ist, werden die Anweisungen wiederholt.
- `anweisungen` — Die SQL-Anweisungen im Schleifenkörper. Eine davon sollte die Bedingung irgendwann
  falsch machen, sonst endet die Schleife nie.

**PostgreSQL**

```sql
WHILE bedingung LOOP
    anweisungen
END LOOP;
```

- `bedingung` — Ein logischer Ausdruck, der vor jedem Schleifendurchlauf geprüft wird. Solange er wahr
  ist, werden die Anweisungen wiederholt.
- `anweisungen` — Die SQL-Anweisungen im Schleifenkörper. Eine davon sollte die Bedingung irgendwann
  falsch machen, sonst endet die Schleife nie.

### Beispiel für WHILE

Hier ein Beispiel für eine Stored Procedure, die mehrere Testfächer anlegt:

**MySQL**

```sql
CREATE PROCEDURE create_test_subjects(IN count_subjects INT)
BEGIN
    DECLARE i INT DEFAULT 1;
    DECLARE subject_id INT DEFAULT 20;

    WHILE i <= count_subjects DO
        INSERT INTO Subject (id, name)
        VALUES
        (
            subject_id + i,
            CONCAT('Test Subject ', i)
        );

        SET i = i + 1;
    END WHILE;
END;

-- 3 Testfächer anlegen
CALL create_test_subjects(3);

-- Ergebnis prüfen
SELECT * FROM Subject WHERE name LIKE 'Test Subject%';
```

**PostgreSQL**

```sql
CREATE OR REPLACE PROCEDURE create_test_subjects(count_subjects INT)
LANGUAGE plpgsql
AS $$
DECLARE
    i INT := 1;
    subject_id INT := 20;
BEGIN
    WHILE i <= count_subjects LOOP
        INSERT INTO Subject (id, name)
        VALUES
        (
            subject_id + i,
            'Test Subject ' || i
        );

        i := i + 1;
    END LOOP;

    RAISE NOTICE '% Testfächer wurden erstellt', count_subjects;
END;
$$;

-- 3 Testfächer anlegen
CALL create_test_subjects(3);

-- Ergebnis prüfen
SELECT * FROM Subject WHERE name LIKE 'Test Subject%';
```

| id  | name           |
| --- | -------------- |
| 21  | Test Subject 1 |
| 22  | Test Subject 2 |
| 23  | Test Subject 3 |

Mit Kontrollstrukturen werden Stored Procedures und Functions zu einem mächtigen Werkzeug, um komplexe Geschäftslogik direkt in der Datenbank umzusetzen! 🚀
