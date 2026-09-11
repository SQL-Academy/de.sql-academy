---
meta:
    title: "Subqueries mit mehreren Zeilen und einer Spalte"
    description: "Subqueries mit den Operatoren ANY, IN, ALL"
---

# Subqueries mit mehreren Zeilen und einer Spalte

Wenn eine Subquery mehr als eine Zeile zurückgibt, kann sie nicht einfach mit Vergleichsoperatoren verwendet werden, wie es mit <a href="https://sql-academy.org/de/guide/subquery-with-one-column-one-row" target="_blank">skalaren Subqueries</a> möglich war.

Mit Subqueries, die mehrere Zeilen und eine Spalte zurückgeben, lassen sich aber drei zusätzliche Operatoren verwenden.

## Subquery und der Operator ALL

Mit dem Operator `ALL` können wir einen einzelnen Wert mit jedem Wert in der von der Subquery gelieferten Menge vergleichen. Diese Bedingung gibt nur dann `TRUE` zurück, wenn alle Vergleiche des einzelnen Wertes mit den Werten der Menge `TRUE` ergeben.

Die folgende künstliche Abfrage prüft zum Beispiel, ob für alle Wohnräume die Bedingung gilt, dass sie günstiger als 200 sind.

```sql
SELECT 200 > ALL(SELECT price FROM Rooms)
```

Oder ein praktischeres Beispiel: Wir wollen die Namen aller Vermieter finden, die selbst noch nie eine Unterkunft gemietet haben. Um diese Liste zu bekommen, gehen wir folgendermaßen vor:

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/subquery-with-one-column-several-row).

- Liste der Namen aller Vermieter holen

    ```sql
    SELECT DISTINCT name FROM Users INNER JOIN Rooms
    ON Users.id = Rooms.owner_id
    ```

- Liste der IDs aller Nutzer holen, die Unterkünfte gemietet haben

    ```sql
    SELECT DISTINCT user_id FROM Reservations
    ```

- Die erste Liste aller Vermieter nach der Bedingung filtern, dass die ID des Vermieters keiner der IDs der Nutzer entspricht, die jemals eine Unterkunft gemietet haben

    ```sql
    SELECT DISTINCT name FROM Users INNER JOIN Rooms
        ON Users.id = Rooms.owner_id
        WHERE Users.id <> ALL (
            SELECT DISTINCT user_id FROM Reservations
        )
    ```

## Subquery und der Operator IN

Der Operator `IN` prüft, ob ein konkreter Wert in einer Menge von Werten enthalten ist. Als solche Menge lässt sich genau eine Subquery verwenden, die mehrere Zeilen mit einer Spalte zurückgibt.

Wenn wir zum Beispiel alle Informationen über die Vermieter von Unterkünften mit einem Preis über 150 Geldeinheiten brauchen, geht das so:

```sql
SELECT * FROM Users WHERE id IN (
    SELECT DISTINCT owner_id FROM Rooms WHERE price >= 150
)
```

## Subquery und der Operator ANY

Eine Bedingung mit `ANY` verhält sich ähnlich, gibt aber `TRUE` zurück, wenn mindestens ein Vergleich des einzelnen Wertes mit einem Wert in der Menge `TRUE` ergibt.

Schreiben wir damit dieselbe Abfrage wie zuvor mit `IN`: Wir suchen Nutzer, die mindestens 1 Wohnraum mit einem Preis über 150 besitzen.

```sql
SELECT * FROM Users WHERE id = ANY (
    SELECT DISTINCT owner_id FROM Rooms WHERE price >= 150
)
```
