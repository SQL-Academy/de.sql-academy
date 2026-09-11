---
meta:
    title: "Stored Procedures und Functions in SQL"
    description: "Einführung in Stored Procedures und Functions in SQL. Wofür sie da sind, die wichtigsten Unterschiede und wann du welche Variante nutzt."
---

# Stored Procedures und Functions

In SQL gibt es neben einfachen Queries noch mächtigere Werkzeuge — **Stored Procedures** und **Stored Functions**.
Mit diesen Objekten kannst du fertige Codeblöcke anlegen, die du immer wieder verwenden kannst 🔄.

> **Stored Procedures und Functions** sind im Voraus geschriebene und in der Datenbank gespeicherte SQL-Skripte, die du über ihren Namen aufrufst.

Stell dir vor, du hast eine komplexe Query für die Verkaufsstatistik, die du jeden Tag brauchst.
Statt sie jedes Mal neu zu schreiben, legst du einfach eine Procedure oder Function an und rufst sie auf!

## Wozu sind sie gut?

Stored Procedures und Functions lösen mehrere wichtige Aufgaben:

- **🚀 Wiederverwendung von Code** — einmal geschrieben, überall einsetzbar. Kein Copy-Paste an unterschiedlichen Stellen.
- **⚡ Performance** — der Code läuft direkt auf dem Datenbankserver, oft schneller als normale Queries.
- **🔒 Sicherheit** — du kannst Zugriff auf die Procedure gewähren, ohne direkten Zugriff auf die Tabellen zu erlauben.
- **🛡️ Zentrale Logik** — die gesamte Business-Logik liegt an einem Ort, in der Datenbank.

## Die wichtigsten Unterschiede

Procedures und Functions wirken ähnlich, unterscheiden sich aber an einigen wichtigen Stellen:

**MySQL**

| Merkmal                | Stored Procedures                                | Stored Functions                                     |
| ---------------------- | ------------------------------------------------ | ---------------------------------------------------- |
| **Rückgabewert**       | Können gar nichts oder mehrere Werte zurückgeben | Geben immer genau einen Wert zurück                  |
| **In Queries nutzbar** | Werden separat aufgerufen                        | In SELECT, WHERE und anderen Query-Teilen verwendbar |
| **Datenänderung**      | Können Daten in Tabellen verändern               | Sind nur zum Lesen von Daten gedacht                 |
| **Aufruf**             | `CALL procedure_name()`                          | `SELECT function_name()`                             |

**PostgreSQL**

| Merkmal                | Stored Procedures                  | Stored Functions                                     |
| ---------------------- | ---------------------------------- | ---------------------------------------------------- |
| **Rückgabewert**       | Können keine Werte zurückgeben     | Geben immer genau einen Wert zurück                  |
| **In Queries nutzbar** | Werden separat aufgerufen          | In SELECT, WHERE und anderen Query-Teilen verwendbar |
| **Datenänderung**      | Können Daten in Tabellen verändern | Können Daten in Tabellen verändern                   |
| **Aufruf**             | `CALL procedure_name()`            | `SELECT function_name()`                             |

## Wann nimmst du eine Procedure?

**Stored Procedures** passen am besten, wenn du:

- eine Abfolge von Operationen ausführen willst (z. B. Bestellung anlegen, Bestand reduzieren, Benachrichtigung senden)
- Daten in mehreren Tabellen gleichzeitig ändern willst
- komplexe Business-Logik umsetzen willst
- mehrere Ergebnismengen zurückgeben willst

### Beispiel für eine Procedure

Angenommen, wir brauchen eine Procedure für die Anlage einer Bestellung:

```sql
-- Beispielhafter Aufruf einer Procedure (konzeptionell)
CALL create_order(customer_id = 123, product_id = 456, quantity = 2);
```

Eine solche Procedure kann:

1. die Warenverfügbarkeit prüfen
2. einen Eintrag in der Bestelltabelle anlegen
3. die Lagerbestände aktualisieren
4. einen Eintrag in der Aktionshistorie ergänzen

## Wann nimmst du eine Function?

**Stored Functions** sind ideal, wenn du:

- Berechnungen ausführen und ein Ergebnis zurückgeben willst
- eine wiederverwendbare Formel anlegen willst
- Daten in einer bestimmten Weise umformen willst
- das Ergebnis in anderen SQL-Queries weiterverwenden willst

### Beispiel für eine Function

Bauen wir eine Function für die Rabattberechnung:

```sql
-- Beispiel für die Verwendung einer Function in einer Query
SELECT
    product_name,
    price,
    calculate_discount(price, customer_type) AS discount_amount
FROM Products;
```

Diese Function nimmt Preis und Kundentyp entgegen und liefert den Rabattbetrag — den du in beliebigen Queries einsetzen kannst.

## Einfache Faustregel

Wenn du unsicher bist, hilft diese Daumenregel:

- **Du brauchst einen Wert für eine Query?** → Function
- **Du willst mehrere Schritte ausführen oder Daten ändern?** → Procedure

## Wissen festigen

Jetzt, da du die wichtigsten Unterschiede kennst, ordne die Aufgaben im interaktiven Spiel zu:

Die interaktive Demonstration ist [in der Lektion auf SQL Academy verfügbar](https://sql-academy.org/de/guide/stored-procedures-and-functions).

## Wie geht's weiter?

Das war eine Einführung in das große Konzept. In den nächsten Artikeln schauen wir uns im Detail an,
wie du Stored Procedures und Functions erstellst und mit ihnen arbeitest.
