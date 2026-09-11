---
meta:
    title: "Datenbanken erstellen und löschen: MySQL und PostgreSQL"
    description: "SQL-Syntax zum Erstellen und Löschen von Datenbanken in MySQL und PostgreSQL: CREATE DATABASE, DROP DATABASE, IF EXISTS und IF NOT EXISTS."
---

# Datenbanken erstellen und löschen

Beim Schreiben von SQL-Queries arbeiten wir ständig mit Tabellen. Diese Tabellen liegen wiederum in konkreten Datenbanken – und um genau diese geht es in diesem Artikel.

## Datenbank erstellen

Eine Datenbank legst du mit folgender Syntax an:

```sql
CREATE DATABASE datenbank_name;
```

**MySQL**

Im Namen einer Datenbank verwendet man üblicherweise Buchstaben, Ziffern sowie die Zeichen „\_" und „$". Die maximale Länge beträgt 64 Zeichen.

Ob die Datenbank wirklich angelegt wurde, prüfst du mit `SHOW DATABASES`.

```sql
SHOW DATABASES;
```

| Database           |
| ------------------ |
| user_table_1       |
| user_table_2       |
| information_schema |
| mysql              |
| performance_schema |
| sys                |

> Beachte: `SHOW DATABASES` zeigt neben deinen eigenen Datenbanken auch die Service-Datenbanken: information_schema, mysql, performance_schema, sys.

**PostgreSQL**

Wird der Datenbankname ohne doppelte Anführungszeichen geschrieben, muss er mit einem Buchstaben oder dem Zeichen „\_" beginnen. Danach sind Buchstaben, Ziffern sowie die Zeichen „\_" und „$" erlaubt. Die maximale Länge beträgt 63 Zeichen.

Ob die Datenbank wirklich angelegt wurde, prüfst du mit dieser SQL-Query:

```sql
SELECT datname FROM pg_database WHERE datistemplate = false;
```

| datname         |
| --------------- |
| user_database_1 |
| user_database_2 |
| postgres        |

> Beachte: Neben deinen eigenen Datenbanken enthält PostgreSQL auch Service-Datenbanken: postgres, template0, template1.

## Datenbank löschen

Eine Datenbank löschst du mit `DROP DATABASE`:

```sql
DROP DATABASE datenbank_name;
```

**PostgreSQL**

> Du kannst keine Datenbank löschen, mit der deine aktuelle Sitzung verbunden ist. Auch wenn andere aktive Sitzungen mit der Datenbank verbunden sind, schlägt das Löschen fehl.

**MySQL**

## IF EXISTS und IF NOT EXISTS

Beim Erstellen oder Löschen einer Datenbank kann ein Fehler auftreten: zum Beispiel, wenn eine Datenbank mit diesem Namen schon existiert oder umgekehrt noch nicht angelegt wurde. Für solche Fälle gibt es die Konstrukte `IF EXISTS` und `IF NOT EXISTS`.

Wenn wir eine Datenbank also nur erstellen wollen, falls sie noch nicht existiert, sieht das so aus:

```sql
CREATE DATABASE IF NOT EXISTS datenbank_name;
```

Wenn wir eine Datenbank nur löschen wollen, falls sie existiert, sieht das so aus:

```sql
DROP DATABASE IF EXISTS datenbank_name;
```

**PostgreSQL**

## IF EXISTS

Beim Löschen einer Datenbank kann ein Fehler auftreten, falls diese gar nicht existiert. In diesem Fall hilft das `IF EXISTS`-Konstrukt.

Zum Löschen einer Datenbank kannst du `IF EXISTS` verwenden:

```sql
DROP DATABASE IF EXISTS datenbank_name;
```
