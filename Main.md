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

```
ORDER BY name ASC/DESC
```

### Joins
ohne WHERE Bedingung entsteht ein Kreuzprodukt
```
SELECT *
FROM aufkopf AS a, aufpos AS p
WHERE a.aufnr=p.aufnr
```

```
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





Was ist die Normalform? Was steckt dahinter? Was ist der Nutzen?
