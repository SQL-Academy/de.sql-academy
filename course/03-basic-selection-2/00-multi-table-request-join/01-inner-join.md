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
[INNER | {LEFT | RIGHT | FULL} [OUTER]] JOIN tabelle_2
    ON verbund_bedingung
[...]
```

- `tabellen_felder` — Die Spalten aus beliebigen verbundenen Tabellen, die ins Ergebnis kommen.
- `tabelle_1` — Die erste Tabelle, mit der die übrigen verbunden werden.
- `INNER` (Schlüsselwort, optional) — Innerer Verbund: Ins Ergebnis kommen nur Zeilen, für die ein Gegenstück gefunden
  wurde. Das ist der Standard, deshalb kannst du `INNER` weglassen.
- `LEFT | RIGHT | FULL` (Schlüsselwort, optional) — Äußerer Verbund: links, rechts oder vollständig. Neben den Paaren kommen auch die
  Zeilen ohne Gegenstück aus der linken, der rechten oder beiden Tabellen ins Ergebnis.
- `OUTER` (Schlüsselwort, optional) — Betont nur, dass der Verbund ein äußerer ist. Steht nach `LEFT`, `RIGHT` oder `FULL`
  und ändert am Ergebnis nichts.
- `tabelle_2` — Die Tabelle, die angefügt wird. Um weitere Tabellen anzufügen, ergänze für jede ein
  eigenes `JOIN ... ON ...`.
- `verbund_bedingung` — Die Regel, nach der Zeilen der beiden Tabellen einander finden, zum Beispiel
  `Payments.family_member = FamilyMembers.member_id`.

Bei einer Multi-Table-Query mit innerem Verbund sieht die allgemeine Struktur so aus:

```sql
SELECT tabellen_felder
FROM tabelle_1
[INNER] JOIN tabelle_2
    ON verbund_bedingung
[...]
```

- `tabellen_felder` — Die Spalten aus beliebigen verbundenen Tabellen, die ins Ergebnis kommen.
- `tabelle_1` — Die erste Tabelle, mit der die übrigen verbunden werden.
- `INNER` (Schlüsselwort, optional) — Innerer Verbund: Ins Ergebnis kommen nur Zeilen, für die ein Gegenstück gefunden
  wurde. Das ist der Standard, deshalb kannst du `INNER` weglassen.
- `tabelle_2` — Die Tabelle, die angefügt wird. Um weitere Tabellen anzufügen, ergänze für jede ein
  eigenes `JOIN ... ON ...`.
- `verbund_bedingung` — Die Regel, nach der Zeilen der beiden Tabellen einander finden, zum Beispiel
  `Payments.family_member = FamilyMembers.member_id`.

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
