---
meta:
    title: "Unterabfragen in SQL"
    description: "Unterabfragen und verschachtelte Queries in SQL. Ihre Arten und Einsatzmöglichkeiten."
---

# Unterabfragen

Unterabfragen (Subqueries) gehören zu den mächtigsten Werkzeugen in SQL und lassen sich in jeder Art von Query einsetzen.
In den nächsten Kapiteln lernen wir die wichtigsten Arten von Unterabfragen kennen und schauen uns Beispiele für ihre Anwendung an.

> Eine Unterabfrage ist eine Query, die innerhalb einer anderen SQL-Query verwendet wird.
> Sie steht immer in runden Klammern und wird in der Regel vor der
> Hauptquery ausgeführt.

Wie jede SQL-Query liefert auch eine Unterabfrage ein Ergebnis. Das kann sein:

- eine Zeile mit einer Spalte;
- mehrere Zeilen mit einer Spalte;
- mehrere Zeilen mit mehreren Spalten.

Vom Typ des Ergebnisses der Unterabfrage hängt ab, welche Operatoren in der Hauptquery verwendet werden können.

## Beispiel

Wir holen die Liste aller Reservierungen für die aktuell teuerste Unterkunft:

```sql
SELECT * FROM Reservations
    WHERE Reservations.room_id = (
        SELECT id FROM Rooms ORDER BY price DESC LIMIT 1
    )
```

Hier wird die Query, die die teuerste Unterkunft ermittelt, als Unterabfrage ausgeführt;
ihr Ergebnis wird dann in der Hauptquery eingesetzt.

```sql
SELECT id FROM Rooms ORDER BY price DESC LIMIT 1
```

| id  |
| --- |
| 21  |

Mit den einzelnen Arten von Unterabfragen beschäftigen wir uns in den nächsten Kapiteln im Detail.
