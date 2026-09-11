---
meta:
    title: "Der HAVING-Operator"
    description: "Einsatz des HAVING-Operators in SQL"
---

# Der HAVING-Operator

Wir haben uns bereits die Query angeschaut, mit der wir den durchschnittlichen Mietpreis pro
Unterkunftstyp ermitteln:

ER-Diagramm der Datenbank Airbnb: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/operator-having).

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
```

| home_type       | avg_price |
| --------------- | --------- |
| Private room    | 89.4286   |
| Entire home/apt | 148.6667  |
| Shared room     | 40        |

Bauen wir die Query so um, dass im Ergebnis nur die Gruppen erscheinen, deren Durchschnittspreis größer als 50 ist.

Aus dem bisherigen Wissen entsteht schnell die Versuchung, dafür `WHERE` zu verwenden. Beim Versuch, eine solche Query auszuführen,
liefert das DBMS aber zwangsläufig einen Fehler und weist darauf hin, dass die Syntax `WHERE avg_price > 50` so nicht korrekt ist.

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
WHERE avg_price > 50
```

Vorab so viel: Zum Filtern von Gruppen brauchen wir den Operator `HAVING`:

**MySQL**

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
HAVING avg_price > 50
```

**PostgreSQL**

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
GROUP BY home_type
HAVING AVG(price) > 50
```

| home_type       | avg_price |
| --------------- | --------- |
| Private room    | 89.4286   |
| Entire home/apt | 148.6667  |

**PostgreSQL**

> In PostgreSQL stehen in `SELECT` deklarierte Aliase in `HAVING` nicht zur Verfügung.

## Ausführungsreihenfolge einer SQL-Query

Aber warum konnten wir `WHERE` nicht verwenden und wozu braucht es überhaupt einen eigenen Operator dafür? Die Antwort liegt in der Reihenfolge, in der eine SQL-Query ausgeführt wird.

![Schema der Ausführungsreihenfolge einer SQL-Query](https://sql-academy.org/static/guidePage/operator-having/sql_query_order_de.png "Schema der Ausführungsreihenfolge einer SQL-Query")

Unsere erste Query war falsch, weil wir das Feld `avg_price` der entstehenden Gruppen schon vor deren Bildung benutzen wollten –
`WHERE` wird nämlich vor der Gruppierung ausgeführt.

`WHERE` weiss zum Zeitpunkt seiner Ausführung also nichts von der späteren Gruppierung; es arbeitet ausschließlich mit den Datensätzen aus der Tabelle.
So können wir damit zum Beispiel die Datensätze der Tabelle `Rooms` nach Preis filtern, bevor die Gruppierung greift, und erst danach den Durchschnittspreis
der Gruppen der übrigen Unterkünfte berechnen:

```sql
SELECT home_type, AVG(price) as avg_price FROM Rooms
WHERE price > 50
GROUP BY home_type
```

| home_type       | avg_price |
| --------------- | --------- |
| Private room    | 96.875    |
| Entire home/apt | 148.6667  |

<br />

## Allgemeine Struktur einer Query mit HAVING

```sql
SELECT [konstanten, aggregat_funktionen, gruppierungs_felder]
FROM tabellen_name
WHERE zeilen_einschraenkungs_bedingungen
GROUP BY gruppierungs_felder
HAVING bedingung_fuer_zeilen_nach_gruppierung
ORDER BY sortier_bedingung
```

## Anwendungsbeispiel für HAVING

Ermitteln wir als Beispiel den Mindestpreis pro Unterkunftstyp mit Fernseher. Uns interessieren dabei nur Unterkunftstypen, zu denen mindestens
5 Wohnungen gehören.

Um zu diesem Ergebnis zu kommen, müssen wir:

- zuerst alle Daten aus der Tabelle holen

    ```sql
    SELECT ... FROM Rooms;
    ```

- dann aus allen Datensätzen der Tabelle `Rooms` nur die uns interessierenden auswählen, also nur Unterkünfte mit Fernseher

    ```sql
    SELECT ... FROM Rooms
    WHERE has_tv = True
    ```

- anschließend die Datensätze der Unterkünfte nach Typ gruppieren

    ```sql
    SELECT ... FROM Rooms
    WHERE has_tv = True
    GROUP BY home_type
    ```

- danach die entstandenen Gruppen nach unserer Bedingung filtern. Uns interessieren Gruppen mit mindestens 5 Vertretern

    ```sql
    SELECT ... FROM Rooms
    WHERE has_tv = True
    GROUP BY home_type
    HAVING COUNT(*) >= 5
    ```

- und zum Schluss schauen, was in der Aufgabe verlangt ist, und die nötigen Felder ausgeben. In unserem Fall brauchen wir
  den Namen des Unterkunftstyps und seinen Mindestpreis.
    ```sql
    SELECT home_type, MIN(price) as min_price FROM Rooms
    WHERE has_tv = True
    GROUP BY home_type
    HAVING COUNT(*) >= 5;
    ```
