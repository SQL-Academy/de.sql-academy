---
meta:
    title: "LIKE-Operator"
    description: "Syntax der SQL-Operatoren LIKE und ILIKE für die Suche nach Datensätzen anhand eines Musterstrings."
---

# LIKE-Operator

Der `LIKE`-Operator wird in Bedingungen verwendet, wenn du prüfen möchtest, ob ein String einem bestimmten Muster entspricht.

Angenommen, wir haben eine Tabelle `Users` mit dem Feld `email`:

```sql
SELECT name, email FROM Users;
```

| name              | email                  |
| ----------------- | ---------------------- |
| Bruce Willis      | barjam\@hotmail.com    |
| George Clooney    | tellis\@me.com         |
| Kevin Costner     | metzzo\@hotmail.com    |
| Samuel L. Jackson | moonlapse\@outlook.com |
| Kurt Russell      | gator\@live.com        |

Nehmen wir an, wir möchten alle Nutzer finden, deren Mail-Adresse zur Second-Level-Domain „hotmail“ gehört. Konkret sollen also nur die Datensätze ausgewählt werden, die folgender Bedingung entsprechen:

- nach dem Zeichen „@“ folgt „hotmail“
- nach „hotmail“ folgt das Zeichen „.“ und danach eine beliebige Zeichenfolge

Für solche nicht-trivialen Suchen in Stringfeldern brauchst du den `LIKE`-Operator.

## Syntax

```sql
... WHERE tabellen_feld [NOT] LIKE muster_string
```

Das Muster kann zwei Sonderzeichen enthalten — `%` und `_`. Das bedeutet jeweils:

| Zeichen | Beschreibung                                                     |
| ------- | ---------------------------------------------------------------- |
| `%`     | Eine beliebige Zeichenfolge: 0 Zeichen, 1 Zeichen, Viele Zeichen |
| `_`     | Genau ein Zeichen                                                |

So kann unsere Query, die Nutzer der Domain „hotmail“ findet, etwa so aussehen:

```sql
SELECT name, email FROM Users
WHERE email LIKE '%@hotmail.%'
```

| name                 | email                 |
| -------------------- | --------------------- |
| Bruce Willis         | barjam\@hotmail.com   |
| Kevin Costner        | metzzo\@hotmail.com   |
| Jennifer Lopez       | barjam\@hotmail.com   |
| Harrison Ford        | kostas\@hotmail.com   |
| Michael Douglas      | timtroyr\@hotmail.com |
| Catherine Zeta-Jones | flakeg\@hotmail.com   |

## Beispiele

- ```sql
  ... WHERE tabellen_feld LIKE 'text%'
  ```

    Passt auf alle Strings, die mit „text“ beginnen.

- ```sql
  ... WHERE tabellen_feld LIKE '%text'
  ```

    Passt auf alle Strings, die auf „text“ enden.

- ```sql
  ... WHERE tabellen_feld LIKE '_ext'
  ```

    Passt auf Strings der Länge 4, deren letzte drei Zeichen „ext“ sein müssen. Zum Beispiel „text“ und „next“.

- ```sql
  ... WHERE tabellen_feld LIKE 'begin%end'
  ```
    Passt auf Strings, die mit „begin“ beginnen und mit „end“ enden.

**MySQL**

> In MySQL sind die Muster standardmäßig nicht zwischen Groß- und Kleinschreibung unterscheidend.

**PostgreSQL**

> In PostgreSQL unterscheiden die Muster zwischen Groß- und Kleinschreibung. Für eine Suche ohne Berücksichtigung der Groß-/Kleinschreibung verwendest du den Operator `ILIKE`.

## Sonderzeichen escapen

Manchmal willst du Strings finden, in denen `%` oder `_` Teil des eigentlichen Textes sind und nicht Bestandteil des Musters. In solchen Fällen musst du diese Zeichen escapen.

In `LIKE`-Mustern wird standardmäßig der Backslash `\` zum Escapen verwendet. Wenn du etwa die IDs der Tasks finden willst, deren Fortschritt genau `3%` beträgt, kannst du das so schreiben:

```sql
SELECT job_id FROM Jobs
WHERE progress LIKE '3\%';
```

### ESCAPE-Zeichen

Wenn du statt des Standardzeichens ein anderes Escape-Zeichen verwenden möchtest, kannst du es mit `ESCAPE` explizit angeben.

### Syntax mit ESCAPE

```sql
... WHERE tabellen_feld LIKE 'muster_string' ESCAPE 'escape_zeichen'
```

Dieselbe Query lässt sich mit explizit angegebenem Escape-Zeichen auch so schreiben:

```sql
SELECT job_id FROM Jobs
WHERE progress LIKE '3!%' ESCAPE '!';
```

Hier übernimmt `!` dieselbe Rolle wie `\` im vorherigen Beispiel.

## Interaktive Übung

Jetzt vertiefen wir das Gelernte mit einer kleinen Übung!
In der Aufgabe unten sollst du E-Mail-Adressen den passenden LIKE-Mustern zuordnen, indem du sie in die entsprechenden Felder ziehst.

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/operator-like).
