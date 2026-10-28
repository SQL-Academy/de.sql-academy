---
meta:
    title: "Transaktionen: MySQL und PostgreSQL"
    description: "Lerne die Bedeutung von Transaktionen in der Datenbankverwaltung mit MySQL und PostgreSQL und wie sie die Zuverlässigkeit von Anwendungen sicherstellen. Erfahre, warum Transaktionen für die Wahrung der Datenintegrität unerlässlich sind und wie sie helfen, Fehler beim gleichzeitigen Zugriff vieler Nutzer zu vermeiden."
---

# Transaktionen

Wenn Datenbankserver zu 100 % der Zeit störungsfrei laufen würden, wenn Nutzer Programme immer in Ruhe zu Ende laufen ließen und wenn Anwendungen immer ohne fatale Fehler enden würden, die die Ausführung anhalten, gäbe es nichts, was man über den gleichzeitigen Zugriff auf eine Datenbank diskutieren müsste.

Eine derart ideale Situation ist aber unrealistisch, und deshalb müssen wir uns mit Mechanismen beschäftigen, die es vielen Nutzern erlauben, mit denselben Daten zu arbeiten. Eines der Schlüsselelemente bei der Lösung dieser Aufgabe ist die Transaktion.

> Eine Transaktion ist eine Abfolge von Datenbankoperationen, die als eine Einheit ausgeführt werden.

In diesem Abschnitt sprechen wir über Transaktionen, mit denen sich mehrere SQL-Anweisungen zu einer Gruppe zusammenfassen lassen, sodass garantiert ist, dass entweder alle Anweisungen erfolgreich ausgeführt werden oder keine einzige davon.

## Prüf dich selbst

Was garantiert eine Transaktion?

1. Queries in einer Transaktion können nicht fehlschlagen — Eine Transaktion verhindert keine Fehler. Schlägt eine Query fehl, lässt sich die gesamte Transaktion zurücknehmen, damit in der Datenbank nicht nur ein Teil ihrer Änderungen übrig bleibt.

2. **Richtige Antwort:** Die Änderungen der Transaktion werden entweder alle oder gar nicht gespeichert — Eine Transaktion führt ihre Operationen als eine Einheit aus, deshalb bleibt in der Datenbank nie nur ein Teil ihrer Änderungen übrig.

3. Solange die Transaktion läuft, können andere Nutzer die Tabellen nicht lesen — Eine Transaktion sperrt von sich aus keine ganzen Tabellen. Wie andere Nutzer mit den Daten arbeiten, die eine Transaktion ändert, regeln Locks – darum geht es in der nächsten Lektion.
