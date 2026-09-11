---
meta:
    title: "Sortierung innerhalb des Fensters"
    description: "Sortierung innerhalb des Fensters ist eine wichtige Eigenschaft von Window Functions in SQL. Lerne, wie du sie nutzt, um Daten in einem Fenster zu ordnen und genauere Aggregat-Berechnungen zu erhalten. Mit Beispiel und Erklärung der Besonderheiten ohne explizite Fensterrahmen."
---

# Sortierung innerhalb des Fensters

In den vorigen Artikeln haben wir uns Window Functions und Partitionierung in SQL angeschaut.
Jetzt widmen wir uns einer weiteren wichtigen Eigenschaft von Window Functions — der Sortierung innerhalb des Fensters.

## Wozu eine Sortierung im Fenster?

Sortierung in Window Functions ist der Schlüssel zu fortgeschrittener Datenanalyse.
Sie erlaubt es, Daten innerhalb einer Gruppe oder eines Fensters anzuordnen
und so genauere und zielgerichtete Aggregat-Berechnungen durchzuführen.
Besonders nützlich ist das bei Zeitreihen, wo die Reihenfolge der Ereignisse zählt, oder beim Ranking innerhalb von Gruppen.

## Beispiel

### Aufgabenstellung

Angenommen, wir wollen Buchungsdaten von Unterkünften analysieren,
um herauszufinden, wie sich die Gesamtausgaben für Mieten pro Nutzer im Lauf der Zeit entwickelt haben.

Anfangs haben wir folgende Auswahl an Buchungen:

![Verwendung der Window Function ohne Sortierung](https://sql-academy.org/static/guidePage/sorting-in-windows-functions/initial-data_de.png "Verwendung der Window Function ohne Sortierung")

Als Ergebnis möchten wir die Veränderung der Ausgaben pro Nutzer im Zeitverlauf sehen, also
eine Tabelle wie diese:

![Verwendung der Window Function mit Sortierung](https://sql-academy.org/static/guidePage/sorting-in-windows-functions/final-result.png "Verwendung der Window Function mit Sortierung")

#### Daten in Partitionen aufteilen

Um das gewünschte Ergebnis zu bekommen, müssen wir die Daten zuerst pro Nutzer in Partitionen aufteilen,
damit die Window Function für jeden Nutzer unabhängig arbeitet.
Für die Summenbildung nutzen wir die Window Function `SUM`.

```sql
SELECT user_id,
       start_date,
       total AS reservation_price,
       SUM(total) OVER (
            PARTITION BY user_id
       ) AS total_expenses
FROM Reservations;
```

![Zwischenergebnis](https://sql-academy.org/static/guidePage/sorting-in-windows-functions/result-1.png "Zwischenergebnis")

In der Spalte `total_expenses` steht jetzt die Gesamtausgabensumme pro Nutzer. Aber das ist noch nicht ganz, was wir wollen: Die Daten in der Tabelle sind nicht nach Datum geordnet, und
wir sehen nicht, wie die Gesamtausgaben über die Zeit gewachsen sind — nur den Endwert.

Um zum gewünschten Ergebnis zu kommen, müssen wir die Query um eine Sortierung nach dem Buchungsstart-Datum erweitern:

```sql
SELECT user_id,
       start_date,
       total AS reservation_price,
       SUM(total) OVER (
           PARTITION BY user_id
           ORDER BY start_date
       ) AS cumulative_total
FROM Reservations;
```

![Endergebnis](https://sql-academy.org/static/guidePage/sorting-in-windows-functions/final-result.png "Endergebnis")

Genau das wollten wir. Aber was hat sich durch `ORDER BY start_date` geändert?

1. Innerhalb einer Partition sind die Daten jetzt nach dem Buchungsstart-Datum sortiert.
2. Die Berechnung der Gesamtsumme funktioniert anders: Sie wird innerhalb der Partition jetzt kumulativ aufsummiert,
   statt als Endsumme ausgegeben. Das hängt mit einer Besonderheit zusammen, wenn `ORDER BY` ohne explizites `ROWS|RANGE`
   im `OVER`-Ausdruck verwendet wird. Schauen wir uns das genauer an.

### Besonderheiten beim Einsatz von Sortierung ohne Fensterrahmen

Wie wir wissen, sieht die vollständige Syntax einer Window Function so aus:

```sql
SELECT <window_function>(<tabellen_feld>)
OVER (
      [PARTITION BY <spalten_zum_partitionieren>]
      [ORDER BY <spalten_zum_sortieren>]
      [ROWS|RANGE <definition_des_zeilen_bereichs>]
)
```

Neben `PARTITION BY` und `ORDER BY` gibt es darin den optionalen Block `ROWS|RANGE`. Den schauen wir uns
im nächsten Artikel im Detail an, jetzt ist erst einmal wichtig, wozu er dient. Er erlaubt es, einen Fensterrahmen relativ zur aktuellen Zeile festzulegen,
der für die Berechnung in der Window Function verwendet wird.

Du kannst zum Beispiel festlegen, dass bei der Berechnung der Werte
für die aktuelle Zeile nicht alle Einträge der Partition in die Window Function einfließen,
sondern nur `N` Zeilen davor und `N` Zeilen danach.

Wenn du `ORDER BY` benutzt und im `ROWS|RANGE`-Block nichts angibst, wird in der Window Function
automatisch die Regel `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` angewendet.
Das bedeutet: Das Fenster beginnt bei der ersten Zeile und
endet bei der aktuellen Zeile.

Die Werte für die Spalte `cumulative_total` werden also so berechnet:

![Erklärung, wie die Sortierung wirkt](https://sql-academy.org/static/guidePage/sorting-in-windows-functions/explanation.png "Erklärung, wie die Sortierung wirkt")

Mehr zu Fensterrahmen und ihrer Definition liest du im nächsten Artikel.
