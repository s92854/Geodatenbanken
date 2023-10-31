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

<img width="287" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/37dd43ca-5ee5-450c-a5e5-19b3a02d6d86">

2. Namen welcher Vertreter enden auf „mann“?
```
SELECT *
FROM vert
WHERE name LIKE '%mann'
```
> '*mann' in Access

<img width="704" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/62e4fbd5-f0b6-42aa-8158-9a4ff79c400d">

3. Vertreter mit Provisionssatz von 3,3%, 3,7% oder 5,4%:
```
SELECT name,vorname,prov
FROM vert
WHERE prov = 3.3 OR prov = 3.7 OR prov = 5.4
```

<img width="275" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/0a32db95-e0a1-4b10-9e83-a679fcc949fb">

4. Alle Infos über alle Artikel:
```
SELECT ARTNR,ARTBEZ,EKPREIS,VPREIS,MGEHT,GRUPPE,KW,BESTAND
FROM artst
```

<img width="537" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/eb07a837-abad-4022-afc2-5b85c71aa017">

5. Über alle Artikel Bezeichnung, Einkaufs- und Verkaufspreis und der aktuelle (Lager-)Bestand:
```
SELECT artbez,ekpreis,vpreis,bestand
FROM artst
```

<img width="297" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/fb7ed5f1-dded-46b5-9d2d-f0cee6f756b7">

6. Welche Berliner Stadtteile sind Vertreter?
```
SELECT stadtteil
FROM vert
GROUP BY stadtteil
```

<img width="121" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/d1511267-3337-4206-bf14-82a12011b4eb">

7. Von allen Kunden Kundennummer, Firmenbezeichnung, Umsatzsoll und -haben
```
SELECT kdnr,firma,umssoll,umshaben
FROM kdst
```

<img width="314" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/c4f3a627-0cb7-4f0b-bea4-a36d6a2ed8fd">

8. Alle Artikel < 10: Artikelnummer, Bezeichnung, Bestand und Mengeneinheit; keine negativen Bestände
```
SELECT bestand, artnr,artbez,mgeht
FROM artst
WHERE bestand < 10 AND bestand >= 0
```

<img width="317" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/93312cc0-40b3-4932-b797-3486d0ecf3fb">

9. Alle Kunden mit ausgeglichenem Saldo: Kundennummer, Firma, Anschrift und Umsatzsoll
```
SELECT kdnr,firma,strasse || ', '|| plz || ', ' || ort AS adresse,umssoll
FROM kdst
WHERE saldo = 0
```
> in Access kann statt '||' der Operator '+' verwendet werden

<img width="341" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/d1b8f5f6-b291-43e0-9c05-9cb444e22553">

10. Alle Artikel mit positivem Lagerbestand & VP > 50€: Artikelnummer, Bezeichnung, Bestand, Verkaufs- und Einkaufspreis
```
SELECT artnr,artbez,bestand,vpreis,ekpreis
FROM artst
WHERE vpreis > 50 AND bestand > 0
```

<img width="357" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/e3a61c99-09cf-406f-bd16-c1d4ebae1ac1">

11. Vertreter mit Provision über 3,5%: Name & Anschrift (separate Spalten)
```
SELECT name || ', ' || vorname AS Name, strasse || ', ' || plz || ', ' || ort || ', ' || stadtteil AS Anschrift
FROM vert
WHERE prov > 3.5
```

<img width="282" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/b20558f1-2b0e-456a-8f5e-4a59f997ce9f">

12.  alle Artikel Artikelnummer, Bezeichnung, Bestand, Verkaufs- und Einkaufspreis, Handelsspanne (Differenz aus Einkaufs- und Verkaufspreis) absolut und prozentual (auf den Verkaufspreis bezogen, 2 Nachkommastellen)
```
SELECT artnr,artbez,bestand,vpreis,ekpreis,vpreis-ekpreis AS "Handelsspanne Absolut", ROUND(((vpreis-ekpreis)/vpreis)*100,2) AS "Handelsspanne Prozent"
FROM artst
```

<img width="551" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/5bcc1901-7bd2-44c7-bf99-f72f7c1cd083">

13. Artikel, deren Handelsspanne größer als 8%: Artikelnummer, Bezeichnung und Handelsspanne
```
SELECT artnr,artbez,bestand,vpreis,ekpreis, ROUND(((vpreis-ekpreis)/vpreis)*100,2) AS "Handelsspanne"
FROM artst
WHERE ((vpreis - ekpreis) / vpreis) * 100 > 8.0;
```

<img width="427" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/e8a558f2-3d0d-48e4-a451-13d6ce2f6353">

14. Kunden, deren Umsatzsoll größer als 5.000 EUR: Kundennummer, Firmenbezeichnung, Vertreter, Saldo
```
SELECT kdnr,firma,vertreter,saldo
FROM kdst
WHERE umssoll > 5000
```

<img width="293" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/833d8e0c-9ba9-4b8e-aa6f-048e5286cac7">

15. Wert des niedrigsten, des höchsten und des durchschnittlichen Provisionssatzes. Durch textuelle Beschreibung kenntlich machen.
```
SELECT 'Niedrigster Provisionssatz' AS Beschreibung, MIN(prov) AS Wert
FROM vert
UNION ALL
SELECT 'Höchster Provisionssatz' AS Beschreibung, MAX(prov) AS Wert
FROM vert
UNION ALL
SELECT 'Durchschnittlicher Provisionssatz' AS Beschreibung, ROUND(AVG(prov),2) AS Wert
FROM vert
```

<img width="199" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/09a2c22e-f27a-43ed-a8ca-be0f8ba5f726">

16. Welche Kundengruppen gibt es? Mit Gruppennummer und beschreibendem Text.
```
SELECT *
FROM kdgru
```

<img width="179" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1577373c-6d1e-43d5-946c-7e2e17c285e8">

17. Welche der Artikel sind “hamserver”?
```
SELECT *
FROM artst
WHERE artbez LIKE 'hamserver%'
```
> falls lediglich die Namen der hamserver gefragt sind: SELECT artbez

<img width="537" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/74d7978f-f921-4dee-b9a1-a203e1532d83">

18. Artikel mit Vorzugspreis (10% niedriger als Verkaufspreis) unter 30 und über 1000€: Artikelnummer, Artikelbezeichnung, Vorzugspreis.
```
SELECT artnr,artbez,vpreis * 0.9 AS "Vorzugspreis"
FROM artst
WHERE vpreis * 0.9 > 1000 OR vpreis * 0.9 < 30
```

<img width="246" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/50d259a6-112e-424f-beb4-5d9b6b7c30f4">

19. Alle Artikel deren Einkaufspreis Vielfaches von 25€: Artikelnummer, Artikelbezeichnung und Einkaufspreis
```
SELECT artnr,artbez,ekpreis
FROM artst
WHERE ekpreis % 25 = 0
```

<img width="242" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/3ce82289-47e3-4426-bc50-f4e67e3e4f7c">

20. Zu welchen unterschiedlichen Kundengruppen sind Kunden des Unternehmens zusammengefasst?

**Lösung 1 - simple Auflistung der Kundengruppen:**
```
SELECT grup_txt
FROM kdgru
```

<img width="124" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1651b97d-0212-42f5-baea-c3d7a3626942">

**Lösung 2 - Join:**
```
SELECT kdst.firma,kdgru.grup_txt
FROM kdst
INNER JOIN kdgru ON kdst.kdgruppe = kdgru.kdgruppe
```

<img width="221" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1886ca05-2511-4018-be96-f5f2850e12ff">

21. Welche Kunden werden vom Vertreter mit Vertreternummer "7" betreut?
```
SELECT kdnr,firma,vertreter
FROM kdst
WHERE vertreter = 7
```

<img width="227" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/c5647e93-97e3-4b68-afe0-d8882d0f0fc3">

**Lösung mit einem Join, bei dem der Vertretername mit aufgelistet wird:**

```
SELECT kdnr,firma,kdst.vertreter,vert.vorname || ' ' || vert.name AS "Vertretername"
FROM kdst
INNER JOIN vert ON kdst.vertreter = vert.vertreter
WHERE kdst.vertreter = 7
```

<img width="294" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/f5408739-e49d-4f46-b481-253bf1f0bd6b">

22. Summen vom Einkaufspreis, Verkaufspreis, Vorzugspreis, Handelsspanne in €.
```
SELECT SUM(ekpreis) AS "Einkaufspreis gesamt", SUM(vpreis) AS "Verkaufspreis gesamt", SUM(vpreis * 0.9) AS "Vorzugspreis gesamt", SUM(ROUND(((vpreis-ekpreis)/vpreis)*100,2)) AS "Handelsspanne gesamt"
FROM artst
```

<img width="398" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/456b5dea-9285-4bde-bc15-9a19a41f04be">
