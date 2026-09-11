---
meta:
    title: "Bedingte Logik, der CASE-Ausdruck"
    description: "Bedingte Logik in SQL, Verwendung des CASE WHEN THEN END-Ausdrucks"
---

# Bedingte Logik, der CASE-Ausdruck

SQL erlaubt es dir – wie viele Programmiersprachen – bedingte Logik zu schreiben, sodass je nach einer Reihe von Bedingungen einer von mehreren möglichen Werten zurückgegeben wird. In diesem Artikel sehen wir uns an, wie das in SQL mit dem
`CASE`-Ausdruck funktioniert.

## Was ist bedingte Logik?

Unter bedingter Logik versteht man, dass ein Programm je nach bestimmten Bedingungen mehrere Ausführungspfade hat.

Beispiel: In der Datenbank „Schedule" gibt es die Tabelle `Student` mit dem Feld `birthday`, das das Geburtsdatum eines Studenten enthält. Angenommen,
in der Ergebnismenge soll nicht das Geburtsdatum selbst stehen, sondern der Text „Volljährig" oder „Minderjährig" – je nachdem,
ob der Student schon 18 ist. Das ist bedingte Logik: Je nach konkreter Bedingung wird entweder der eine oder der andere Wert ausgegeben.

ER-Diagramm der Datenbank Schedule: [auf SQL Academy öffnen](https://sql-academy.org/de/guide/case-expression).

So eine Query mit `CASE` kann zum Beispiel so aussehen:

**MySQL**

```sql
SELECT first_name, last_name,
CASE
  WHEN TIMESTAMPDIFF(YEAR, birthday, NOW()) >= 18 THEN 'Volljährig'
  ELSE 'Minderjährig'
END AS status
FROM Student
```

**PostgreSQL**

```sql
SELECT first_name, last_name,
CASE
  WHEN EXTRACT(YEAR FROM AGE(NOW(), birthday)) >= 18 THEN 'Volljährig'
  ELSE 'Minderjährig'
END AS status
FROM Student
```

| first_name | last_name    | status       |
| ---------- | ------------ | ------------ |
| Nikolaj    | Sokolov      | Volljährig   |
| Vyacheslav | Eliseev      | Volljährig   |
| Ivan       | Efremov      | Volljährig   |
| Anatolij   | ZHdanov      | Minderjährig |
| Georgij    | Noskov       | Volljährig   |
| Artyom     | Sergeev      | Minderjährig |
| Arina      | Evseeva      | Volljährig   |
| Angelina   | Voroncova    | Volljährig   |
| Ekaterina  | Ustinova     | Volljährig   |
| Raisa      | Lapina       | Volljährig   |
| Leonid     | Ignatov      | Minderjährig |
| Snezhana   | Seliverstova | Volljährig   |
| Semyon     | Biryukov     | Volljährig   |
| Georgij    | Baranov      | Volljährig   |
| YUliya     | Vishnyakova  | Volljährig   |
| Valentina  | Bolshakova   | Volljährig   |
| Leonid     | Kryukov      | Volljährig   |
| Vladislav  | Cvetkov      | Volljährig   |
| Snezhana   | Morozova     | Volljährig   |
| Lyubov     | Borisova     | Volljährig   |
| Anfisa     | Kalashnikova | Volljährig   |
| Anna       | Osipova      | Volljährig   |

## Syntax des suchenden CASE-Ausdrucks

```sql
CASE
    WHEN bedingung_1 THEN rueckgabe_wert_1
    WHEN bedingung_2 THEN rueckgabe_wert_2
    WHEN bedingung_n THEN rueckgabe_wert_n
    [ELSE standard_rueckgabe_wert]
END
```

Wenn `bedingung_1` wahr ist, gibt der `CASE`-Ausdruck `rueckgabe_wert_1` zurück, andernfalls wird `bedingung_2` geprüft und so weiter. Trifft keine der Bedingungen zu, kommt `NULL` zurück – oder `standard_rueckgabe_wert`, falls ein `ELSE`-Zweig angegeben ist.

### Beispiel

Schauen wir uns `CASE` am Beispiel der Bestimmung der Schulstufe an.

![Stufen der Schulbildung](https://sql-academy.org/static/guidePage/case-expression/de_school_education_stages.png "Stufen der Schulbildung")

**MySQL**

```sql
SELECT name,
CASE
  WHEN SUBSTRING(name, 1, INSTR(name, ' ')) IN (10, 11) THEN 'Oberstufe'
  WHEN SUBSTRING(name, 1, INSTR(name, ' ')) IN (5, 6, 7, 8, 9) THEN 'Mittelstufe'
  ELSE 'Grundschule'
END AS stage
FROM Class
```

**PostgreSQL**

```sql
SELECT name,
CASE
  WHEN SUBSTRING(name, 1, POSITION(' ' IN name) - 1) IN ('10', '11') THEN 'Oberstufe'
  WHEN SUBSTRING(name, 1, POSITION(' ' IN name) - 1) IN ('5', '6', '7', '8', '9') THEN 'Mittelstufe'
  ELSE 'Grundschule'
END AS stage
FROM Class
```

| name | stage       |
| ---- | ----------- |
| 8 A  | Mittelstufe |
| 8 B  | Mittelstufe |
| 9 C  | Mittelstufe |
| 9 B  | Mittelstufe |
| 9 A  | Mittelstufe |
| 10 B | Oberstufe   |
| 10 A | Oberstufe   |
| 11 B | Oberstufe   |
| 11 A | Oberstufe   |
| 7 A  | Mittelstufe |
| 7 B  | Mittelstufe |
| 6 A  | Mittelstufe |
| 6 B  | Mittelstufe |
| 5 A  | Mittelstufe |
| 5 B  | Mittelstufe |
| 4 A  | Grundschule |

**MySQL**

- Zuerst extrahieren wir die Klassennummer aus dem Namen:
    ```sql
    SUBSTRING(name, 1, INSTR(name, ' '))
    ```

**PostgreSQL**

- Zuerst extrahieren wir die Klassennummer aus dem Namen:
    ```sql
    SUBSTRING(name, 1, POSITION(' ' IN name) - 1)
    ```

* Dann prüfen wir, ob diese Nummer zur Liste der Oberstufen- bzw. Mittelstufenklassen gehört.
* Liegt die Klassennummer nicht im Bereich 5–11, geben wir „Grundschule" aus.

## Syntax des einfachen CASE-Ausdrucks

`CASE` hat auch eine einfachere Syntax, die dem suchenden `CASE` ähnelt, aber
weniger flexibel ist. Allgemein sieht sie so aus:

```sql
CASE wert
    WHEN vergleichs_wert_1 THEN rueckgabe_wert_1
    WHEN vergleichs_wert_2 THEN rueckgabe_wert_2
    WHEN vergleichs_wert_n THEN rueckgabe_wert_n
    [ELSE standard_rueckgabe_wert]
END
```

Bei dieser Syntax wird `wert` in `CASE` nacheinander mit den in `WHEN` angegebenen Werten verglichen. Stimmt einer überein, wird der Wert nach `THEN` zurückgegeben.

Mit dieser Syntax können wir unser vorheriges Beispiel so umschreiben:

**MySQL**

```sql
SELECT name,
CASE SUBSTRING(name, 1, INSTR(name, ' '))
  WHEN 11 THEN 'Oberstufe'
  WHEN 10 THEN 'Oberstufe'
  WHEN 9 THEN 'Mittelstufe'
  WHEN 8 THEN 'Mittelstufe'
  WHEN 7 THEN 'Mittelstufe'
  WHEN 6 THEN 'Mittelstufe'
  WHEN 5 THEN 'Mittelstufe'
  ELSE 'Grundschule'
END AS stage
FROM Class
```

**PostgreSQL**

```sql
SELECT name,
CASE SUBSTRING(name, 1, POSITION(' ' IN name) - 1)
  WHEN '11' THEN 'Oberstufe'
  WHEN '10' THEN 'Oberstufe'
  WHEN '9' THEN 'Mittelstufe'
  WHEN '8' THEN 'Mittelstufe'
  WHEN '7' THEN 'Mittelstufe'
  WHEN '6' THEN 'Mittelstufe'
  WHEN '5' THEN 'Mittelstufe'
  ELSE 'Grundschule'
END AS stage
FROM Class
```

| name | stage       |
| ---- | ----------- |
| 8 A  | Mittelstufe |
| 8 B  | Mittelstufe |
| 9 C  | Mittelstufe |
| 9 B  | Mittelstufe |
| 9 A  | Mittelstufe |
| 10 B | Oberstufe   |
| 10 A | Oberstufe   |
| 11 B | Oberstufe   |
| 11 A | Oberstufe   |
| 7 A  | Mittelstufe |
| 7 B  | Mittelstufe |
| 6 A  | Mittelstufe |
| 6 B  | Mittelstufe |
| 5 A  | Mittelstufe |
| 5 B  | Mittelstufe |
| 4 A  | Grundschule |

### Prüf dich selbst

Welchen Wert gibt der `CASE`-Ausdruck in diesem Fall zurück?

```sql
CASE 2
  WHEN 0 THEN 'Null'
  WHEN 1 THEN 'Eins'
  ELSE 'Viele'
END
```

1. Null — Der CASE-Ausdruck kann nicht "Null" zurückgeben, weil die Werte "2" und "0" nicht gleich sind.

2. Eins — Der CASE-Ausdruck kann nicht "Eins" zurückgeben, weil die Werte "2" und "1" nicht gleich sind.

3. **Richtige Antwort:** Viele — Der CASE-Ausdruck gibt tatsächlich "Viele" zurück, weil keiner der in WHEN angegebenen Werte mit "2" übereinstimmt.
