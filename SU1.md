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
[Datenbank](https://github.com/s92854/Geodatenbanken/files/12866750/mat_inf.zip) in Access laden > Erstellen > Abfragentwurf > Ansicht > SQL Ansicht > bspw.: ```SELECT*FROM artst;``` > wieder Ansicht auf Datenblattansicht wechseln

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
```
SELECT artnr,artbez,ekpreis,vpreis
FROM artst
WHERE artnr < 100
ORDER BY artbez ASC, ekpreis DESC
```

4.  Kundennummer, die Firmenbezeichnung und die komplette Adresse, zunächst nach Orten sortiert, gleiche Orte eine nach der PLZ absteigende Sortierung:
```
SELECT kdnr,firma,plz,ort,straße,
FROM kdst
WHERE artnr < 100
ORDER BY ort ASC, plz DESC
```

<img width="371" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/9541d433-8e66-4a50-8290-9dce95928451">


```
SELECT DISTINCT ort,SUM(umssoll)
FROM kdst
GROUP BY ort
ORDER BY ort;

```

```

SELECT ort,SUM(umssoll)
FROM kdst
GROUP BY ort
HAVING SUM(umssoll) >= 30000
```

## Übung 1

1. Auftragsnummer, Kundennummer, Lieferschein- und Rechnungsdatum für Datum Lieferschein nach "01.12.2004":
```
SELECT aufnr,kdnr,ls_datum,rg_datum
FROM aufkopf
WHERE ls_datum > #12/01/2004#
```

2. Namen welcher Vertreter enden auf „mann“?
```
SELECT *
FROM vert
WHERE name LIKE "*mann"
```

3. Vertreter mit Provisionssatz von 3,3%, 3,7% oder 5,4%:
```
SELECT name,vorname,prov
FROM vert
WHERE prov = 3.3 OR prov = 3.7 OR prov = 5.4
```

4. Alle Infos über alle Artikel:
```
SELECT ARTNR,ARTBEZ,EKPREIS,VPREIS,MGEHT,GRUPPE,KW,BESTAND
FROM artst
```

5. Über alle Artikel Bezeichnung, Einkaufs- und Verkaufspreis und der aktuelle (Lager-)Bestand:
```
SELECT artbez,ekpreis,vpreis,bestand
FROM artst
```

6. Welche Berliner Stadtteile sind Vertreter?
```
SELECT stadtteil
FROM vert
GROUP BY stadtteil
```

7. Von allen Kunden Kundennummer, Firmenbezeichnung, Umsatzsoll und -haben
```
SELECT kdnr,firma,umssoll,umshaben
FROM kdst
```

8. Alle Artikel < 10: Artikelnummer, Bezeichnung, Bestand und Mengeneinheit; keine negativen Bestände
```
SELECT bestand, artnr,artbez,mgeht
FROM artst
WHERE bestand < 10 AND bestand >= 0
```

9. Alle Kunden mit ausgeglichenem Saldo: Kundennummer, Firma, Anschrift und Umsatzsoll
```
SELECT kdnr,firma,strasse + ', '+ plz + ', ' + ort AS adresse,umssoll
FROM kdst
WHERE saldo = 0
```
