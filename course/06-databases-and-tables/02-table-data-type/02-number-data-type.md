---
meta:
    title: "Numerischer Datentyp in SQL: MySQL und PostgreSQL"
    description: "Arbeit mit Zahlen in MySQL und PostgreSQL. Die wichtigsten numerischen Datentypen: INTEGER, DECIMAL, FLOAT und mehr."
---

# Numerischer Datentyp

Numerische Daten unterscheiden sich nach exakt und näherungsweise sowie nach ganzzahlig und reell. Eine eigene Kategorie bilden Bit-Werte.

**MySQL**

## Exakte ganze Zahlen

| Typ                                      | Speicher | Wertebereich                                                                                                        |
| :--------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------ |
| `TINYINT`                                | 1 Byte   | von -128 bis 127 (von -2<sup>7</sup> bis 2<sup>7</sup>-1) <br /> von 0 bis 255 (von 0 bis 2<sup>8</sup>-1)          |
| `SMALLINT`                               | 2 Byte   | von -32768 bis 32767 (von -2<sup>15</sup> bis 2<sup>15</sup>-1) <br /> von 0 bis 65535 (von 0 bis 2<sup>16</sup>-1) |
| `MEDIUMINT`                              | 3 Byte   | von -2<sup>23</sup> bis 2<sup>23</sup>-1 <br /> von 0 bis 2<sup>24</sup>-1                                          |
| `INT` <br /> `INTEGER` <br /> (Synonyme) | 4 Byte   | von -2<sup>31</sup> bis 2<sup>31</sup>-1 <br /> von 0 bis 2<sup>32</sup>-1                                          |
| `BIGINT`                                 | 8 Byte   | von -2<sup>63</sup> bis 2<sup>63</sup>-1 <br /> von 0 bis 2<sup>64</sup>-1                                          |

Ganze Zahlen können mit dem Schlüsselwort `UNSIGNED` deklariert werden. Dann sind in der Spalte keine negativen Werte mehr möglich,
und der erlaubte Wertebereich verdoppelt sich. So nimmt `TINYINT` Werte von -128 bis 127 an, `TINYINT UNSIGNED` dagegen von 0 bis 255.

## Exakte reelle Zahlen

| Typ                                                    | Wertebereich                        |
| :----------------------------------------------------- | :---------------------------------- |
| `DEC[(M,D)]` <br /> `DECIMAL[(M,D)]` <br /> (Synonyme) | Abhängig von den Parametern M und D |

Der Typ `DECIMAL` speichert reelle Zahlen exakt. Er wird verwendet, wenn die Genauigkeit kritisch ist – zum Beispiel bei finanziellen Daten.

Beispiel:

```sql
CREATE TABLE Users (
    ...
    salary DECIMAL(5,2)
);
```

In diesem Beispiel wird festgelegt, dass in der Spalte `salary` Zahlen mit maximal 5 Stellen gespeichert werden, davon 2 nach dem Komma.
Die Spalte fasst also Werte im Bereich von -999.99 bis 999.99.

Die Syntax `DECIMAL` ist äquivalent zu `DECIMAL(M)` und `DECIMAL(M,0)`. Standardmäßig ist `M` gleich 10.

Vor- und Nachkommateil werden als zwei separate ganze Zahlen abgelegt.
Damit lässt sich der Speicherbedarf leicht berechnen: Bei `DECIMAL(5,2)` hat der Vorkommateil 3 Stellen und braucht 2 Byte,
der Nachkommateil mit 2 Stellen kommt mit 1 Byte aus. Insgesamt werden also 3 Byte benötigt.

## Bit-Werte

| Typ                                       | Speicher | Wertebereich                              |
| :---------------------------------------- | :------- | :---------------------------------------- |
| `BIT[(M)]`                                | M Bit    | von 1 bis 64 Bit, abhängig vom Wert von M |
| `BOOL` <br /> `BOOLEAN` <br /> (Synonyme) | 1 Bit    | entweder 0 oder 1                         |

Der Datentyp `BIT(M)` speichert eine Bit-Folge fester Länge. Standardmäßig sind das 8 Bit.
Verwendet der zugewiesene Wert weniger als M Bit, wird links mit Nullen aufgefüllt.
Versuchst du zum Beispiel, den Wert `b'101'` in `BIT(6)` zu schreiben, wird tatsächlich `b'000101'` gespeichert.

## Näherungswerte

| Typ                                                      | Speicher | Wertebereich                                                          |
| :------------------------------------------------------- | :------- | :-------------------------------------------------------------------- |
| `FLOAT[(M, D)]`                                          | 4 Byte   | Minimum ±1.17·10<sup>-39</sup> <br /> Maximum ±3.4·10<sup>38</sup>    |
| `REAL[(M, D)]` <br /> `DOUBLE[(M, D)]` <br /> (Synonyme) | 8 Byte   | Minimum ±2.22·10<sup>-308</sup> <br /> Maximum ±1.79·10<sup>308</sup> |

Auch Gleitkomma-Datentypen können `UNSIGNED` sein.
Wie bei den ganzzahligen Typen verhindert das Attribut, dass in der Spalte negative Werte gespeichert werden, anders als dort
bleibt der maximale Wertebereich der Spalte aber unverändert.

**PostgreSQL**

## Ganze Zahlen

| Typ                                      | Speicher | Wertebereich                                     |
| :--------------------------------------- | :------- | :----------------------------------------------- |
| `SMALLINT`                               | 2 Byte   | von -32768 bis 32767                             |
| `INT` <br /> `INTEGER` <br /> (Synonyme) | 4 Byte   | von -2147483648 bis 2147483647                   |
| `BIGINT`                                 | 8 Byte   | von -9223372036854775808 bis 9223372036854775807 |

## Auto-Increment-Typen

| Typ           | Speicher | Wertebereich                  |
| :------------ | :------- | :---------------------------- |
| `SMALLSERIAL` | 2 Byte   | von 1 bis 32767               |
| `SERIAL`      | 4 Byte   | von 1 bis 2147483647          |
| `BIGSERIAL`   | 8 Byte   | von 1 bis 9223372036854775807 |

Die `SERIAL`-Typen sind Pseudotypen zum Anlegen von Auto-Increment-Spalten. `SERIAL` entspricht `INTEGER` mit einer automatisch erzeugten Sequenz.

## Exakte reelle Zahlen

| Typ                                                                                  | Genauigkeit       | Wertebereich                                                |
| :----------------------------------------------------------------------------------- | :---------------- | :---------------------------------------------------------- |
| `DECIMAL[(precision, scale)]` <br /> `NUMERIC[(precision, scale)]` <br /> (Synonyme) | Benutzerdefiniert | Bis zu 131072 Stellen vor dem Komma und bis zu 16383 danach |

Der Typ `NUMERIC` speichert reelle Werte exakt. Er wird verwendet, wenn die Genauigkeit kritisch ist – zum Beispiel bei finanziellen Daten.

Beispiel:

```sql
CREATE TABLE Users (
    ...
    salary NUMERIC(10,2)
);
```

In diesem Beispiel wird festgelegt, dass in der Spalte `salary` Zahlen mit maximal 10 Stellen gespeichert werden, davon 2 nach dem Komma.
Die Spalte fasst also Werte im Bereich von -99999999.99 bis 99999999.99.

## Näherungswerte

| Typ                | Speicher | Genauigkeit | Wertebereich          |
| :----------------- | :------- | :---------- | :-------------------- |
| `REAL`             | 4 Byte   | 6 Stellen   | von 1E-37 bis 1E+37   |
| `DOUBLE PRECISION` | 8 Byte   | 15 Stellen  | von 1E-307 bis 1E+308 |

Gleitkomma-Typen werden für näherungsweise Berechnungen verwendet. PostgreSQL unterstützt zusätzlich die Sonderwerte `Infinity`, `-Infinity` und `NaN` (keine Zahl).
