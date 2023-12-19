# Übung 3
### Nico Haupt (956450) und Tara Richter (934172)

0. Das Schema kann man sich [hier]() herunterladen

&nbsp;

## 1. **Flughäfen**

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
SELECT *
FROM flughafen
ORDER BY airportname DESC
```

Das Ergebnis mit 3 Beispieldaten sieht dann so aus:

<img title="Informationen über Flughäfen" hight="700" src="https://github.com/s92854/Geodatenbanken/assets/134683810/302608a0-e470-495f-b832-079498ee59ea">

&nbsp;

## 2. **Fluggesellschaften**

a) Die Tabelle Flughafen ist bereits erstellt, jetzt fehlt jedoch noch die Tabelle Flugplan:

```SQL
CREATE TABLE flugplan (
	id SERIAL PRIMARY KEY,
	startairport TEXT,
	zielairport TEXT,
	flugnummer TEXT,
	fluggesellschaft TEXT
);
```

b) Diese Tabelle wird nun mit Beispieldaten gefüllt

```SQL
INSERT INTO flugplan (startairport,zielairport,flugnummer,fluggesellschaft) VALUES
('MUC','BER',NULL,'easyJet'),
('BER','LAX',NULL,'United Airlines'),
('MUC','SFX',NULL,'Conair'),
('MUC','VIE',NULL,'TUI Fly'),
('BUD','MUC',NULL,'Condor'),
('MUC','JFK',NULL,'Delta Air Lines'),
('AMS','BER',NULL,'Lufthansa'),
('MUC','SFX',NULL,'easyJet'),
('LAX','BER',NULL,'Eurowings'),
('STR','SFX',NULL,'Condor')
```

c) Dann kann die Abfrage erfolgen:

```SQL
SELECT DISTINCT fluggesellschaft
FROM flugplan
WHERE startairport = 'MUC';
```

<img title="Fluggesellschaften mit Abflug von München" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/134683810/03f3ee22-0522-4098-b6f7-3c7b1cefa282">

## 3. **Flugrouten**

```SQL
SELECT
    a.startairport,
    a.zielairport,
    COALESCE(b.anzahl_fluege, 0) AS anzahl_fluege
FROM
    (
        SELECT DISTINCT
            startairport,
            zielairport
        FROM
            flugplan
    ) a
LEFT JOIN
    (
        SELECT
            startairport,
            zielairport,
            COUNT(*) AS anzahl_fluege
        FROM
            flugplan
        WHERE
            fluggesellschaft = 'Lufthansa'
        GROUP BY
            startairport, zielairport
    ) b ON a.startairport = b.startairport AND a.zielairport = b.zielairport;
```

<img title="Flugrouten mit Anzahl von Lufthansa" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/17aec496-957d-483c-a988-50cef36d11dc">

## 4. **jährliche Passagierzahlen**

a) Datenbank Flughafen um die Passagierzahl erweitern

```SQL
alter table flughafen 
add Passagiere2022 integer,
add Passagiere2021 integer,
add Passagiere2020 integer,
add Passagiere2019 integer,
add Passagiere2018 integer,
add Passagiere2017 integer,
add Passagiere2016 integer;
```

b) Passagierzahlen beim Flughafen Frankfurt einarbeiten 

```SQL
update flughafen
set passagiere2022 = 48920000,
set passagiere2021 = 24810000,
set Passagiere2020 = 18770000,
set Passagiere2019 = 70590000,
set Passagiere2018 = 69510000,
set Passagiere2017 = 64510000,
set Passagiere2016 = 60790000
where iata = FRA;
```

c) Abfrage erstellen

## 5. **Abflugplan Dresden**

a) Flugplan-Datenbank um Status, Start- und Landezeit erweitern

```SQL
alter table flugplan 
add Startzeit time,
add Landezeit time;
```

b) Einfügen von Beispieldaten 
```SQL
INSERT INTO flugplan (startairport,zielairport,flugnummer,fluggesellschaft, status, startzeit, landezeit) 
VALUES ('DRS','AMS','KL1810','Royal Dutch Airline', 'CheckIn', '12:00:00', '13:30:00'),
('DRS','DUS','EW9023','Eurowings','CheckIn', '19:40:00', '20:50:00'),
('DRS','ZRH','LX919','Swiss International Air Lines', 'Boarding', '10:15:00', '11:30:00'),
('DRS','PMI','EW6803','Eurowings','Boarding', '17:35:00', '20:05:00'),
('DRS','AYT','XQ6272','Sun Express', 'Verspätet', '09:50:00', '14:55:00');
```

c) Abfrage
```SQL
SELECT flugnummer, fluggesellschaft, zielairport, status, startzeit, landezeit
FROM flugplan
WHERE  startairport = 'DRS';
```
<img title="Abflugplan von Dresden" hight="500" src="https://github.com/s92854/Geodatenbanken/assets/142684377/554d272c-f7d7-4000-aefd-b6f80b138dab">
