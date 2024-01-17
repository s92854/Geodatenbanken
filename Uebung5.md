1. Erweitern des Flughafenschemas
   
a) Standort-Attribut erstellen:

```SQL
alter table airport
add Standort geometry;
```

Standorte updaten:
```SQL
update airport
set standort = ST_GeomFromText('Point(52.3776072 13.4857623)')
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

2. 

3. Flughäfen erweitern
```SQL

```
