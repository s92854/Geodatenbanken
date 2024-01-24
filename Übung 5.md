# Übung 5: Spatial

## 1. Erweitern des Flughafenschemas
   
a) Standort-Attribut erstellen:
```SQL
alter table airport
add Standort geometry;
```

Standorte updaten (Bsp. Berlin):
```SQL
update airport
set standort = ST_GeomFromText('Point(13.4857623 52.3776072)')
where iata ='BER';
```

<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/b0e91ea6-937c-456d-ba9b-ff9332db44f8">

b) Tabelle Bundesland erstellen:
```SQL
create table Bundesland (
	id serial,
	name text, 
	flaeche geometry
);
```

## 2. Indexieren

a) Index für iata-codes erstellen: 
```SQL
CREATE INDEX idx_iatacode ON airport USING btree (iata);
```

b) Geometry-Spalten indexieren:
```SQL
CREATE INDEX idx_standort ON airport USING gist (standort);
CREATE INDEX idx_flaeche ON bundesland USING gist (flaeche);
````

## 3. Flughäfen erweitern und hinzufügen

a) FRA und LAX erweitern:
```SQL
update airport
set standort = ST_GeomFromText('Point(8.5595777 50.0380192)')
where iata ='FRA';

update airport
set standort = ST_GeomFromText('Point(-118.4116839 33.9437902)')
where iata ='LAX';
```

<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/b0e91ea6-937c-456d-ba9b-ff9332db44f8">

b) Nordrhein-Westfalen Flughäfen hinzufügen:
```SQL
insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('FMO', 'Flughafen Münster/Osnarbrück', 'Germany', 'Münster', '2170m', ST_GeomFromText('Point(7.6838402 52.13297)'));

insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('NRN', 'Flughafen Niederrhein', 'Germany', 'Weeze', '2440', ST_GeomFromText('Point(6.1390716 51.6027586)'));

insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('PAD', 'Flughafen Paderborn/Lippstadt', 'Germany', 'Paderborn', '2180m', ST_GeomFromText('Point(8.6151872 51.6131764)'));

insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('DTM', 'Flughafen Dortmund', 'Germany', 'Dortmund', '2000m', ST_GeomFromText('Point(7.6094325 51.5173204)'));

insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('DUS', 'Flughafen Düsseldorf', 'Germany', 'Düsseldorf', '3000m', ST_GeomFromText('Point(6.7561146 51.2810789)'));

insert into airport (iata, airportname, country, city, landebahn, standort) 
values ('CGN', 'Flughafen Köln/Bonn', 'Germany', 'Köln', '2459m', ST_GeomFromText('Point(7.112011 50.87598)'));
```

<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/7d02c6e6-6a31-40a6-897a-de18ed2ed873">


c) Fläche Nordrhein-Westfalen erstellen (Polygon auf 8 Punkte aufgespannt):
```SQL
INSERT INTO bundesland (name, flaeche)
VALUES ('Nordrhein-Westfalen', 'POLYGON((5.9694583 51.8385864, 6.4113023 50.3185954, 9.4073073 51.6341921,
	8.6758016 52.536239, 7.9650879 52.5429551, 8.388496 52.1445514, 7.9261338 52.0761215, 7.6176956 52.4843394, 5.9694583 51.8385864))');
```
<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/bc7573c7-f5a7-4a06-8049-4af56c619fa8">

## 4. Düsseldorf und Umkreis
```SQL
select
    airportname,
    ST_Distance(
        ST_GeographyFromText('POINT(6.7561146 51.2810789)'),
        standort::geography
    ) / 1000 AS distance_km 
FROM
    airport
WHERE
    ST_DWithin(
        standort,
        ST_GeographyFromText('POINT(6.7561146 51.2810789)'), 
        200000 
    )
ORDER BY
    distance_km;
```

<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/333a196a-3bbb-493b-a6ce-b2e07de3f820">

## 5. Flughäfen in Nordrhein-Westfalen
Um sicherzugehen, dass postgis installiert ist, wird der CREATE EXTENSION Befehl ausgeführt
```SQL
CREATE EXTENSION IF NOT EXISTS postgis;
```

```SQL
SELECT iata,airportname,city
FROM airport
WHERE ST_Within(airport.standort, (SELECT flaeche FROM bundesland WHERE name = 'Nordrhein-Westfalen'));
```

<img src="https://github.com/s92854/Geodatenbanken/assets/134683810/e88615ca-52f8-4b7f-80e9-1fdb6ff39aa0">

## 6. Berechnungen von Flächeninhalt, geom. Mittelpunkt, minim. umschl. Kreis und Rechteck
### Flächeninhalt
```SQl
SELECT ST_Area((SELECT flaeche FROM bundesland WHERE name = 'Nordrhein-Westfalen')) AS Flächeninhalt
FROM bundesland;
```

<img title="Flächeninhalt" src="https://github.com/s92854/Geodatenbanken/assets/134683810/659d5eae-0415-48fa-b4c0-708abfefd639">

### Geometrischer Mittelpunkt
```SQL
SELECT ST_AsText(ST_Centroid((SELECT flaeche FROM bundesland WHERE name = 'Nordrhein-Westfalen'))) AS geometrischer_mittelpunkt
FROM bundesland;
```

<img title="Geometrischer Mittelpunkt" src="https://github.com/s92854/Geodatenbanken/assets/134683810/49bc9bc0-83b5-4b88-853f-872a397ade9c">

### Minimal umschließender Kreis
```SQL
SELECT ST_AsText(ST_MinimumBoundingCircle((SELECT flaeche FROM bundesland WHERE name = 'Nordrhein-Westfalen'))) AS minimal_umschließender_kreis
FROM bundesland;
```

<img title="Minimal umschließender Kreis" src="https://github.com/s92854/Geodatenbanken/assets/134683810/26b30bc7-fcd4-4013-bc44-07eb827f95a2">

### Minimal umschließendes Rechteck
```SQL
SELECT ST_AsText(ST_Envelope((SELECT flaeche FROM bundesland WHERE name = 'Nordrhein-Westfalen'))) AS minimales_umschließendes_rechteck
FROM bundesland;
```

<img title="Minimal umschließendes Rechteck" src="https://github.com/s92854/Geodatenbanken/assets/134683810/9d1a9371-365c-4f27-bbca-4eb495beb541">

## 7. Nächstgelegene Flughäfen zu Köln/Bonn
```SQL
SELECT iata, airportname, ST_AsText(standort) AS geometrie, ST_Distance(standort, (SELECT standort FROM airport WHERE iata = 'CGN')) AS distanz
FROM airport
WHERE iata != 'CGN'
ORDER BY ST_Distance(standort, (SELECT standort FROM airport WHERE iata = 'CGN')) ASC
LIMIT 3;
```

<img title="Nächsten 3 Flughäfen zum Flughafen Köln" src="https://github.com/s92854/Geodatenbanken/assets/134683810/64bf09fe-11f5-494f-9b9b-46ab22fc5072">

## 8. Distanz der Flughäfen Frankfurt und Los Angeles
```SQL
SELECT ST_Distance((SELECT standort FROM airport WHERE iata = 'FRA'),(SELECT standort FROM airport WHERE iata = 'LAX')) AS distanz,
ST_DistanceSphere((SELECT standort FROM airport WHERE iata = 'FRA'),(SELECT standort FROM airport WHERE iata = 'LAX'))/1000 AS distanz_sphere,
ST_DistanceSpheroid((SELECT standort FROM airport WHERE iata = 'FRA'),(SELECT standort FROM airport WHERE iata = 'LAX'),'SPHEROID["WGS 84",6378137,298.257223563]')/1000 AS distanz_spheroid
FROM airport
LIMIT 1;
```

<img title="Distanz Flughäfen FRA LAX" src="https://github.com/s92854/Geodatenbanken/assets/134683810/109f7452-d791-4dd2-a6df-e16841a5ccac">

Der erhaltene Wert bei ST_Distance ist in Grad. Deshalb muss die Geometrie in Geographie umgerechnet werden. Dazu muss vorerst die SRID von WGS 84, der Spalte *standort* gesetzt werden, da man sonst eine Fehlermeldung erhält.

```SQL
UPDATE airport
SET standort = ST_SetSRID(standort, 4326)
```

```SQL
SELECT ST_Distance(ST_Transform(standort, 4326)::geography, ST_Transform((SELECT standort FROM airport WHERE iata = 'LAX'), 4326)::geography)/1000 AS distanz,
ST_DistanceSphere((SELECT standort FROM airport WHERE iata = 'FRA'),(SELECT standort FROM airport WHERE iata = 'LAX'))/1000 AS distanz_sphere,
ST_DistanceSpheroid((SELECT standort FROM airport WHERE iata = 'FRA'),(SELECT standort FROM airport WHERE iata = 'LAX'),'SPHEROID["WGS 84",6378137,298.257223563]')/1000 AS distanz_spheroid
FROM airport
WHERE iata = 'FRA';
```

<img title="Umrechnung in km" src="https://github.com/s92854/Geodatenbanken/assets/134683810/9a9ad94d-b3d2-46ff-9b01-f3f84117ff12">

Die Werte sind so unterschiedlich, da *ST_Distanz* die planare Distanz, *ST_DistanzSphere* die Distanz auf einer idealen Kugel und *ST_DistanzSpheroid* die Distanz auf dem Spheroid des Bezugssystems WGS84 berechnet. Das Spheroid stellt keine Kugel, sondern eine Ellipse dar. Ich gehe davon aus, dass der Distanzunterschied zwischen ST_Distance und ST_DistanceSpheroid sehr gering ist, da bei diesen beiden Methoden mit dem Bezugssystem WGS 84 gerechnet wurde und somit trotz großer Distanzen nur ein sehr geringer Unterschied zu verzeichnen ist.
