# Übung 1

1. Auftragsnummer, Kundennummer, Lieferschein- und Rechnungsdatum für Datum Lieferschein nach "01.12.2004":
```SQL
SELECT aufnr,kdnr,ls_datum,rg_datum
FROM aufkopf
WHERE ls_datum > #12/01/2004#
```

<img width="287" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/37dd43ca-5ee5-450c-a5e5-19b3a02d6d86">

2. Namen welcher Vertreter enden auf „mann“?
```SQL
SELECT *
FROM vert
WHERE name LIKE '%mann'
```
> '*mann' in Access

<img width="704" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/62e4fbd5-f0b6-42aa-8158-9a4ff79c400d">

3. Vertreter mit Provisionssatz von 3,3%, 3,7% oder 5,4%:
```SQL
SELECT name,vorname,prov
FROM vert
WHERE prov = 3.3 OR prov = 3.7 OR prov = 5.4
```

<img width="275" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/0a32db95-e0a1-4b10-9e83-a679fcc949fb">

4. Alle Infos über alle Artikel:
```SQL
SELECT ARTNR,ARTBEZ,EKPREIS,VPREIS,MGEHT,GRUPPE,KW,BESTAND
FROM artst
```

<img width="537" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/eb07a837-abad-4022-afc2-5b85c71aa017">

5. Über alle Artikel Bezeichnung, Einkaufs- und Verkaufspreis und der aktuelle (Lager-)Bestand:
```SQL
SELECT artbez,ekpreis,vpreis,bestand
FROM artst
```

<img width="297" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/fb7ed5f1-dded-46b5-9d2d-f0cee6f756b7">

6. Welche Berliner Stadtteile sind Vertreter?
```SQL
SELECT stadtteil
FROM vert
GROUP BY stadtteil
```

<img width="121" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/d1511267-3337-4206-bf14-82a12011b4eb">

7. Von allen Kunden Kundennummer, Firmenbezeichnung, Umsatzsoll und -haben
```SQL
SELECT kdnr,firma,umssoll,umshaben
FROM kdst
```

<img width="314" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/c4f3a627-0cb7-4f0b-bea4-a36d6a2ed8fd">

8. Alle Artikel < 10: Artikelnummer, Bezeichnung, Bestand und Mengeneinheit; keine negativen Bestände
```SQL
SELECT bestand, artnr,artbez,mgeht
FROM artst
WHERE bestand < 10 AND bestand >= 0
```

<img width="317" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/93312cc0-40b3-4932-b797-3486d0ecf3fb">

9. Alle Kunden mit ausgeglichenem Saldo: Kundennummer, Firma, Anschrift und Umsatzsoll
```SQL
SELECT kdnr,firma,strasse || ', '|| plz || ', ' || ort AS adresse,umssoll
FROM kdst
WHERE saldo = 0
```
> in Access kann statt '||' der Operator '+' verwendet werden

<img width="341" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/d1b8f5f6-b291-43e0-9c05-9cb444e22553">

10. Alle Artikel mit positivem Lagerbestand & VP > 50€: Artikelnummer, Bezeichnung, Bestand, Verkaufs- und Einkaufspreis
```SQL
SELECT artnr,artbez,bestand,vpreis,ekpreis
FROM artst
WHERE vpreis > 50 AND bestand > 0
```

<img width="357" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/e3a61c99-09cf-406f-bd16-c1d4ebae1ac1">

11. Vertreter mit Provision über 3,5%: Name & Anschrift (separate Spalten)
```SQL
SELECT name || ', ' || vorname AS Name, strasse || ', ' || plz || ', ' || ort || ', ' || stadtteil AS Anschrift
FROM vert
WHERE prov > 3.5
```

<img width="282" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/b20558f1-2b0e-456a-8f5e-4a59f997ce9f">

12.  alle Artikel Artikelnummer, Bezeichnung, Bestand, Verkaufs- und Einkaufspreis, Handelsspanne (Differenz aus Einkaufs- und Verkaufspreis) absolut und prozentual (auf den Verkaufspreis bezogen, 2 Nachkommastellen)
```SQL
SELECT artnr,artbez,bestand,vpreis,ekpreis,vpreis-ekpreis AS "Handelsspanne Absolut", ROUND(((vpreis-ekpreis)/vpreis)*100,2) AS "Handelsspanne Prozent"
FROM artst
```

<img width="551" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/5bcc1901-7bd2-44c7-bf99-f72f7c1cd083">

13. Artikel, deren Handelsspanne größer als 8%: Artikelnummer, Bezeichnung und Handelsspanne
```SQL
SELECT artnr,artbez,bestand,vpreis,ekpreis, ROUND(((vpreis-ekpreis)/vpreis)*100,2) AS "Handelsspanne"
FROM artst
WHERE ((vpreis - ekpreis) / vpreis) * 100 > 8.0;
```

<img width="427" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/e8a558f2-3d0d-48e4-a451-13d6ce2f6353">

14. Kunden, deren Umsatzsoll größer als 5.000 EUR: Kundennummer, Firmenbezeichnung, Vertreter, Saldo
```SQL
SELECT kdnr,firma,vertreter,saldo
FROM kdst
WHERE umssoll > 5000
```

<img width="293" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/833d8e0c-9ba9-4b8e-aa6f-048e5286cac7">

15. Wert des niedrigsten, des höchsten und des durchschnittlichen Provisionssatzes. Durch textuelle Beschreibung kenntlich machen.
```SQL
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
```SQL
SELECT *
FROM kdgru
```

<img width="179" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1577373c-6d1e-43d5-946c-7e2e17c285e8">

17. Welche der Artikel sind “hamserver”?
```SQL
SELECT *
FROM artst
WHERE artbez LIKE 'hamserver%'
```
> falls lediglich die Namen der hamserver gefragt sind: SELECT artbez

<img width="537" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/74d7978f-f921-4dee-b9a1-a203e1532d83">

18. Artikel mit Vorzugspreis (10% niedriger als Verkaufspreis) unter 30 und über 1000€: Artikelnummer, Artikelbezeichnung, Vorzugspreis.
```SQL
SELECT artnr,artbez,vpreis * 0.9 AS "Vorzugspreis"
FROM artst
WHERE vpreis * 0.9 > 1000 OR vpreis * 0.9 < 30
```

<img width="246" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/50d259a6-112e-424f-beb4-5d9b6b7c30f4">

19. Alle Artikel deren Einkaufspreis Vielfaches von 25€: Artikelnummer, Artikelbezeichnung und Einkaufspreis
```SQL
SELECT artnr,artbez,ekpreis
FROM artst
WHERE ekpreis % 25 = 0
```

<img width="242" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/3ce82289-47e3-4426-bc50-f4e67e3e4f7c">

20. Zu welchen unterschiedlichen Kundengruppen sind Kunden des Unternehmens zusammengefasst?

**Lösung 1 - simple Auflistung der Kundengruppen:**
```SQL
SELECT grup_txt
FROM kdgru
```

<img width="124" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1651b97d-0212-42f5-baea-c3d7a3626942">

**Lösung 2 - Join:**
```SQL
SELECT kdst.firma,kdgru.grup_txt
FROM kdst
INNER JOIN kdgru ON kdst.kdgruppe = kdgru.kdgruppe
```

<img width="221" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/1886ca05-2511-4018-be96-f5f2850e12ff">

21. Welche Kunden werden vom Vertreter mit Vertreternummer "7" betreut?
```SQL
SELECT kdnr,firma,vertreter
FROM kdst
WHERE vertreter = 7
```

<img width="227" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/c5647e93-97e3-4b68-afe0-d8882d0f0fc3">

**Lösung mit einem Join, bei dem der Vertretername mit aufgelistet wird:**

```SQL
SELECT kdnr,firma,kdst.vertreter,vert.vorname || ' ' || vert.name AS "Vertretername"
FROM kdst
INNER JOIN vert ON kdst.vertreter = vert.vertreter
WHERE kdst.vertreter = 7
```

<img width="294" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/f5408739-e49d-4f46-b481-253bf1f0bd6b">

22. Summen vom Einkaufspreis, Verkaufspreis, Vorzugspreis, Handelsspanne in €.
```SQL
SELECT SUM(ekpreis) AS "Einkaufspreis gesamt", SUM(vpreis) AS "Verkaufspreis gesamt", SUM(vpreis * 0.9) AS "Vorzugspreis gesamt", SUM(ROUND(((vpreis-ekpreis)/vpreis)*100,2)) AS "Handelsspanne gesamt"
FROM artst
```

<img width="398" alt="image" src="https://github.com/s92854/Geodatenbanken/assets/134683810/456b5dea-9285-4bde-bc15-9a19a41f04be">
