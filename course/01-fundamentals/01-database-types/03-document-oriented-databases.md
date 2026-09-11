---
meta:
    title: "Dokumentenorientierte Datenbanken"
    description: "Definition dokumentenorientierter Datenbanken, Beispiele für die Datenspeicherung, Besonderheiten und Ranking der beliebtesten dokumentenorientierten DBMS."
---

# Dokumentenorientierte Datenbanken

> Dokumentenorientierte Datenbanken sind ein Datenbanktyp, der darauf abzielt, Daten in JSON-ähnlichen Dokumenten zu speichern und abzufragen.

Anders als andere Datenbanken arbeiten dokumentenorientierte Datenbanken mit „Dokumenten", die in Collections gruppiert sind.
Ein Dokument ist eine Menge von Attributen (Schlüssel und zugehöriger Wert).
Die Werte können einfache Datentypen sein (Strings, Zahlen oder Datumswerte) oder komplexere Strukturen wie
verschachtelte Objekte, Arrays und Verweise auf andere Dokumente.

## Beispiel für die Datenspeicherung

![Beispiel für die Datenspeicherung](https://sql-academy.org/static/guidePage/document-oriented-databases/de_document_oriented_databases_1.png "Beispiel für die Datenspeicherung")
![Beispiel für die Datenspeicherung](https://sql-academy.org/static/guidePage/document-oriented-databases/de_document_oriented_databases_2.png "Beispiel für die Datenspeicherung")

## Besonderheiten dokumentenorientierter Datenbanken

In relationalen Datenbanken ist die Struktur eines Datensatzes streng festgelegt, und jeder Datensatz hat dieselben Felder.
Auch wenn ein Feld nicht genutzt wird, ist es vorhanden – nur eben leer.

Dokumentenorientierte Datenbanken verfolgen einen anderen Ansatz: Sie haben kein festes Schema,
sodass du in einzelne Datensätze neue Informationen einfügen kannst, ohne dass alle anderen Datensätze in der Datenbank die gleiche Struktur haben müssen.

Dokumente werden über einen eindeutigen Schlüssel adressiert – meist einen automatisch generierten String.
Über diesen Schlüssel kannst du z. B. einen Datensatz abrufen oder auf andere Dokumente verweisen.

Ein weiteres wichtiges Merkmal dokumentenorientierter Datenbanken: Sie bieten – über die reine Schlüsselsuche wie bei Key-Value-Stores hinaus –
eine eigene Abfragesprache, deren Funktionsumfang, Syntax und Performance von Implementierung zu Implementierung unterschiedlich sind.

So kann zum Beispiel eine Abfrage im populärsten Vertreter dieser Datenbankart – MongoDB – aussehen:

```dbms=MongoDB
> db.users.find({"name": "Daniel"}).count()
> 1
```

## Ranking der DBMS nach Beliebtheit

![Ranking dokumentenorientierter DBMS nach Beliebtheit](https://sql-academy.org/static/guidePage/document-oriented-databases/de_document_oriented_databases_rating.png "Ranking dokumentenorientierter DBMS nach Beliebtheit")
