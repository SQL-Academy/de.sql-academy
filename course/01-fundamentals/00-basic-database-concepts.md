---
meta:
    title: "Datenbanken und DBMS"
    description: "Definition von Datenbank und Datenbankmanagementsystem. Funktionsweise von Datenbanken. Ranking und Beliebtheit relationaler Datenbanken. Besonderheiten relationaler Datenbanken. Die Abfragesprache SQL."
---

# Datenbanken und DBMS

Bevor wir mit SQL loslegen, schauen wir uns die grundlegenden Begriffe rund um Datenbanken an. So verstehst du, wofür SQL gebraucht wird und in welcher Umgebung es läuft.

> Eine Datenbank ist eine Sammlung von Daten, die in strukturierter Form gespeichert sind.

Im Grunde ist das nur ein Speicher für irgendwelche Informationen – mehr nicht. Für sich genommen wären Datenbanken kaum interessant, wenn es nicht die Datenbankmanagementsysteme gäbe.

> Ein Datenbankmanagementsystem ist eine Sammlung von Sprachmitteln und Software, die
> auf Daten zugreift, sie erstellen, ändern und löschen kann und außerdem
> die Datensicherheit gewährleistet usw.

Einfacher gesagt: Ein DBMS ist ein System, mit dem du Datenbanken erstellst
und die darin enthaltenen Informationen verwaltest.

So sieht das Zusammenspiel mit einer Datenbank im einfachsten Fall aus:

![Schema der Arbeit mit einer Datenbank](https://sql-academy.org/static/guidePage/basic-database-concepts/de_schema_of_db_work.png "title")

## DBMS-Ranking

Aktuell sieht das Ranking der Datenbankmanagementsysteme so aus:

- `Oracle` – relationales DBMS
- `MySQL` – relationales DBMS
- `Microsoft SQL Server` – relationales DBMS
- `PostgreSQL` – relationales DBMS
- `MongoDB` – dokumentenorientiertes DBMS
- `Redis` – Key-Value-Store
- `Snowflake` – cloud-basiertes relationales DBMS
- `Elasticsearch` – Suchmaschine
- `IBM Db2` – relationales DBMS
- `SQLite` – relationales DBMS

Fällt dir auf, dass 7 von 10 der beliebtesten DBMS relational sind? Du hast also genau das Richtige zum Lernen ausgewählt 😉.

Lass uns prüfen, ob du das Thema verstanden hast. Wähle die richtige Aussage:

1. Ein DBMS interagiert in keiner Weise mit der Datenbank — Datenbankmanagementsysteme (DBMS) ermöglichen es, Datenbanken zu verwalten, zu ändern und zu löschen. Das DBMS interagiert also sehr wohl mit der Datenbank – das Schema oben zeigt genau dieses Zusammenspiel.

2. **Richtige Antwort:** Ein DBMS verwaltet die in der Datenbank gespeicherten Daten — Eine Datenbank ist nur eine Sammlung von Daten. Um sie zu verwalten, braucht man tatsächlich ein Datenbankmanagementsystem.

3. Für die Arbeit mit allen DBMS kann man SQL verwenden — Es gibt unterschiedliche DBMS und nicht alle verwenden SQL. SQL ist der Standard für relationale DBMS.
