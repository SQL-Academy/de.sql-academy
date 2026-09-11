---
meta:
    title: "Subquery mit einer Zeile und einer Spalte"
    description: "Skalare Subqueries in SQL, Beispiele und Syntax"
---

# Subquery mit einer Zeile und einer Spalte

In dieser Lektion schauen wir uns die Subquery, die eine Zeile und eine Spalte zurückgibt, genauer an. Dieser Typ von Subquery ist auch als skalare Subquery bekannt.

Sie kann in verschiedenen Teilen der Hauptabfrage verwendet werden, am häufigsten kommt sie jedoch in Filterbedingungen mit Vergleichsoperatoren (`=`, `<>`, `>`, `<`) zum Einsatz.

## Beispiele

Die folgende einfache Abfrage zeigt die Ausgabe eines einzelnen Wertes (des Firmennamens). In dieser Form ergibt sie nicht viel Sinn, deine Abfragen können aber deutlich komplexer sein.

```sql
SELECT (SELECT name FROM company LIMIT 1) AS company_name;
```

| company_name |
| ------------ |
| Don_avia     |

Genauso lassen sich skalare Subqueries zum Filtern von Zeilen mit `WHERE` und Vergleichsoperatoren einsetzen.

```sql
SELECT * FROM FamilyMembers
    WHERE birthday = (SELECT MAX(birthday) FROM FamilyMembers);
```

| member_id | status   | member_name      | birthday                 |
| --------- | -------- | ---------------- | ------------------------ |
| 8         | daughter | Wednesday Addams | 2005-01-13T00:00:00.000Z |

Mit dieser Abfrage lässt sich das jüngste Familienmitglied ermitteln. Die Subquery wird hier benötigt, um das maximale Geburtsdatum zu bestimmen, das dann in der Hauptabfrage zum Filtern der Zeilen verwendet wird.

## Wie wichtig die Übereinstimmung von Operator und Subquery-Ergebnis ist

Wenn du das Ergebnis einer Subquery mit Vergleichsoperatoren wie in unserem Beispiel verwendest, ist es wichtig, dass die Subquery tatsächlich einen skalaren Wert zurückgibt (1 Zeile und 1 Spalte).

**MySQL**

Würde diese Subquery mehrere Werte zurückliefern, würde das DBMS einen Fehler werfen, der mitteilt, dass nur 1 Datensatz erwartet wurde: „ER_SUBQUERY_NO_1_ROW: Subquery returns more than 1 row“.

**PostgreSQL**

Würde diese Subquery mehrere Werte zurückliefern, würde das DBMS einen Fehler werfen, der mitteilt, dass nur 1 Datensatz erwartet wurde: „ERROR: more than one row returned by a subquery used as an expression“.

Deshalb solltest du beim Schreiben von Subqueries vorsichtig sein und dir vorstellen, welches Ergebnis die Subquery liefert und welche Operatoren wir zusammen mit dieser Ergebnismenge verwenden können.
