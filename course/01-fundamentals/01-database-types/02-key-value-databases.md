---
meta:
    title: "Key-Value-Datenbanken"
    description: "Datenbanken nach dem Key-Value-Prinzip. Ihre Vor- und Nachteile. Ranking der beliebtesten Key-Value-Datenbanken."
---

# Key-Value-Datenbanken

> Key-Value-Datenbanken speichern Daten als Sammlung von Schlüssel-Wert-Paaren,
> wobei der Schlüssel als eindeutiger Identifikator dient.

Es entsteht also eine eindeutige Zuordnung von Wert zu Schlüssel. Sowohl Schlüssel als auch Werte können alles Mögliche sein – von einfachen Datentypen bis zu komplexen, zusammengesetzten Objekten.

## Beispiel einer Key-Value-Datenbank

![Beispiel einer Key-Value-Datenbank](https://sql-academy.org/static/guidePage/key-value-databases/key_value_dbms.png "Beispiel einer Key-Value-Datenbank")

## Vorteile

- Geschwindigkeit
- Einfaches Speichermodell
- Flexibilität: Werte können beliebig sein, einschließlich JSON

## Nachteile

- Skalieren schlecht, wenn das Datenmodell komplexer wird
- Ineffizient bei der Arbeit mit Gruppen von Datensätzen

Die Standardumsetzung sagt nichts über den Inhalt des tatsächlichen Werts aus – beim Auslesen über den Schlüssel hast du keine Garantie,
was du bekommst. Das heißt: Du musst die Daten, die du nicht brauchst, im Code deiner Anwendung filtern oder weiterverarbeiten.
Performance-technisch ist das in der Regel ineffizienter, als einen Grossteil dieser Arbeit in der Datenbank zu erledigen.

- Keine Abfragesprache

Damit landet Logik, die normalerweise in der Datenbank steckt, im Anwendungscode. Das macht den Code komplexer und schwerer wartbar.

## Ranking der beliebtesten Key-Value-DBMS

![Ranking der Key-Value-DBMS](https://sql-academy.org/static/guidePage/key-value-databases/popular_key_value_dbms.png "Ranking der Key-Value-DBMS")
