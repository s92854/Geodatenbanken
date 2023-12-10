# Übung 3
## Nico Haupt (956450) und Tara Richter (934172)

0. Das Schema kann man sich [hier]() herunterladen

&nbsp;

1. **Flughäfen**

a) Zuerst müssen die TYPES erstellt werden:

```SQL
CREATE TYPE status AS ENUM('Checkin','Boarding','Verspätet');

CREATE TYPE iataCodes AS ENUM('FRA','BER','SFX','STR','MUC','DUS','HAM','AMS','ZRH','VIE','LUX','PRG','BUD','JFK','LAX','MIA');

CREATE TYPE rolle AS ENUM('Checkin','Reinigung','Verkauf');


```

b) Anschließend muss die Tabelle flughafen erstellt werden. Wir setzen den IATA Code als Primary Key.

```SQL
CREATE TABLE flughafen (
	iata iataCodes PRIMARY KEY,
	airportname TEXT,
	anzPersonal INT,
	webseite TEXT,
	verkehrsanbindung TEXT,
	serviceTel TEXT,
	landebahn TEXT
);
```

c) Nun füllen wir die Tabelle mit Informationen

```SQL
INSERT INTO flughafen (iata,airportname,anzPersonal,webseite,verkehrsanbindung,serviceTel,landebahn)
VALUES ('FRA','Flughafen Frankfurt',NULL,'https://www.frankfurt-airport.com/de.html','Zug, Auto','0180 6 3724636','2800m');

INSERT INTO flughafen (iata,airportname,anzPersonal,webseite,verkehrsanbindung,serviceTel,landebahn)
VALUES ('BER','Flughafen Berlin Brandenburg Willy Brandt',NULL,'https://ber.berlin-airport.de/de.html','Zug, Auto','+49 30 6091 6091 0','3600m');

INSERT INTO flughafen (iata,airportname,anzPersonal,webseite,verkehrsanbindung,serviceTel,landebahn)
VALUES ('MUC','Flughafen München',NULL,'https://www.munich-airport.de','Zug, Auto','089 97500','4000m')

INSERT INTO flughafen (iata,airportname,anzPersonal,webseite,verkehrsanbindung,serviceTel,landebahn)
VALUES ('LAX','Los Angeles International Airport',NULL,'https://www.flylax.com','Zug, Auto','(855) 463-5252','2721m')
```

d) Nun rufen wir alle Informationen der Tabelle absteigend sortiert mit folgendem Befehl ab:

```SQL
SELECT * FROM flughafen ORDER BY airportname DESC
```

Das Ergebnis mit 3 Beispieldaten sieht dann so aus:

<img hight="700" src="https://github.com/s92854/Geodatenbanken/assets/134683810/302608a0-e470-495f-b832-079498ee59ea">

2. **Fluggesellschaften**
