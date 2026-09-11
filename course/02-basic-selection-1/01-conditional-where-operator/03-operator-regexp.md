---
meta:
    title: "Die Operatoren REGEXP und ~ in SQL"
    description: "Reguläre Ausdrücke in SQL mit den Operatoren REGEXP in MySQL und ~ in PostgreSQL nutzen."
---

**MySQL**

# Der Operator REGEXP für reguläre Ausdrücke

Der Operator `REGEXP` (oder sein Synonym `RLIKE`) in MySQL wird zum Suchen und Verarbeiten von Stringdaten
mit Hilfe von regulären Ausdrücken verwendet.

**PostgreSQL**

# Der Operator \~ für reguläre Ausdrücke

Die Operatoren `~` und `~*` in PostgreSQL werden zum Suchen und Verarbeiten von Stringdaten
mit Hilfe von regulären Ausdrücken verwendet.

Reguläre Ausdrücke bieten mächtige Möglichkeiten für komplexe Suchmuster,
die sich mit dem `LIKE`-Operator nur schwer umsetzen lassen.

## Wann reguläre Ausdrücke statt LIKE einsetzen?

Der `LIKE`-Operator eignet sich gut für einfache Suchmuster wie das Finden von Strings,
die mit bestimmten Zeichen beginnen oder enden bzw. bestimmte Teilstrings enthalten.
Wenn du jedoch eine komplexere und flexiblere Suche brauchst — etwa nach mehreren Bedingungen oder
mit Sonderzeichen und Bereichen — werden die Operatoren für reguläre Ausdrücke zu einem unverzichtbaren Werkzeug.

Wichtig ist: `LIKE` vergleicht den String mit dem gesamten Muster, während ein regulärer Ausdruck nach einer Übereinstimmung innerhalb des Strings sucht. Wenn du explizit den Anfang oder das Ende eines Strings prüfen willst, nutzt du die Sonderzeichen `^` und `$`.

## Syntax regulärer Ausdrücke

**MySQL**

```sql
... WHERE table_field REGEXP 'pattern';
```

Wobei `pattern` der reguläre Ausdruck ist, der das Suchmuster festlegt.

**PostgreSQL**

```sql
... WHERE table_field ~ 'pattern';   -- mit Berücksichtigung der Groß-/Kleinschreibung
... WHERE table_field ~* 'pattern';  -- ohne Berücksichtigung der Groß-/Kleinschreibung
```

Wobei `pattern` der reguläre Ausdruck ist, der das Suchmuster festlegt.

## Wichtige Details

**MySQL**

1. **Groß-/Kleinschreibung**

    Standardmäßig sind reguläre Ausdrücke in MySQL nicht zwischen Groß- und Kleinschreibung unterscheidend.
    Zum Beispiel findet `REGEXP 'abc'` sowohl `abc` als auch `Abc` und `ABC`.

2. **Sonderzeichen**

    Einige Zeichen haben in regulären Ausdrücken eine besondere Bedeutung und müssen escaped werden
    (zum Beispiel `.`, `*`, `+`, `?`, `[`, `]`, `(`, `)`, `{`, `}`, `|`, `\`).

    Zum Escapen solcher Zeichen verwendest du einen doppelten Backslash — `\\`.

**PostgreSQL**

1. **Groß-/Kleinschreibung**

    Standardmäßig unterscheiden reguläre Ausdrücke in PostgreSQL zwischen Groß- und Kleinschreibung.

    - Operator `~` — mit Berücksichtigung der Groß-/Kleinschreibung
    - Operator `~*` — ohne Berücksichtigung der Groß-/Kleinschreibung

2. **Sonderzeichen**

    Einige Zeichen haben in regulären Ausdrücken eine besondere Bedeutung und müssen escaped werden
    (zum Beispiel `.`, `*`, `+`, `?`, `[`, `]`, `(`, `)`, `{`, `}`, `|`, `\`).

    Zum Escapen solcher Zeichen verwendest du einen einfachen Backslash — `\`.

## Sonderzeichen und Konstrukte

| Zeichen und Konstrukte | Wofür sie stehen                                                |
| :--------------------- | :-------------------------------------------------------------- |
| `*`                    | 0 oder mehr Vorkommen des vorangehenden Musters                 |
| `+`                    | 1 oder mehr Vorkommen des vorangehenden Musters                 |
| `.`                    | Ein beliebiges einzelnes Zeichen                                |
| `?`                    | 0 oder 1 Vorkommen des vorangehenden Musters                    |
| `^`                    | Anfang des Strings                                              |
| `$`                    | Ende des Strings                                                |
| `[abc]`                | Ein beliebiges Zeichen aus den eckigen Klammern                 |
| `[^abc]`               | Ein beliebiges Zeichen, das nicht in den eckigen Klammern steht |
| `[A-Z]`                | Ein beliebiger Großbuchstabe                                    |
| `[a-z]`                | Ein beliebiger Kleinbuchstabe                                   |
| `[0-9]`                | Eine beliebige Ziffer                                           |
| `p1\|p2\|p3`           | Eines der Muster `p1`, `p2` oder `p3`                           |
| `{n}`                  | Genau `n` Vorkommen des vorangehenden Musters                   |
| `{m,n}`                | `m` bis `n` Vorkommen des vorangehenden Musters                 |

## Beispiele mit Erklärung

- **Hole alle Nutzer, deren Namen mit „John“ beginnen:**

    **MySQL**

    ```sql
    SELECT * FROM Users WHERE name REGEXP '^John'
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Users WHERE name ~ '^John'
    ```

    | id  | name          | email             | email_verified_at        | password             | phone_number    |
    | --- | ------------- | ----------------- | ------------------------ | -------------------- | --------------- |
    | 18  | John Travolta | wainwrig\@msn.com | 2016-11-19T12:30:43.000Z | fzjhl0v82o0amalr8649 | +1 202 555 0176 |
    | 28  | Johnny Depp   | cgarcia\@yahoo.ca | 2017-05-26T01:19:06.000Z | qpp6hbnae42cdhmxlk4j | +7 401 195 7363 |

    Dieser Ausdruck sucht Strings, die mit „John“ beginnen. Das Zeichen `^` markiert den Anfang des Strings.

- **Gib alle Schulfächer aus, deren Name auf „e“ oder „y“ endet:**

    **MySQL**

    ```sql
    SELECT * FROM  Subject WHERE name REGEXP '[ey]$'
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM  Subject WHERE name ~ '[ey]$'
    ```

    | id  | name             |
    | --- | ---------------- |
    | 2   | Russian language |
    | 3   | Literature       |
    | 5   | Chemistry        |
    | 6   | Geography        |
    | 7   | History          |
    | 8   | Biology          |
    | 9   | English language |
    | 11  | Physical Culture |
    | 13  | Technology       |

    In diesem Beispiel legt `[ey]` die möglichen Werte für das Muster fest, und `$` gibt an, womit der String enden muss.

- **Finde alle Nutzer, deren E-Mail-Adresse auf „@outlook.com“ oder „@icloud.com“ endet:**

    **MySQL**

    ```sql
    SELECT * FROM Users WHERE email REGEXP '@(outlook\\.com|icloud\\.com)$'
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Users WHERE email ~ '@(outlook\.com|icloud\.com)$'
    ```

    | id  | name              | email                  | email_verified_at        | password             | phone_number      |
    | --- | ----------------- | ---------------------- | ------------------------ | -------------------- | ----------------- |
    | 7   | Samuel L. Jackson | moonlapse\@outlook.com | 2018-07-19T11:16:13.000Z | i6yvht95527z3idgqx9y | +1 202 555 0162   |
    | 13  | Steve Martin      | nelson\@outlook.com    | 2016-07-29T04:25:00.000Z | w76yphg3kvzg77ilmxfs | +1 202 555 0138   |
    | 29  | Pierce Brosnan    | treeves\@icloud.com    | 2019-03-08T01:56:00.000Z | lqiwecclne9rv8woo2go | +7 401 749 3620   |
    | 30  | Sean Connery      | jschauma\@icloud.com   | 2016-05-21T00:45:17.000Z | lyh4jkdxkvtvulvqi5db | +7 401 511 6783   |
    | 31  | Bruce Willis      | kewley\@icloud.com     | 2016-12-08T20:18:59.000Z | 0ofa2khvnptiackbssv0 | +375 154 771 3462 |

    Auch hier markiert `$` das Ende des Strings, und `|` erlaubt mehrere Alternativen.

- **Finde alle Nutzer, deren Telefonnummer keine Ziffern „2“ und „8“ enthält:**

    **MySQL**

    ```sql
    SELECT * FROM Users WHERE phone_number REGEXP '^[^28]*$'
    ```

    **PostgreSQL**

    ```sql
    SELECT * FROM Users WHERE phone_number ~ '^[^28]*$'
    ```

    | id  | name        | email                 | email_verified_at        | password             | phone_number    |
    | --- | ----------- | --------------------- | ------------------------ | -------------------- | --------------- |
    | 27  | Brad Pitt   | kewley\@optonline.net | 2017-02-11T05:45:15.000Z | 829j2ygocn8btzae49kv | +7 401 741 3797 |
    | 28  | Johnny Depp | cgarcia\@yahoo.ca     | 2017-05-26T01:19:06.000Z | qpp6hbnae42cdhmxlk4j | +7 401 195 7363 |

    In diesem Beispiel steht `[^28]` für jedes Zeichen außer „2“ und „8“, und
    `*` erlaubt eine beliebige Anzahl solcher Zeichen. Die Zeichen `^` und `$` markieren Anfang und Ende des Strings
    und sorgen dafür, dass der gesamte String dem Muster entspricht.

- **Finde alle Nutzer, deren Telefonnummer mit „+7“ beginnt**

    **MySQL**

    ```sql
    SELECT name, phone_number FROM Users WHERE phone_number REGEXP '^\\+7'
    ```

    **PostgreSQL**

    ```sql
    SELECT name, phone_number FROM Users WHERE phone_number ~ '^\+7'
    ```

    | name           | phone_number    |
    | -------------- | --------------- |
    | Hideo Kojima   | +7 401 452 0052 |
    | ClINT Eastwood | +7 401 722 0912 |
    | Brad Pitt      | +7 401 741 3797 |
    | Johnny Depp    | +7 401 195 7363 |
    | Pierce Brosnan | +7 401 749 3620 |
    | Sean Connery   | +7 401 511 6783 |

    In diesem Beispiel markiert `^` den Anfang des Strings. Wir suchen also Strings, die mit einem bestimmten Muster beginnen.

    **MySQL**

    Da `+` in regulären Ausdrücken ein Sonderzeichen ist, muss es mit einem doppelten Backslash (`\\`) escaped werden,
    damit es als gewöhnliches `+` interpretiert wird. So entspricht `\\+` dem Zeichen `+` im String.

    **PostgreSQL**

    Da `+` in regulären Ausdrücken ein Sonderzeichen ist, muss es mit einem einfachen Backslash (`\`) escaped werden,
    damit es als gewöhnliches `+` interpretiert wird. So entspricht `\+` dem Zeichen `+` im String.
