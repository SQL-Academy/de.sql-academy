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

- `datenbank_name` — Der Name der neuen Datenbank.

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

- `datenbank_name` — Die Datenbank, die samt allen Tabellen gelöscht wird.

**PostgreSQL**

> Du kannst keine Datenbank löschen, mit der deine aktuelle Sitzung verbunden ist. Auch wenn andere aktive Sitzungen mit der Datenbank verbunden sind, schlägt das Löschen fehl.

**MySQL**

## IF EXISTS und IF NOT EXISTS

Beim Erstellen oder Löschen einer Datenbank kann ein Fehler auftreten: zum Beispiel, wenn eine Datenbank mit diesem Namen schon existiert oder umgekehrt noch nicht angelegt wurde. Für solche Fälle gibt es die Konstrukte `IF EXISTS` und `IF NOT EXISTS`.

Wenn wir eine Datenbank also nur erstellen wollen, falls sie noch nicht existiert, sieht das so aus:

```sql
CREATE DATABASE IF NOT EXISTS datenbank_name;
```

- `IF NOT EXISTS` (Schlüsselwort) — Die Datenbank wird nur angelegt, wenn es noch keine mit diesem Namen gibt. Sonst macht
  die Query nichts und bricht nicht mit einem Fehler ab.
- `datenbank_name` — Der Name der neuen Datenbank.

Wenn wir eine Datenbank nur löschen wollen, falls sie existiert, sieht das so aus:

```sql
DROP DATABASE IF EXISTS datenbank_name;
```

- `IF EXISTS` (Schlüsselwort) — Die Datenbank wird nur gelöscht, wenn sie existiert. Sonst macht die Query nichts und
  bricht nicht mit einem Fehler ab.
- `datenbank_name` — Die Datenbank, die gelöscht wird.

**PostgreSQL**

## IF EXISTS

Beim Löschen einer Datenbank kann ein Fehler auftreten, falls diese gar nicht existiert. In diesem Fall hilft das `IF EXISTS`-Konstrukt.

Zum Löschen einer Datenbank kannst du `IF EXISTS` verwenden:

```sql
DROP DATABASE IF EXISTS datenbank_name;
```

- `IF EXISTS` (Schlüsselwort) — Die Datenbank wird nur gelöscht, wenn sie existiert. Sonst macht die Query nichts und
  bricht nicht mit einem Fehler ab.
- `datenbank_name` — Die Datenbank, die gelöscht wird.

## Prüf dich selbst

In der Datenbank `shop` gibt es Tabellen mit Daten. Was passiert, wenn du diese Query ausführst?

```sql
DROP DATABASE shop;
```

1. Die Query bricht mit einem Fehler ab: Zuerst müssen die Tabellen gelöscht werden — Du musst die Datenbank vorher nicht leeren: DROP DATABASE löscht sie zusammen mit allen ihren Tabellen.

2. **Richtige Antwort:** Die Datenbank wird mit allen Tabellen und Daten gelöscht — DROP DATABASE löscht die gesamte Datenbank. Danach lassen sich Tabellen und Daten nur noch aus einem Backup wiederherstellen.

3. Die Tabellen werden gelöscht, die leere Datenbank bleibt bestehen — DROP DATABASE löscht nicht nur den Inhalt, sondern auch die Datenbank selbst: Nach der Query taucht sie nicht mehr in der Liste der Datenbanken auf.

Auf dem Server gibt es keine Datenbank `shop`. Was passiert, wenn du diese Query ausführst?

```sql
DROP DATABASE IF EXISTS shop;
```

1. Die Query bricht mit einem Fehler ab, weil es die Datenbank nicht gibt — So verhält sich DROP DATABASE ohne IF EXISTS. Genau um diesen Fehler zu vermeiden, gibt es IF EXISTS.

2. **Richtige Antwort:** Die Query macht nichts und bricht nicht mit einem Fehler ab — IF EXISTS löscht die Datenbank nur, wenn sie existiert, und überspringt das Löschen sonst ohne Fehler.

3. Es wird eine leere Datenbank shop angelegt — DROP DATABASE legt nie Datenbanken an, und IF EXISTS prüft nur, ob die Datenbank vor dem Löschen existiert.
