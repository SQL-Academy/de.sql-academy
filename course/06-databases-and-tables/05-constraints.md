---
meta:
    title: "Spalten-Constraints in SQL: MySQL und PostgreSQL"
    description: "Vollständiger Leitfaden zu Constraints in MySQL und PostgreSQL, der erklärt, wie sie die Korrektheit und Integrität der Daten in Datenbanktabellen sicherstellen. Erfahre alles über die verschiedenen Constraint-Arten: PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK und DEFAULT."
---

# Spalten-Constraints in SQL

Constraints sind Regeln, die auf die Daten einer Tabelle angewendet werden, um deren Genauigkeit und Verlässlichkeit sicherzustellen. Sie spielen eine wichtige Rolle bei der Datenintegrität und beim Einhalten von Business-Regeln. 🔒

Wenn du eine Tabelle anlegst oder ihre Struktur änderst, kannst du verschiedene Constraints definieren, die das Einfügen, Ändern oder Löschen von Daten verhindern, die gegen die festgelegten Regeln verstoßen.
So vermeidest du unerwünschte Situationen wie:

- Mehrere Nutzer mit derselben ID
- Verweise auf nicht existierende Datensätze in anderen Tabellen
- Fehlende Pflichtdaten
- Ungültige Werte (z. B. ein negatives Alter oder ein Geburtsdatum in der Zukunft)

## Die wichtigsten Constraint-Arten in SQL ✨

In SQL gibt es folgende Hauptarten von Constraints:

1. **PRIMARY KEY** — eindeutiger Identifikator eines Datensatzes in der Tabelle
2. **FOREIGN KEY** — sichert die referenzielle Integrität zwischen Tabellen
3. **UNIQUE** — garantiert die Einzigartigkeit der Werte in einer Spalte oder Spaltengruppe
4. **NOT NULL** — verbietet NULL-Werte in einer Spalte
5. **CHECK** — prüft, ob Daten einer bestimmten Bedingung entsprechen
6. **DEFAULT** — setzt einen Standardwert für eine Spalte

Sehen wir uns jeden Typ genauer an.

## PRIMARY KEY (Primärschlüssel)

Ein Primary Key ist eine Spalte oder eine Kombination von Spalten, die jede Zeile einer Tabelle eindeutig identifiziert.

Er darf keine NULL-Werte enthalten und muss eindeutig sein. Eine Tabelle kann nur einen Primary Key haben.

**MySQL**

```sql
CREATE TABLE Users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100)
);
```

Alternative Syntax mit separatem Constraint:

```sql
CREATE TABLE Users (
    id INT AUTO_INCREMENT,
    username VARCHAR(50),
    email VARCHAR(100),
    CONSTRAINT pk_users PRIMARY KEY (id)
);
```

Wenn du versuchst, einen Datensatz mit einem bereits vorhandenen Primärschlüssel oder mit `NULL` als Schlüssel einzufügen, gibt das DBMS einen Fehler aus:

```sql
Error(1062) 23000: "Duplicate entry '1' for key 'users.PRIMARY'"
```

**PostgreSQL**

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100)
);
```

Alternative Syntax mit separatem Constraint:

```sql
CREATE TABLE Users (
    id SERIAL,
    username VARCHAR(50),
    email VARCHAR(100),
    CONSTRAINT pk_users PRIMARY KEY (id)
);
```

Wenn du versuchst, einen Datensatz mit einem bereits vorhandenen Primärschlüssel oder mit `NULL` als Schlüssel einzufügen, gibt das DBMS einen Fehler aus:

```sql
ERROR: duplicate key value violates unique constraint "users_pkey"
DETAIL: Key (id)=(1) already exists.
```

## FOREIGN KEY (Fremdschlüssel)

Ein Foreign Key ist eine Spalte oder Gruppe von Spalten in einer Tabelle, die auf den Primary Key einer anderen Tabelle verweist.

Er sichert die referenzielle Integrität der Daten und garantiert, dass die Werte in der Foreign-Key-Spalte mit Werten im Primary Key der verknüpften Tabelle übereinstimmen.

```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(id)
);
```

Dank des FOREIGN KEY:

- Kann keine Bestellung für einen nicht existierenden Nutzer angelegt werden
- Kann ein Nutzer mit vorhandenen Bestellungen nicht gelöscht werden (es sei denn, es sind spezielle CASCADE-Optionen gesetzt)

Du kannst auch festlegen, was beim Löschen oder Aktualisieren der verknüpften Daten passieren soll:

```sql
CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

Mögliche Optionen für `ON DELETE` und `ON UPDATE`:

- `CASCADE` — löscht oder aktualisiert verknüpfte Datensätze automatisch
- `SET NULL` — setzt den Foreign Key auf NULL
- `SET DEFAULT` — setzt den Standardwert
- `RESTRICT` — verbietet das Löschen oder Ändern (Standardverhalten)
- `NO ACTION` — entspricht in den meisten DBMS RESTRICT

## UNIQUE (Einzigartigkeit)

Der UNIQUE-Constraint garantiert, dass alle Werte in einer Spalte oder Spaltengruppe einzigartig sind. Anders als der PRIMARY KEY erlaubt er NULL-Werte (normalerweise nur einen NULL-Wert, da NULL != NULL).

```sql
CREATE TABLE Users (
    id INT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE
);
```

So verhinderst du, dass mehrere Nutzer denselben Nutzernamen oder dieselbe E-Mail-Adresse haben.

## NOT NULL (Pflichtwert)

Der NOT NULL-Constraint garantiert, dass eine Spalte keine NULL-Werte enthalten kann. Praktisch für Pflichtfelder, ohne die ein Datensatz keinen Sinn ergibt.

```sql
CREATE TABLE Users (
    id INT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    bio TEXT
);
```

In diesem Beispiel sind die Felder `username` und `email` Pflicht, `bio` darf leer bleiben.

## CHECK (Bedingungsprüfung)

**MySQL**

Der CHECK-Constraint legt eine Bedingung fest, die die Werte einer Spalte erfüllen müssen. Damit setzt du Business-Regeln durch und verhinderst die Eingabe ungültiger Daten.

**Hinweis:** CHECK-Constraints werden in MySQL erst ab Version 8.0.16 vollständig unterstützt. In älteren Versionen wurden sie syntaktisch akzeptiert, aber nicht geprüft.

```sql
CREATE TABLE Products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) CHECK (price > 0),
    quantity INT CHECK (quantity >= 0)
);
```

Ein komplexeres Beispiel mit benanntem Constraint:

```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    birth_date DATE NOT NULL,
    hire_date DATE NOT NULL,
    CONSTRAINT chk_dates CHECK (hire_date > birth_date)
);
```

**PostgreSQL**

Der CHECK-Constraint legt eine Bedingung fest, die die Werte einer Spalte erfüllen müssen. Damit setzt du Business-Regeln durch und verhinderst die Eingabe ungültiger Daten.

PostgreSQL unterstützt CHECK-Constraints vollständig und prüft sie aktiv.

```sql
CREATE TABLE Products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2) CHECK (price > 0),
    quantity INT CHECK (quantity >= 0)
);
```

Ein komplexeres Beispiel mit benanntem Constraint:

```sql
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    birth_date DATE NOT NULL,
    hire_date DATE NOT NULL,
    CONSTRAINT chk_dates CHECK (hire_date > birth_date)
);
```

PostgreSQL unterstützt auch komplexe CHECK-Constraints mit Subqueries und Funktionen:

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(100) CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
    age INT CHECK (age >= 0 AND age <= 150)
);
```

## DEFAULT (Standardwert)

Der DEFAULT-Constraint legt einen Wert fest, der verwendet wird, wenn beim Einfügen eines neuen Datensatzes für diese Spalte kein Wert angegeben wird.

**MySQL**

```sql
CREATE TABLE Orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    order_date DATE DEFAULT (CURRENT_DATE),
    status VARCHAR(20) DEFAULT 'Pending',
    FOREIGN KEY (user_id) REFERENCES Users(id)
);
```

In diesem Beispiel wird ohne Angabe des Bestelldatums das aktuelle Datum verwendet, und der Standardstatus ist „Pending".

**PostgreSQL**

```sql
CREATE TABLE Orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT,
    order_date DATE DEFAULT CURRENT_DATE,
    status VARCHAR(20) DEFAULT 'Pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(id)
);
```

In diesem Beispiel wird ohne Angabe des Bestelldatums das aktuelle Datum verwendet, und der Standardstatus ist „Pending".

PostgreSQL unterstützt auch komplexere DEFAULT-Werte, einschließlich Funktionsaufrufen:

```sql
CREATE TABLE Users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    created_at TIMESTAMP DEFAULT NOW(),
    uuid_field UUID DEFAULT gen_random_uuid()
);
```

## Constraints hinzufügen und entfernen

**MySQL**

Constraints kannst du nicht nur beim Anlegen einer Tabelle definieren, sondern auch nachträglich ändern:

```sql
-- PRIMARY KEY hinzufügen
ALTER TABLE Users
ADD PRIMARY KEY (id);

-- FOREIGN KEY hinzufügen
ALTER TABLE Orders
ADD CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES Users(id);

-- UNIQUE-Constraint hinzufügen
ALTER TABLE Users
ADD CONSTRAINT uq_email UNIQUE (email);

-- CHECK-Constraint hinzufügen
ALTER TABLE Products
ADD CONSTRAINT chk_price CHECK (price > 0);

-- NOT NULL-Constraint hinzufügen
ALTER TABLE Users
MODIFY username VARCHAR(50) NOT NULL;

-- Standardwert setzen
ALTER TABLE Orders
ALTER COLUMN status SET DEFAULT 'Pending';
```

Auch das Entfernen von Constraints geht mit ALTER TABLE:

```sql
-- PRIMARY KEY entfernen
ALTER TABLE Users
DROP PRIMARY KEY;

-- FOREIGN KEY entfernen
ALTER TABLE Orders
DROP FOREIGN KEY fk_user;

-- UNIQUE-Constraint entfernen
ALTER TABLE Users
DROP INDEX uq_email;

-- CHECK-Constraint entfernen
ALTER TABLE Products
DROP CHECK chk_price;

-- NOT NULL-Constraint entfernen
ALTER TABLE Users
MODIFY username VARCHAR(50) NULL;

-- Standardwert entfernen
ALTER TABLE Orders
ALTER COLUMN status DROP DEFAULT;
```

**PostgreSQL**

Constraints kannst du nicht nur beim Anlegen einer Tabelle definieren, sondern auch nachträglich ändern:

```sql
-- PRIMARY KEY hinzufügen
ALTER TABLE Users
ADD PRIMARY KEY (id);

-- FOREIGN KEY hinzufügen
ALTER TABLE Orders
ADD CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES Users(id);

-- UNIQUE-Constraint hinzufügen
ALTER TABLE Users
ADD CONSTRAINT uq_email UNIQUE (email);

-- CHECK-Constraint hinzufügen
ALTER TABLE Products
ADD CONSTRAINT chk_price CHECK (price > 0);

-- NOT NULL-Constraint hinzufügen
ALTER TABLE Users
ALTER COLUMN username SET NOT NULL;

-- Standardwert setzen
ALTER TABLE Orders
ALTER COLUMN status SET DEFAULT 'Pending';
```

Auch das Entfernen von Constraints geht mit ALTER TABLE:

```sql
-- PRIMARY KEY entfernen
ALTER TABLE Users
DROP CONSTRAINT users_pkey;

-- FOREIGN KEY entfernen
ALTER TABLE Orders
DROP CONSTRAINT fk_user;

-- UNIQUE-Constraint entfernen
ALTER TABLE Users
DROP CONSTRAINT uq_email;

-- CHECK-Constraint entfernen
ALTER TABLE Products
DROP CONSTRAINT chk_price;

-- NOT NULL-Constraint entfernen
ALTER TABLE Users
ALTER COLUMN username DROP NOT NULL;

-- Standardwert entfernen
ALTER TABLE Orders
ALTER COLUMN status DROP DEFAULT;
```

## Best Practices beim Einsatz von Constraints 🚀

Beim Datenbankdesign solltest du folgende Empfehlungen beherzigen:

1. **Definiere immer einen Primary Key** für jede Tabelle, damit sich jeder Datensatz eindeutig identifizieren lässt.

2. **Nutze Foreign Keys**, um die referenzielle Integrität zwischen verknüpften Tabellen sicherzustellen.

3. **Setze NOT NULL** für Spalten, die zwingend einen Wert haben müssen.

4. **Verwende UNIQUE** für Spalten, die einzigartige Werte enthalten sollen (z. B. E-Mail oder Telefonnummer).

5. **Füge CHECK-Constraints** für Spalten hinzu, deren Werte bestimmten Business-Regeln entsprechen müssen.

6. **Lege Standardwerte fest** für Spalten, die häufig denselben Wert enthalten.

7. **Vergib Namen für Constraints** (z. B. `CONSTRAINT pk_users PRIMARY KEY (id)`), damit sie leichter zu identifizieren und zu verwalten sind.

## Wissens-Check zu SQL-Constraints:

Welcher der folgenden SQL-Constraints darf KEINE NULL-Werte enthalten?

1. UNIQUE — Der UNIQUE-Constraint erlaubt NULL-Werte, normalerweise allerdings nur einen einzigen NULL-Wert pro Spalte.

2. CHECK — Der CHECK-Constraint prüft die Bedingung nur für nicht-leere Werte, NULL-Werte werden in der Regel von der Prüfung ausgenommen.

3. **Richtige Antwort:** PRIMARY KEY — Ein Primary Key darf keine NULL-Werte enthalten, da er jede Zeile der Tabelle eindeutig identifizieren muss.

4. FOREIGN KEY — Ein Foreign Key darf NULL-Werte enthalten, sofern nicht anders festgelegt – das bedeutet schlicht, dass keine Verknüpfung zu einer anderen Tabelle besteht.
