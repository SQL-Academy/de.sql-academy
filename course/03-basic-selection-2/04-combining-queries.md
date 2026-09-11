---
meta:
    title: "Queries kombinieren, der UNION-Operator"
    description: "Beispiel zur Verwendung des SQL-Operators UNION zum Kombinieren von Ergebnismengen"
---

# Queries kombinieren, der UNION-Operator

Die Ergebnisse von SQL-Queries lassen sich kombinieren. Dafür gibt es den `UNION`-Operator.

## Allgemeine Struktur einer Query mit UNION

```sql
SELECT tabellen_felder FROM tabellen_liste ...
UNION [ALL]
SELECT tabellen_felder FROM tabellen_liste ... ;
```

`UNION` entfernt standardmäßig Duplikate aus der Ergebnistabelle. Wenn du Duplikate behalten willst, gibt es den optionalen Parameter `ALL`.

- Verwechsle das Kombinieren von Queries nicht mit dem Verknüpfen von Tabellen – dafür ist `JOIN` da.
- Verwechsle das Kombinieren von Queries auch nicht mit Subqueries. Subqueries laufen auf verknüpften Tabellen.

`UNION` kombiniert Tabellen, die nicht miteinander verknüpft sind, aber eine ähnliche Struktur haben.

Damit `UNION` korrekt funktioniert, müssen die Ergebnistabellen aller einzelnen SQL-Queries die gleiche Anzahl an Spalten haben –
mit kompatiblen Datentypen und in derselben Reihenfolge.

> Kompatible Datentypen sind Typen, für die das DBMS einen gemeinsamen Ergebnistyp auswählen kann.
> Zum Beispiel können `INT` und `BIGINT` in einen gemeinsamen numerischen Typ umgewandelt werden. Die Typen müssen nicht exakt übereinstimmen,
> aber wenn es keinen gemeinsamen Typ gibt, endet die Query mit einem Fehler.

Es gibt zwei weitere Operatoren mit sehr ähnlichem Verhalten wie `UNION`:

- `INTERSECT`
  Kombiniert zwei `SELECT`-Queries, gibt aber nur die Datensätze des ersten `SELECT` zurück, die auch im zweiten `SELECT` vorkommen.
- `EXCEPT`
  Kombiniert zwei `SELECT`-Queries, gibt aber nur die Datensätze des ersten `SELECT` zurück, die im zweiten `SELECT` nicht vorkommen.

## Anwendungsbeispiele

Angenommen, wir wollen die Namen aller Waren und die Namen aller Familienmitglieder gemeinsam ausgeben (ein ziemlich konstruiertes Beispiel). Da die Datentypen übereinstimmen, geht das problemlos.

```sql
SELECT DISTINCT Goods.good_name AS name FROM Goods
UNION
SELECT DISTINCT FamilyMembers.member_name AS name FROM FamilyMembers;
```

| name               |
| ------------------ |
| apartment fee      |
| phone fee          |
| bread              |
| milk               |
| red caviar         |
| cinema             |
| black caviar       |
| cough tablets      |
| potato             |
| pineapples         |
| television         |
| vacuum cleaner     |
| jacket             |
| fur coat           |
| music school fee   |
| english school fee |
| Headley Quincey    |
| Flavia Quincey     |
| Andie Quincey      |
| Lela Quincey       |
| Annie Quincey      |
| Ernest Forrest     |
| Constance Forrest  |
| Wednesday Addams   |
