---
meta:
    title: "Tabellen erstellen und löschen: MySQL und PostgreSQL"
    description: "SQL: Tabellen in MySQL und PostgreSQL erstellen und löschen. Operatoren zur Beschreibung von Tabellen."
---

# Tabellen erstellen und löschen

## Tabelle erstellen

**MySQL**

Bevor du eine Tabelle anlegst, musst du die Datenbank wählen, in der die Tabelle gespeichert werden soll. Das geht mit `USE`:

```sql
USE datenbank_name;
```

Zum Anlegen einer Tabelle wird `CREATE TABLE` verwendet. Die Grundsyntax sieht so aus:

```sql
CREATE TABLE [IF NOT EXISTS] tabellen_name (
     spalte_1 datentyp,
    [spalte_2 datentyp,]
    ...
    [spalte_n datentyp,]
);
```

Legen wir zum Beispiel eine Benutzertabelle an.

**MySQL**

```sql
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255),
    age INTEGER
);
```

**PostgreSQL**

```sql
CREATE TABLE Users (
    id INTEGER,
    name VARCHAR(255),
    age INTEGER
);
```

**MySQL**

`INTEGER` und `VARCHAR(255)` sind Datentypen: ein numerischer und ein String-Typ. Mehr dazu in den folgenden Artikeln.

**PostgreSQL**

`INTEGER` und `VARCHAR(255)` sind Datentypen: ein numerischer und ein String-Typ. Mehr dazu in den folgenden Artikeln.

## Zusätzliche Parameter bei der Spaltendefinition

Die obige Spaltendefinition ist eine vereinfachte Form.
Neben Spaltenname und Datentyp können noch folgende optionale Parameter ergänzt werden:

- `PRIMARY KEY`

    Markiert eine Spalte oder eine Spaltengruppe als Primary Key.

**MySQL**

- `AUTO_INCREMENT`

    Gibt an, dass der Wert dieser Spalte beim Einfügen neuer Datensätze automatisch hochgezählt wird. Jede Tabelle kann höchstens eine `AUTO_INCREMENT`-Spalte haben.
    Hinweis: Dieser Parameter ist nur für Ganzzahl- und Gleitkommatypen erlaubt.

**PostgreSQL**

- `SERIAL` oder `GENERATED ALWAYS AS IDENTITY`

    Gibt an, dass der Wert dieser Spalte beim Einfügen neuer Datensätze automatisch hochgezählt wird. `SERIAL` ist die Kurzform, um ein Autoinkrement-Feld anzulegen.

* `UNIQUE`

    Stellt sicher, dass die Werte in dieser Spalte über alle Datensätze hinweg unterschiedlich sind.

* `NOT NULL`

    Stellt sicher, dass die Werte in dieser Spalte nicht `NULL` sein dürfen.

* `DEFAULT`

    Legt einen Standardwert fest.

Für unsere Users-Tabelle könnten wir folgende Parameter setzen:

**MySQL**

```sql
CREATE TABLE Users (
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18
);
```

**PostgreSQL**

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    age INTEGER NOT NULL DEFAULT 18
);
```

In diesem Beispiel ist:

**MySQL**

- `id` ein numerisches Feld, das den Primary Key mit Autoinkrement bildet;

**PostgreSQL**

- `id` ein Feld vom Typ SERIAL (autoinkrementierende Ganzzahl), das den Primary Key bildet;

* `name` ein String-Feld mit maximal 255 Zeichen und Pflichtangabe;
* `age` ein numerisches Feld mit Standardwert 18.

## CURRENT_TIMESTAMP als Standardwert

`CURRENT_TIMESTAMP` ist praktisch, wenn der Zeitpunkt der Datensatzerstellung automatisch festgehalten werden soll. Zum Beispiel zusammen mit dem Typ `TIMESTAMP`.

**MySQL**

```sql
CREATE TABLE Users (
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**PostgreSQL**

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Tabellenbeschreibung

**MySQL**

Um die Struktur einer angelegten Tabelle anzuschauen, gibt es den `DESCRIBE`-Operator.

```sql
DESCRIBE Users;
```

| Field | Type         | Null | Key | Default | Extra          |
| ----- | ------------ | ---- | --- | ------- | -------------- |
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(255) | NO   |     | NULL    |                |
| age   | int          | NO   |     | 18      |                |

**PostgreSQL**

Um die Struktur einer angelegten Tabelle anzuschauen, kannst du eine SQL-Query gegen das Informationsschema absetzen:

```sql
SELECT column_name, data_type, is_nullable, column_default
FROM information_schema.columns
WHERE table_schema = current_schema() AND table_name = 'users';
```

| column_name | data_type         | is_nullable | column_default                    |
| ----------- | ----------------- | ----------- | --------------------------------- |
| id          | integer           | NO          | nextval('users_id_seq'::regclass) |
| name        | character varying | NO          | null                              |
| age         | integer           | NO          | 18                                |

## Weitere Parameter bei der Tabellendefinition

Neben der Spaltendefinition kannst du beim Anlegen einer Tabelle noch folgende Parameter angeben:

**MySQL**

- Primary Key.

    Wenn du den Primary Key nicht direkt bei den Spalteneigenschaften definierst, kannst du das auch in den Tabellen-Parametern tun, indem du nach der Spaltenliste `PRIMARY KEY (<spalte_1>, <spalte_n>)` ergänzt:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        PRIMARY KEY (id)
    );
    ```

**PostgreSQL**

- Primary Key.

    Wenn du den Primary Key nicht direkt bei den Spalteneigenschaften definierst, kannst du das auch in den Tabellen-Parametern tun, indem du nach der Spaltenliste `PRIMARY KEY (<spalte_1>, <spalte_n>)` ergänzt:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        PRIMARY KEY (id)
    );
    ```

**MySQL**

- Foreign Keys.

    Angenommen, wir wollen speichern, in welcher Firma unsere Nutzer arbeiten. Legen wir eine kleine Tabelle `Companies` an, in der wir die eindeutige ID und den Namen der Firma speichern:

    ```sql
    CREATE TABLE Companies (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        PRIMARY KEY (id)
    );
    ```

    Als Nächstes ergänzen wir in der `Users`-Tabelle das Feld `company` – den Arbeitsplatz unseres Nutzers, der auf einen Datensatz in `Companies` verweist. Die vollständige Query zum Anlegen der Tabelle sieht so aus:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id)
    );
    ```

    Damit beim Einfügen neuer Datensätze in `Users` garantiert ist, dass die Spalte `company` eine ID enthält, die es in `Companies` wirklich gibt,
    wird ein Foreign Key verwendet. Seine Syntax:

    ```sql
    FOREIGN KEY (<spalte_1>, <spalte_n>)
    REFERENCES <externe_tabelle> (<spalte_in_externer_tabelle_1>, <spalte_in_externer_tabelle_n>)
    [ON DELETE aktion]
    [ON UPDATE aktion]
    ```

    Die vollständige Query mit Foreign Key sieht so aus:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id),
        FOREIGN KEY (company) REFERENCES Companies (id)
    );
    ```

    Mit Foreign Keys kann man festlegen, wie sich der aktuelle Datensatz verhält, wenn der referenzierte Datensatz geändert oder gelöscht wird.

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id),
        FOREIGN KEY (company) REFERENCES Companies (id)
        ON DELETE RESTRICT ON UPDATE CASCADE
    );
    ```

    `ON DELETE RESTRICT` bedeutet: Versucht jemand, eine Firma zu löschen, zu der es Einträge in `Users` gibt, lässt die Datenbank das nicht zu:

    ```sql
    Cannot delete or update a parent row: a foreign key constraint fails
    ```

    Wäre `ON DELETE CASCADE` angegeben, würden beim Löschen einer Firma auch alle Nutzer gelöscht, die auf diese Firma verweisen.

    Es gibt noch eine Option: `ON DELETE SET NULL`. Damit setzt die Datenbank für alle Nutzer der gelöschten Firma das Feld `company` auf `NULL`.

    `ON UPDATE CASCADE` bedeutet: Ändert eine Firma ihre ID, bekommen alle Nutzer (`Users`) automatisch die neue ID im Feld company.

**PostgreSQL**

- Foreign Keys.

    Angenommen, wir wollen speichern, in welcher Firma unsere Nutzer arbeiten. Legen wir eine kleine Tabelle `Companies` an, in der wir die eindeutige ID und den Namen der Firma speichern:

    ```sql
    CREATE TABLE Companies (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        PRIMARY KEY (id)
    );
    ```

    Als Nächstes ergänzen wir in der `Users`-Tabelle das Feld `company` – den Arbeitsplatz unseres Nutzers, der auf einen Datensatz in `Companies` verweist. Die vollständige Query zum Anlegen der Tabelle sieht so aus:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id)
    );
    ```

    Damit beim Einfügen neuer Datensätze in `Users` garantiert ist, dass die Spalte `company` eine ID enthält, die es in `Companies` wirklich gibt,
    wird ein Foreign Key verwendet. Seine Syntax:

    ```sql
    FOREIGN KEY (<spalte_1>, <spalte_n>)
    REFERENCES <externe_tabelle> (<spalte_in_externer_tabelle_1>, <spalte_in_externer_tabelle_n>)
    [ON DELETE aktion]
    [ON UPDATE aktion]
    ```

    Die vollständige Query mit Foreign Key sieht so aus:

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id),
        FOREIGN KEY (company) REFERENCES Companies (id)
    );
    ```

    Mit Foreign Keys kann man festlegen, wie sich der aktuelle Datensatz verhält, wenn der referenzierte Datensatz geändert oder gelöscht wird.

    ```sql
    CREATE TABLE Users (
        id INTEGER,
        name VARCHAR(255) NOT NULL,
        age INTEGER NOT NULL DEFAULT 18,
        company INTEGER,
        PRIMARY KEY (id),
        FOREIGN KEY (company) REFERENCES Companies (id)
        ON DELETE RESTRICT ON UPDATE CASCADE
    );
    ```

    `ON DELETE RESTRICT` bedeutet: Versucht jemand, eine Firma zu löschen, zu der es Einträge in `Users` gibt, lässt die Datenbank das nicht zu:

    ```sql
    ERROR:  update or delete on table "companies" violates foreign key constraint "users_company_fkey" on table "users"
    DETAIL:  Key (id)=(1) is still referenced from table "users".
    ```

    Wäre `ON DELETE CASCADE` angegeben, würden beim Löschen einer Firma auch alle Nutzer gelöscht, die auf diese Firma verweisen.

    Es gibt noch eine Option: `ON DELETE SET NULL`. Damit setzt die Datenbank für alle Nutzer der gelöschten Firma das Feld `company` auf `NULL`.

    `ON UPDATE CASCADE` bedeutet: Ändert eine Firma ihre ID, bekommen alle Nutzer (`Users`) automatisch die neue ID im Feld company.

## Tabelle löschen

Eine Tabelle löschst du mit `DROP TABLE`.

```sql
DROP TABLE [IF EXISTS] tabellen_name;
```

## Interaktive Aufgabe

Jetzt, wo du die Grundlagen zum Anlegen von Tabellen kennst, kannst du das Gelernte in der interaktiven Aufgabe festigen:

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/create-table).
