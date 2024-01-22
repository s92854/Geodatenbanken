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

c) Fläche Nordrhein-Westfalen erstellen (Polygon auf 8 Punkte aufgespannt):
```SQL
INSERT INTO bundesland (name, flaeche)
VALUES ('Nordrhein-Westfalen', 'POLYGON((5.9694583 51.8385864, 6.4113023 50.3185954, 9.4073073 51.6341921,
	8.6758016 52.536239, 7.9650879 52.5429551, 8.388496 52.1445514, 7.9261338 52.0761215, 7.6176956 52.4843394, 5.9694583 51.8385864))');
```

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

## 5. Flughäfen in Nordrhein-Westfalen
```SQL

```

## 6. Berechnungen von Flächeninhalt, geom. Mittelpunkt, minim. umschl. Kreis und Rechteck
```SQl

```

## 7. Nächstgelegene Flughäfen zu Köln/Bonn
```SQL

```

## 8. Distanz der Flughäfen Frankfurt und Los Angeles
### ST_Distance
```SQL

```

### ST_DistanceSphere
```SQL

```

### ST_DistanceSpheroid
```SQL

```
