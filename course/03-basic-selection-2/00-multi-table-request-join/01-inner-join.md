---
meta:
    title: "Innerer Verbund INNER JOIN"
    description: "Beschreibung und Syntax des inneren Verbunds INNER JOIN"
---

# Innerer Verbund INNER JOIN

Im vorherigen Kapitel haben wir die allgemeine Struktur einer Multi-Table-Query angeschaut:

```sql
SELECT tabellen_felder
FROM tabelle_1
[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN tabelle_2
    ON verbund_bedingung
[[INNER] | [[LEFT | RIGHT | FULL][OUTER]] JOIN tabelle_n
    ON verbund_bedingung]
```

Bei einer Multi-Table-Query mit innerem Verbund sieht die allgemeine Struktur so aus:

```sql
SELECT tabellen_felder
FROM tabelle_1
[INNER] JOIN tabelle_2
    ON verbund_bedingung
[[INNER] JOIN tabelle_n
    ON verbund_bedingung]
```

Eine Query kann zum Beispiel folgendermassen aussehen:

ER-Diagramm der Datenbank Family: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/inner-join).

```sql
SELECT family_member, member_name FROM Payments
INNER JOIN FamilyMembers
    ON Payments.family_member = FamilyMembers.member_id
```

| family_member | member_name     |
| ------------- | --------------- |
| 1             | Headley Quincey |
| 2             | Flavia Quincey  |
| 3             | Andie Quincey   |
| 4             | Lela Quincey    |
| 4             | Lela Quincey    |
| 5             | Annie Quincey   |
| 2             | Flavia Quincey  |
| 2             | Flavia Quincey  |
| 5             | Annie Quincey   |
| 3             | Andie Quincey   |
| 2             | Flavia Quincey  |
| 1             | Headley Quincey |
| 3             | Andie Quincey   |
| 3             | Andie Quincey   |

Da `JOIN` standardmäßig als `INNER JOIN` ausgeführt wird, wenn keine weiteren Angaben gemacht sind, ist `INNER` beim inneren Verbund optional.

## Was ein innerer Verbund ist

Ein innerer Verbund verknüpft Paare von Datensätzen aus zwei Tabellen, die die Verbundbedingung erfüllen, und bildet daraus eine neue Tabelle mit den
Spalten aus der ersten und der zweiten Ausgangstabelle.

Visuell sieht das so aus:

![Schema des inneren Verbunds](https://sql-academy.org/static/guidePage/inner-join/inner-join-example.png "Schema des inneren Verbunds")

Da unsere Bedingung die Gleichheit der Felder `Payments.good_id` und `Goods.good_id` verlangt, landen im Ergebnis nur die Datensätze,
bei denen in beiden Tabellen derselbe Wert für `good_id` vorkommt.

## WHERE zum Verbinden von Tabellen nutzen

Tabellen lassen sich auch über `WHERE` innerlich verbinden. Die obige Query, geschrieben mit `INNER JOIN`, sieht damit so aus:

```sql
SELECT family_member, member_name FROM Payments, FamilyMembers
    WHERE Payments.family_member = FamilyMembers.member_id
```

| family_member | member_name     |
| ------------- | --------------- |
| 1             | Headley Quincey |
| 2             | Flavia Quincey  |
| 3             | Andie Quincey   |
| 4             | Lela Quincey    |
| 4             | Lela Quincey    |
| 5             | Annie Quincey   |
| 2             | Flavia Quincey  |
| 2             | Flavia Quincey  |
| 5             | Annie Quincey   |
| 3             | Andie Quincey   |
| 2             | Flavia Quincey  |
| 1             | Headley Quincey |
| 3             | Andie Quincey   |
| 3             | Andie Quincey   |
