1. Erweitern des Flughafenschemas
   
a) Standort des Flughafens erstellen:

```SQL
alter table airport
add Standort geometry;
```


b) Tabelle Bundesland erstellen:

```SQL
create table Bundesland (
	id serial,
	name text, 
	flaeche_in_km2 integer
);
```
