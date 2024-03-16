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

## Index erstellen

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

## NewSQL

## NoSQL
