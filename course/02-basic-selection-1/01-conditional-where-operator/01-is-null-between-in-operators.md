---
meta:
    title: "Operatoren IS NULL, BETWEEN, IN"
    description: "Syntax und Anwendungsbeispiele für die SQL-Operatoren IS NULL, BETWEEN und IN"
---

# Operatoren IS NULL, BETWEEN, IN

Die Syntax von `WHERE` und die Vergleichsoperatoren kennst du bereits. In bedingten Queries kannst du zusätzlich folgende praktische Operatoren einsetzen:

- `IS NULL`
- `BETWEEN`
- `IN`

Schauen wir uns ihre Anwendung an.

## IS NULL

Mit `IS NULL` prüfst du, ob in einem Feld kein Wert vorhanden ist.

Als Beispiel listen wir alle Lehrkräfte ohne Vatersnamen auf:

```sql
SELECT * FROM Teacher
WHERE middle_name IS NULL;
```

| id  | first_name | middle_name | last_name |
| --- | ---------- | ----------- | --------- |
| 10  | YUrij      | null        | Krylov    |
| 11  | Andrej     | null        | Evseev    |

Für die Verneinung – also alle Datensätze, deren Feld nicht `NULL` ist – verwendest du:

```sql
SELECT * FROM Teacher
WHERE middle_name IS NOT NULL;
```

## BETWEEN

Der Operator `BETWEEN min AND max` prüft, ob der Spaltenwert im Intervall zwischen `min` und `max` liegt, einschließlich der Grenzwerte `min` und `max`.
Er ist identisch zu folgender Bedingung:

```sql
... WHERE field >= min AND field <= max
```

So wird er eingesetzt:

```sql
SELECT * FROM Payments
WHERE unit_price BETWEEN 100 AND 500;
```

Als Ergebnis kommen alle Datensätze aus `Payments` zurück, deren Wert von `unit_price` zwischen 100 und 500 liegt.

## IN

Mit `IN` prüfst du, ob der Spaltenwert in einer Liste vorgegebener Werte enthalten ist.

```sql
SELECT * FROM FamilyMembers
WHERE status IN ('father', 'mother');
```

| member_id | status | member_name       | birthday                 |
| --------- | ------ | ----------------- | ------------------------ |
| 1         | father | Headley Quincey   | 1960-05-13T00:00:00.000Z |
| 2         | mother | Flavia Quincey    | 1963-02-16T00:00:00.000Z |
| 6         | father | Ernest Forrest    | 1961-09-11T00:00:00.000Z |
| 7         | mother | Constance Forrest | 1968-09-06T00:00:00.000Z |
