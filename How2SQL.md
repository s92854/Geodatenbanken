# SQL Zusammenfassung
## Funktionsweise von Befehlen

```SQL
SELECT DISTINCT
```
* bei mehrfach vorkommendenden Werten nur 1x ausgeben

## Tabelle erstellen
```SQL
CREATE TABLE IF NOT EXISTS 'tabellenname' (
'spalte1' datentyp{
int(länge) auto_increment / float(länge) / text character set utf8 /  date / time / datetime / timestamp / varchar(länge) / serial
} unsigned / signed NOT NULL,
spalte2,
PRIMARY KEY ('spaltenname')
```

## Tabelle löschen
```SQL
DROP TABLE IF tabellenname
```

## Tabelle ändern
```SQL
ALTER TABLE
```


## Reihenfolge einer Select-Abfrage
```SQL
SELECT DISTINCT spaltenname1 AS aliasname, spaltenname2, COUNT(*) / *
FROM tabellenname
WHERE bedingung1{
= < <= > >= wert
= 'text'
LIKE '%enthält' / LIKE 'enthält%' / LIKE '%enthält%'
} AND / OR bedingung2
GROUP BY spaltenname
HAVING bedinung{
COUNT(*) / SUM(spaltenname) / ... = < <= > >= wert
}
ORDER BY spaltenname1, spaltenname2 ASC / DESC
```

## Insert
```SQL
INSERT INTO tabellenname
VALUES('Wert1,'Wert2','Wert3')

ODER

INSERT INTO tabellenname(spalte1,spalte3)
VALUES('Wert1,'Wert3')
```
> Bei Option 1 müssen in alle Spalten Werte eingetragen werden

> Bei Option 2 werden leere Werte mit NULL aufgefüllt

## Update
```SQL
UPDATE tabellenname
SET spaltenname = wert
WHERE bedingung
```

## Delete
```SQL
DELETE FROM tabellenname
WHERE bedingung
```

## Join
### Allgemeine Syntax / INNER JOIN
```SQL
SELECT spaltenname
FROM tabelle1
JOIN tabelle2 ON tabelle1.spaltenname = tabelle2.spaltenname
```

### LEFT JOIN
```SQL
SELECT spaltenname
FROM tabelle1
LEFT JOIN tabelle2 ON tabelle1.spaltenname = tabelle2.spaltenname
WHERE bedingung
```
> Es werden alle Werte der linken, aber nur die Werte der rechten Tabelle übernommen, die zur gegebenen Bedinung passen &rarr; Auffüllen mit NULL

### LEFT JOIN
```SQL
SELECT spaltenname
FROM tabelle1
RIGHT JOIN tabelle2 ON tabelle1.spaltenname = tabelle2.spaltenname
WHERE bedingung
```
> Es werden alle Werte der rechten, aber nur die Werte der linken Tabelle übernommen, die zur gegebenen Bedinung passen &rarr; Auffüllen mit NULL

### Union
```SQL
SELECT spaltenname1
FROM tabellenname1
UNION ALL SELECT spaltenname2
FROM tabellennname2
```
> Kombination mehrerer SELECT's

> ohne UNION ALL werden doppelte Werte ignoriert (= SELECT DISTINCT)

## Subquery
```SQL
SELECT spaltenname1
FROM tabellenname1
WHERE bedingung IN (SELECT spaltenname2 FROM tabellenname2)
```

## HAVING
```SQL
SELECT spaltenname
FROM tabellenname
GROUP BY spaltenname
HAVING bedingung = < <= > >= wert
```

## Weitere Funktionen
```SQL
AVG() - Durchschnittswert
COUNT() - Zeilen zählen
FIRST() - ersten Wert einer Spalte selektieren
LAST() - letzten Wert einer Spalte selektieren
MAX() - höchsten Wert einer Spalte selektieren
MIN() - kleinsten Wert einer Spalte selektieren
SUM() - Zeilenwerte summieren
LEN() - Länge einer Zeichenkette einer Spalte
ROUND() - selektierte Werte runden
```

## Constraints

## User defined Types
### ENUM erstellen

## Index erstellen

## Trigger erstellen

## Normalformen
### 0. Urtabelle
### 1. Normalform
### 2. Normalform
### 3. Normalform

## Geodatenbanken

## NewSQL

## NoSQL
