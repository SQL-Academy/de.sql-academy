---
meta:
    title: "Stored Procedures in SQL"
    description: "Stored Procedures in SQL erstellen und einsetzen. Syntax, Parameter, Bedingungslogik, Schleifen und praktische Beispiele."
---

# Stored Procedures in SQL

Stored Procedures sind Programmblöcke, die eine bestimmte Abfolge von Aktionen in der Datenbank ausführen.

**MySQL**

Anders als Functions können Procedures Daten verändern, komplexe Business-Logik abbilden und müssen nicht zwingend einen Wert zurückgeben.

**PostgreSQL**

Anders als Functions können Procedures Daten verändern und komplexe Business-Logik abbilden, sie geben aber keine Werte zurück.

## Allgemeine Struktur einer Stored Procedure

**MySQL**

```sql
CREATE PROCEDURE procedure_name(parameter1 TYP, parameter2 TYP, ...)
BEGIN
    -- Logik der Procedure
END;
```

**PostgreSQL**

```sql
CREATE OR REPLACE PROCEDURE procedure_name(parameter1 TYP, parameter2 TYP, ...)
LANGUAGE plpgsql
AS $$
BEGIN
    -- Logik der Procedure
END;
$$;
```

`LANGUAGE plpgsql` — gibt an, dass die Procedure in **PL/pgSQL** geschrieben ist (der prozeduralen Sprache von PostgreSQL).

`AS $$ ... $$` — sogenanntes **Dollar-Quoting**, eine spezielle Art, den Procedure-Körper zu umschließen. Damit musst du Zeichen innerhalb der Procedure nicht escapen.

## Einfaches Beispiel

Bauen wir eine Procedure, die die Daten eines Studenten aktualisiert:

**MySQL**

```sql
-- Procedure anlegen
CREATE PROCEDURE update_student_info(
    IN student_id INT,
    IN new_first_name VARCHAR(50),
    IN new_last_name VARCHAR(50)
)
BEGIN
    UPDATE Student
    SET first_name = new_first_name,
        last_name = new_last_name
    WHERE id = student_id;
END;

-- Procedure aufrufen
CALL update_student_info(1, 'Alexander', 'Smirnov');

-- Ergebnis prüfen
SELECT * FROM Student WHERE id = 1;
```

**PostgreSQL**

```sql
-- Procedure anlegen
CREATE OR REPLACE PROCEDURE update_student_info(
    student_id INT,
    new_first_name VARCHAR(50),
    new_last_name VARCHAR(50)
)
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE Student
    SET first_name = new_first_name,
        last_name = new_last_name
    WHERE id = student_id;
END;
$$;

-- Procedure aufrufen
CALL update_student_info(1, 'Alexander', 'Smirnov');

-- Ergebnis prüfen
SELECT * FROM Student WHERE id = 1;
```

| id  | first_name | middle_name | last_name | birthday                 | address                    |
| --- | ---------- | ----------- | --------- | ------------------------ | -------------------------- |
| 1   | Alexander  | Fedorovich  | Smirnov   | 2000-10-01T00:00:00.000Z | ul. Pushkina, d. 36, kv. 5 |

Diese Procedure nimmt die ID des Studenten und die neuen Daten entgegen und aktualisiert den zugehörigen Datensatz in der Tabelle `Student`.

**MySQL**

## Parametertypen in Procedures

In MySQL unterstützen Procedures drei Arten von Parametern:

- **IN** — Eingabeparameter (Standard)
- **OUT** — Ausgabeparameter zum Zurückgeben von Werten
- **INOUT** — Parameter, die sowohl Eingabe als auch Ausgabe sein können

### Eingabeparameter (IN)

Eingabeparameter übergeben Daten an die Procedure. Das ist der häufigste Parametertyp:

```sql
CREATE PROCEDURE add_subject(
    IN subject_id INT,
    IN subject_name VARCHAR(100)
)
BEGIN
    INSERT INTO Subject (id, name)
    VALUES (subject_id, subject_name);
END;

-- Aufruf mit Eingabeparametern
CALL add_subject(15, 'Mathematics');
```

### Ausgabeparameter (OUT)

Mit Ausgabeparametern kann die Procedure Werte zurückgeben:

```sql
CREATE PROCEDURE get_student_info(
    IN student_id INT,
    OUT student_name VARCHAR(100),
    OUT student_age INT
)
BEGIN
    SELECT
        CONCAT(first_name, ' ', last_name),
        TIMESTAMPDIFF(YEAR, birthday, CURDATE())
    INTO student_name, student_age
    FROM Student
    WHERE id = student_id;
END;

-- Aufruf der Procedure mit Ausgabeparametern
CALL get_student_info(1, @name, @age);
SELECT @name AS student_name, @age AS student_age;
```

| student_name    | student_age |
| --------------- | ----------- |
| Nikolaj Sokolov | 24          |

### Ein- und Ausgabeparameter (INOUT)

INOUT-Parameter nehmen einen Wert entgegen und geben einen geänderten Wert zurück:

```sql
CREATE PROCEDURE calculate_discount(
    INOUT price DECIMAL(10,2),
    IN discount_percent INT
)
BEGIN
    SET price = price - (price * discount_percent / 100);
END;

-- Verwendung eines INOUT-Parameters
SET @original_price = 1000.00;
CALL calculate_discount(@original_price, 15);
SELECT @original_price AS discounted_price;
```

**MySQL**

| discounted_price |
| ---------------- |
| 850              |

### Beispiel für alle drei Parametertypen

![Beispiele für die Parameter einer Stored Procedure](https://sql-academy.org/static/guidePage/stored-procedures/params-description.jpg "Beispiele für die Parameter einer Stored Procedure")

### Wichtige Unterschiede zwischen den Parametertypen

| Parametertyp | Richtung      | Verwendung                             |
| ------------ | ------------- | -------------------------------------- |
| **IN**       | Eingehend     | Daten an die Procedure übergeben       |
| **OUT**      | Ausgehend     | Ergebnis aus der Procedure zurückgeben |
| **INOUT**    | Bidirektional | Übergebenen Wert verändern             |

> **Wichtig:** OUT- und INOUT-Parameter in MySQL erfordern beim Aufruf Sitzungsvariablen (z. B. `@variable_name`).

## Stored Procedures verwalten

- **Vorhandene Procedures anzeigen**

    **MySQL**

    ```sql
    SHOW PROCEDURE STATUS WHERE Db = 'your_database_name';
    ```

    **PostgreSQL**

    ```sql
    SELECT routine_name, routine_type
    FROM information_schema.routines
    WHERE routine_type = 'PROCEDURE' AND routine_schema = 'public';
    ```

- **Procedure löschen**

    **MySQL**

    ```sql
    DROP PROCEDURE IF EXISTS add_student;
    ```

    **PostgreSQL**

    ```sql
    DROP PROCEDURE IF EXISTS add_student(VARCHAR, VARCHAR, DATE);
    ```

- **Procedure ändern**

    **MySQL**

    Zum Ändern einer Procedure in MySQL musst du die alte Version erst löschen und dann eine neue anlegen:

    ```sql
    DROP PROCEDURE IF EXISTS add_student;
    -- Neue Version der Procedure anlegen
    CREATE PROCEDURE add_student(...) ...
    ```

    **PostgreSQL**

    In PostgreSQL kannst du `CREATE OR REPLACE PROCEDURE` verwenden:

    ```sql
    CREATE OR REPLACE PROCEDURE add_student(
        student_first_name VARCHAR(50),
        student_last_name VARCHAR(50),
        student_birthday DATE
    )
    -- neue Implementierung
    ```

Stored Procedures sind ein mächtiges Werkzeug, um komplexe Business-Logik direkt in der Datenbank umzusetzen. Sie zentralisieren die Logik, steigern die Performance und sorgen für Datenintegrität! 🚀
