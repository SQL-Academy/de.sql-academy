---
meta:
    title: "Locks im DBMS: MySQL und PostgreSQL"
    description: "Locks in MySQL und PostgreSQL verstehen: wie verschiedene Lock-Typen den gleichzeitigen Zugriff auf Daten steuern und Transaktionen korrekt verarbeiten. Lerne die unterschiedlichen Granularitäten von Locks – von Tabellen bis Zeilen – und ihren Einfluss auf Performance und Datenkonsistenz in deiner Datenbank kennen."
---

# Locks im DBMS

Datenbankmanagementsysteme erlauben es einem Nutzer, Daten zu lesen und zu verändern.
In der heutigen Welt können aber Tausende Menschen gleichzeitig Änderungen an einer Datenbank vornehmen.
Wenn die Nutzer überwiegend Daten lesen, ist diese Last für den Datenbankserver kein großes Problem.
Schreiben oder ändern dagegen mehrere Nutzer Daten gleichzeitig, muss der Server deutlich
komplexere Aufgaben lösen.

Stell dir vor, du erstellst einen Finanzbericht, der die täglichen Verkäufe eines Ladens für eine Woche zusammenfasst.
Während du am Bericht arbeitest, passieren gleichzeitig folgende Aktionen:

- Ein Kunde kauft einen Artikel.
- Ein anderer Kunde gibt einen defekten Artikel zurück und bekommt sein Geld erstattet.
- Der Laden erhält eine neue Lieferung.

Während du also den Bericht zusammenstellst, ändern mehrere Nutzer die Daten in der Datenbank.
Welche Zahlen sollen am Ende im Bericht stehen?

Die Antwort hängt davon ab, wie dein Server mit Locks umgeht.

## Lock

Ein Lock ist ein Mechanismus, der den Zugriff auf Daten einschränkt, um eine korrekte Verarbeitung von Transaktionen sicherzustellen.

Datenbankserver setzen Locks ein, um den gleichzeitigen Zugriff auf Daten zu steuern: Solange eine Transaktion mit Daten arbeitet,
können andere Transaktionen diese nicht verändern.

Wenn Daten in der Datenbank gesperrt sind, müssen andere Nutzer, die dieselben Daten lesen oder ändern wollen,
warten, bis der Lock wieder aufgehoben ist.

### Granularität von Locks

Es gibt mehrere Strategien, wie genau eine Ressource gesperrt werden kann.
Der Server kann auf einer von drei Ebenen – Granularitäten – sperren.

- **Tabellen-Lock.** Während die Änderung läuft, ist die ganze Tabelle für alle anderen gesperrt
- **Page-Lock.** Warten muss nur, wer dasselbe Speichersegment trifft — 2 bis 16 KByte
- **Row-Lock.** Warten muss nur, wer genau dieselbe Zeile ändert

Diese Ansätze haben jeweils Vor- und Nachteile.
Eine ganze Tabelle zu sperren ist schnell erledigt, kann aber bei vielen Nutzern zu langen Wartezeiten führen.
Row-Locks erfordern mehr Verwaltungsaufwand,
erlauben dafür aber mehreren Nutzern, gleichzeitig dieselbe Tabelle zu ändern,
solange sie an unterschiedlichen Zeilen arbeiten.

**MySQL**

MySQL kann je nach gewählter Storage Engine Tabellen-, Page- oder Row-Locks verwenden.
Standardmäßig setzt MySQL die Storage Engine InnoDB ein, die Row-Locks unterstützt.

**PostgreSQL**

PostgreSQL nutzt Multiversion Concurrency Control (MVCC) und arbeitet standardmäßig mit Row-Locks.

Bevor wir im nächsten Artikel zum Thema Transaktionen weitergehen, prüfen wir kurz, wie du diese Lektion verstanden hast.

Wofür werden Locks im DBMS gebraucht?

1. **Richtige Antwort:** Locks im DBMS dienen dazu, den gleichzeitigen Zugriff auf Datenressourcen zu steuern. — Der Hauptzweck von Locks in Datenbanken ist, Zugriffskonflikte zu verhindern und die Datenintegrität zu sichern, indem der Zugriff zeitweise gesperrt wird.

2. Locks im DBMS dienen dazu, den Datenbankserver schneller zu machen. — Diese Antwort ist falsch, weil Locks beim Zugriff auf Daten eine gewisse Verzögerung verursachen und ihn nicht beschleunigen. Sie verhindern Konflikte und sichern die Datenintegrität, was in Einzelfällen zu Verzögerungen bei der Verarbeitung von Queries führen kann.

3. Locks im DBMS dienen dazu, viele SQL-Anweisungen als eine logische Operation auszuführen. — Diese Antwort ist falsch, weil Locks nicht zwingend damit zusammenhängen, mehrere SQL-Anweisungen als eine Operation auszuführen. Sie sorgen vielmehr für konsistenten Zugriff auf Daten, wenn diese gleichzeitig von mehreren Nutzern geändert werden.
