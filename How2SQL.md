# SQL Zusammenfassung
## Funktionsweise von Befehlen

```SQL
SELECT DISTINCT
```
* doppelte Werte nicht mitausgeben

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
DROP TABLE IF EXISTS tabellenname
```

## Tabelle ändern
```SQL
ALTER TABLE tabellenname
ADD spaltenname datentyp;
DROP COLUMN spaltenname
RENAME COLUMN altername to neuername;
ALTER COLUMN spaltenname neuerdatentyp;
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

## User Defined Types
* Erstellung der statischen Struktur &rarr; kein Verhalten

### Array
```SQL
CREATE TYPE typname AS VARRAY(länge) OF datentyp; -- Oracle

CREATE TABLE tabellenname (typname datentyp[]); -- PostgreSQL

INSERT INTO typname VALUES(ARRAY[wert1,wert2,wert3]);
```

### Domain
```SQL
CREATE DOMAIN meter AS FLOAT;
CREATE DOMAIN squaremeter AS FLOAT;
```
> Domain verwenden, ob spätere Fehler zu vermeiden

> Postgres betrachtet beide als zwei verschiedene, aber gleichartige Typen

### Composite Types
```SQL
CREATE TYPE typname AS(variable1 datentyp, variable2 datentyp);

CREATE TABLE tabellenname(spaltenname typname);

SELECT spaltenname.variable1, spaltenname.variable2 FROM tabllenname;
```

### Untertypen erstellen
```SQL
CREATE TYPE typname1 AS (variablenname1 datentyp, variablenname2 datentyp);

CREATE TYPE untertypname2
    UNDER typname1(variablenname3 datentyp);
```

### ENUM erstellen
```SQL
CREATE TYPE AS Enum enumname('wert1', 'wert2', 'wert3');

CREATE TABLE tabellenname(variable1 enumname);

SELECT 'wert1'::enumname;
```

## User Defined Functions
> Beispiele

```SQL
CREATE OR REPLACE FUNCTION funktionsname(
IN a INT,
INOUT b INT,
OUT c INT
)
RETURNS SETOF record
LANGUAGE plpgsql
VOLATILE
AS $BODY$
DECLARE
...
BEGIN
...
END;
$BODY$;
```

### Stored Procedure
```SQL
CREATE OR REPLACE FUNCTION insert()
RETURNS void
LANGUAGE sql
VOLATILE STRICT
SECURITY DEFINER
AS $BODY$
INSERT INTO table1 ...
INSERT INTO table2 ...
$BODY$;
```

### Function
```SQL
CREATE OR REPLACE FUNCTION sum(
IN a INT, IN b INT
)
RETURNS INT
LANGUAGE sql
IMMUTABLE STRICT
AS $BODY$
RETURN a + b;
$BODY$;
```

```SQL
CREATE OR REPLACE FUNCTION load_test(
IN id INT
)
RETURNS TABLE(a INT, b INT)
LANGUAGE sql
STABLE
AS $BODY$
RETURN QUERY
SELECT a, b
FROM test
WHERE id > coalesce(id, 0);
-- wenn id NULL, dann 0
$BODY$;
```

### Trigger
```SQL
CREATE OR REPLACE FUNCTION tr_change()
RETURNS trigger
LANGUAGE plpgsql
VOLATILE
AS $BODY$
BEGIN
NEW.changed_at := now();
END;
$BODY$;
```

### Cast Function
```SQL
CREATE OR REPLACE FUNCTION transform(
IN _var domainname
)
RETURNS INT
LANGUAGE sql
IMMUTABLE STRICT
AS $BODY$
RETURN _var::TEXT::INT;
$BODY$;
```

#### Konvertierung zwischen Datentypen, die nicht SQL-Standard enstprechen
```SQL
CREATE CAST (domainname AS DATENTYP)
WITH FUNCTION transform(mydomain);
```

### Aggregate Function
```SQL
CREATE OR REPLACE FUNCTION add_coord(
IN a coord,
IN b coord
)
RETURNS coord
LANGUAGE sql
IMMUTABLE STRICT
AS $BODY$
RETURN ROW(a.x + b.x, a.y + b.y);
$BODY$;
```
```SQL
CREATE AGGREGATE sum(coord)(
sfunc = add_coord,
stype = coord,
initcond = '(0,0)'
);
```

## Indexe (B-Tree)
* ohne Index langsame Datenverarbeitung
* selbstständige Datenstruktur
* eigener Speicherplatz benötigt
* verweist auf Daten in einer Tabelle
* doppelt verkettete Liste + Suchbaum

> Shared hit = gelesene Blöcke aus dem Cache

> read = nicht vorhandene Blöcke von der Platte

### Kosten
|Parameter|Description|Default|
|--|--|--|
|seq_page_cost|cost of a squentially fetched disk page|1.00|
|random_page_cost|cost of a nonsequentionally fetched disk page|4.00|
|cpu_tuple_cost|cost of processing each tuple (row)|0.01|
|cup_operator_cost|cost of processing each operator or functional call|0.0025|
|cpu_index_tuple_cost|cost of processing each index entry during an index scan|0.005|

### Normaler Index
```SQL
CREATE INDEX idx_normal ON table(tabellenname)
```

### Zusammengesetzter Index
```SQL
CREATE INDEX idx_normal ON table(tabelle1, tabelle2)
```

### Funktionaler Index -- was macht dieser nochmal?
```SQL
CREATE INDEX idx_normal ON table(LOWER(tabellenname))
```

### Partieller Index
```SQL
CREATE INDEX idx_normal ON table(tabellenname) WHERE bedinung
```

### Arten von Scans
* **Sequential Scan:** Liest Tabelle sequentiell
* **Index-Scan (Random Access Scan):** ließt Index mit Filterung (WHERE) &rarr; markiert invisible rows
* **Bitmap Index Scan:** liest erst den ganzen Index und dann die Tabelle *(schneller bei großen Datenmengen)*
* **Index Only Scan:** Liest nur Index

### Index löschen
```SQL
DROP INDEX indexname;
```

## Trigger erstellen

```SQL
CREATE TRIGGER triggername
BEFORE / AFTER INSERT OR UPDATE
ON tabellenname
FOR EACH ROW
SET NEW.spaltenname1 = (NEW.spaltenname2 + - * / wert);
```

## Normalformen
### 0. Urtabelle
### 1. Normalform
### 2. Normalform
### 3. Normalform

## Geodatenbanken
* Speicherung von vektorbasierten (Geo-) Daten
  * Punkte (x, y)
  * Linienzüge (x<sub>1</sub>, y<sub>1</sub>, x<sub>n</sub>, y<sub>n</sub>)
  * Polygone

### Index für räumliche Daten

#### R-Tree
* Untergliederung der großen Flächen in immer kleinere Flächen

<img src="https://github.com/s92854/Geodatenbanken/assets/134683810/08b882d0-d05c-408c-9ee3-e0d5cf387cb1">


#### GiST (Generalized Search Tree)
```SQL
CREATE INDEX <name> ON <tabellenname> USING GIST (<name_geometriespalte>);
```

#### GiST für n-dimensionale Indexe:
```SQL
CREATE INDEX <name> ON <tabellenname> USING GIST (<name_geometriespalte> gist_geometry_ops_nd);
```

#### BRIN (Block Range Index) für kleinere Indexe (Daten müssen sortiert sein!)
```SQL
CREATE INDEX <name> ON <tabellenname> USING BRIN (<name_geometriespalte>);
```

#### SP-GiST (Space-Partitioned Generalized Search Tree) für partitionierte Suchbäume
```SQL
CREATE INDEX <name> ON <tabellenname> USING SPGIST (<name_geometriespalte>);
```

#### Anlegen von nicht-blockierenden Indexen
```SQL
CREATE INDEX CONCURRENTLY <name> ON <tabellenname>
```

### Pattern Matrix (DE-9-Intersection-Model)
* Schnitt wird durch einzelnen Punkt beschrieben: **0**
* Schnitt wird durch Linien beschrieben: **1**
* Schnitt wird durch Flächen beschrieben: **2**
* Es liegt (irgendein) Schnitt vor: **T**
* Es liegt kein Schnitt vor: **F**
* es ist egal, ob ein Schnitt vorliegt (keine Aussage): *

<img height="600" src="https://github.com/s92854/Geodatenbanken/assets/134683810/880801e4-6855-40fb-bc75-514abf740e65">

* von links nach rechts, oben nach unten lesen: **212101212**


## NewSQL

## NoSQL
