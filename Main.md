# Geodatenbanken SU1

## Was ist SQL?
Bei mehreren Anwendungen mit verschiedenen Datenbanken treten Datenbankenprobleme auf, da Inhalte hin und her kopiert werden und sich somit u.a. Up-to-date Probleme einschleichen

Datenbank = Heterogener Speicher von allen Daten

Datenbankmanagementsystem (DBMS) verwaltet die Daten aller Anwendungen

SQL = SEQUEL Strukturierte Abfragesprache

Abfragesprache = read, write data

Programmiersprache = um Schleifen, etc. erweiterbar

&nbsp;

DML = lesen, schreiben, löschen

DCL = Rechte vergeben

DDL = 

Definition von Dateiorganisationsformen = Indexierung

ESQL = Einbettung in andere Programmiersprachen

### Wildcards in SQL
% = beliebig viele (* in MS Access)
_ = genau eins

### Sortieren
ASC = ascending (aufwärts: A-Z, muss nicht hingeschrieben werden)

DESC = descending (abwärts: Z-A)

```sql
ORDER BY name ASC/DESC
```

### Joins
ohne WHERE Bedingung entsteht ein Kreuzprodukt
```sql
SELECT *
FROM aufkopf AS a, aufpos AS p
WHERE a.aufnr=p.aufnr
```

```sql
SELECT *
FROM aufkopf AS a, aufpos AS p,kdst
WHERE a.aufnr=p.aufnr
    AND kdst.kdnr=a.kdnr
```

### Alle Datensatzfilter
SELECT [DISTINCT] [SUM]
FROM
WHERE
GROUP BY
HAVING SUM(umssoll) [Boolischer Ausdruck]
ORDER BY

## MS Access
[Datenbank](https://github.com/s92854/Geodatenbanken/files/12866750/mat_inf.zip) in Access laden > Erstellen > Abfragentwurf > Ansicht > SQL Ansicht > bspw.: ```SELECT* FROM artst;``` > wieder Ansicht auf Datenblattansicht wechseln

## Training

1. Um bspw. nach Artikeln zu filtern, deren Bestand negativ ist, kann man so filtern:
```SQL
SELECT artbez,bestand
FROM artst
WHERE bestand<0
```

2. Alle Kunden ausgeben, die AG haben:
```SQL
SELECT *
FROM kdst
WHERE Firma LIKE "*AG"
```

3. Artikelnummer, Artikelbezeichnung, Einkaufs- und Verkaufspreis ausgegeben, nach Artikelbezeichnungen in alphabetischer Reihenfolge ordnen:
```SQL
SELECT artnr,artbez,ekpreis,vpreis
FROM artst
ORDER BY artbez ASC
```

Es ist auch möglich zwei ORDER BY zu definieren. Sollte ein WHERE definiert werden, muss die Reihenfolge eingehalten werden:
```SQL
SELECT artnr,artbez,ekpreis,vpreis
FROM artst
WHERE artnr < 100
ORDER BY artbez ASC, ekpreis DESC
```

4.  Kundennummer, die Firmenbezeichnung und die komplette Adresse, zunächst nach Orten sortiert, gleiche Orte eine nach der PLZ absteigende Sortierung:
```SQL
SELECT kdnr,firma,plz,ort,straße,
FROM kdst
WHERE artnr < 100
ORDER BY ort ASC, plz DESC
```

<img width="371" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/9541d433-8e66-4a50-8290-9dce95928451">


```SQL
SELECT DISTINCT ort,SUM(umssoll)
FROM kdst
GROUP BY ort
ORDER BY ort;
```

```SQL
SELECT ort,SUM(umssoll)
FROM kdst
GROUP BY ort
HAVING SUM(umssoll) >= 30000
```


## Phasen von Datenbanken
Logischer Entwurf: Überlegung welches Datenbankenmodell, Postgres/MySQL/etc.
Datendefinitionen: Erstellung und Nutzung der Datenbank
Physischer Entwurf: Indexierung, Verschnellerung der Datenbank

Auflösen von n-zu-m-Beziehungen: Hinzufügen einer weiteren Entität


Syntaktisch: Syntaxfehler an einer Stelle
Symantisch: Sinn/ Logik stimmt nicht


## Nornalformlehre
### 1. Normalform
### 2. Normalform
* voll-funktional (umfasst alle) und funktional abhängig


#### Übungsbeispiel

|AlbumNr|Album|Titelliste|
|---|---|---|
|100|Die Ärzte - Jazz ist anders|{1. Himmelblau,2. Lied vom Scheitern,3.Breit}|
|101|DJ Sammy- Heaven|{1. California Dreamin, 2. Heaven, 3. Sunlight}|

1. Normalform:

Albumtitel(<u>AlbumNr</u>,Interpret,Albumname,Titelname,<u>TitelNr</u>)

|AlbumNr|Interpret|Albumname|Titelname|Liednummer|
|---|---|---|---|---|
|100|Die Ärzte|Jazz ist anders|Himmelblau|1|
|100|Die Ärzte|Jazz ist anders|Lied vom Scheitern|2|
|100|Die Ärzte|Jazz ist anders|Breit|3|
|101|DJ Sammy|Heaven|California Dreamin|1|
|101|DJ Sammy|Heaven|Heaven|2|
|101|DJ Sammy|Heaven|Sunlight|3|

2. Normalform:

Album(<u>AlbumNr</u>,Albumname,Interpret) oder Album(<u>AlbumNr</u>,InterpretNr<sub>2</sub>,Albumname)(Interpret,Gebdatum,<u>InterpretNr</u>)
Titel(TitelNr,TitelName)

3. Normalform


## User defined Collection Types
* definieren statische Struktur, nicht Verhalten
* stored procedure (user defined function)
    * Unterprogramm, welches in der DB gespeichert und dort direkt ausgeführt wird
    * Verlagerung von mehreren Datenbankverarbeitungsschritten in der DB
    * Vermeidung von aufwändigem Datentransfer
    * Entlastung des Anwendungsprogramms (Clients)
    * Belastung des Datenbanksystems
> bei großen DB-System können mehrere TB RAM benötigt werden

### Einsatzgebiete
* Transformationen beim Lesen/Schreiben
* Konvertierung der Daten in ein bestimmtes Format
* Bearbeitung der Daten nach komplexen Algorithmen
* Gruppierung der Daten nach anderen Anforderungen
* Ereignisgesteuerte Manipulation oder Überwachung von Daten

### Collection Types
```sql
CREATE TYPE zahlen AS VARRAY(5) OF integer; –- Oracle
```

> erstellt einen neuen Type namens zahlen des typs int als Array

```sql
CREATE TABLE lotto (zahlen int[]); --PostgreSQL
```

> liefert ein Array des Typs int zurück

```sql
INSERT INTO lotto VALUES (ARRAY[1, 10, 23, 35, 4, 9]);
```

> fügt die Werte in ARRAY[] in zahlen ein

### Distinct-Types
Verhindern, dass verschiedene Types miteinander kombiniert werden



### Bestandteile UDF-Header
* Verhalten der Funktion (u.a. wichtig für Optimizer)
    * IMMUTABLE → liefert immer das gleiche Ergebnis bei gleichen Parametern, verändert nicht die DB und liest nicht aus der DB
    * STABLE → liefert das gleiche Ergebnis bei gleichen Parametern innerhalb eines Table Scans, verändert nicht die DB, aber liest aus der DB
    * VOLATILE (standard) → das Ergebnis kann jederzeit verschieden sein (z.B. random()), Funktion kann weiterhin Seiteneffekte aufweisen
* Verhalten bei NULL Input
    * CALLED ON NULL INPUT (Standard)
    * RETURNS NULL ON NULL INPUT (aka STRICT)
* Ausführungsprivileg – Welche Privilegien gelten während der Ausführung der Funktion
    * SECURITY INVOKER → Privilegien des Funktionsaufrufers
    * SECURITY DEFINER → Privilegien vom Ersteller (Owner)
    * Zusammenfassung: Habe ich Admin- oder meine Rechte?
* Deklaration im DECLARE Block
    * _var INT;
* Initialisierung
    * während der Deklaration im DECLARE
        * _var INT := 3;
        * _var INT DEFAULT 3;
    * Erste Zuweisung im BEGIN
* Zuweisung
    * SET _var := 3;
    * SELECT a INTO _var;
* Funktion beenden
    * RETURN → verläßt den Block bzw. die Funktion (void)
    * RETURN value → gibt einen definierten Wert zurück
    * RETURN NEXT → innerhalb der Cursor-Schleife liefert es die aktuelle Zeile an dem der Cursor steht
    * RETURN QUERY → gibt ein komplettes Resultset der Abfrage zurück



Was ist die Normalform? Was steckt dahinter? Was ist der Nutzen?
