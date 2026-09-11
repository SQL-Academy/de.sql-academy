---
meta:
    title: "Korrelierte Subqueries"
    description: "Korrelierte und nicht-korrelierte Subqueries in SQL und ihre Syntax"
---

# Korrelierte Subqueries

Alle bisher besprochenen Subqueries waren nicht-korreliert (unabhängig).
Sie konnten unabhängig von der äußeren Query laufen, und wir konnten uns ansehen, was sie zurückgeben, bevor ihr Ergebnis in der äußeren Query verwendet wurde.
Korrelierte Subqueries dagegen beziehen sich auf eine oder mehrere Spalten der äußeren Query.

## Beispiel für eine korrelierte Subquery

Die folgende korrelierte Subquery berechnet zum Beispiel, wer wie viel ausgegeben hat:

```sql
SELECT FamilyMembers.member_name, (
    SELECT SUM(Payments.unit_price * Payments.amount)
    FROM Payments
    WHERE Payments.family_member = FamilyMembers.member_id
) AS total_spent
FROM FamilyMembers;
```

| member_name       | total_spent |
| ----------------- | ----------- |
| Headley Quincey   | 2504        |
| Flavia Quincey    | 74194       |
| Andie Quincey     | 3600        |
| Lela Quincey      | 650         |
| Annie Quincey     | 1060        |
| Ernest Forrest    | \<NULL>     |
| Constance Forrest | \<NULL>     |
| Wednesday Addams  | \<NULL>     |

Hier verweist die korrelierte Subquery auf die Spalte `member_id` aus der äußeren Query.

Der Unterschied zur nicht-korrelierten Subquery ist:
Sie läuft nicht einmal vor der äußeren Query, sondern für jede Zeile, die im Endergebnis landen könnte.

In unserem Fall liefert die äußere Query auf `FamilyMembers` also 8 Datensätze, und für jeden davon wird die korrelierte Subquery erneut ausgeführt.

## Auswirkungen auf die Performance

Beachte: Korrelierte Subqueries können Performance-Probleme verursachen –
vor allem, wenn die äußere Query viele Zeilen liefert, da die korrelierte Subquery für jede dieser Zeilen separat ausgeführt wird.
