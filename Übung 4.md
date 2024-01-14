# Übung 4: User Defined Objects

## 1. Enum für Status

a) Den Type Flugstatus mit 3 Variablen erstellen:

```SQL
CREATE TYPE flugstatus AS Enum('CheckIn', 'Boarding', 'Verspaetet');
````

b) Den Datatype des Attributs Status ändern:

```SQL
ALTER TABLE flugplan
ALTER COLUMN status TYPE flugstatus
USING status::flugstatus;
````

## 2. User Defined Type für Iata

a) Die Domain iata_codes erstellen

```SQL
CREATE domain iata_code AS CHAR(3);
````

b) Den Datatype des Attributs iata ändern:

```SQL
ALTER TABLE airport 
ALTER COLUMN iata type iata_code
USING iata::iata_code;
````

## 3. User Defined Function

User Defined Function anlegen: 

```SQL
CREATE OR REPLACE FUNCTION create_flightplan(
    p_flugnr TEXT,
    p_fluggesellschaft TEXT,
    p_iataDep CHAR(3),
    p_iataArr CHAR(3),
    p_departure TIME,
    p_arrival TIME,
	p_status flugstatus
) 
RETURNS VOID
LANGUAGE plpgsql
VOLATILE STRICT
SECURITY DEFINER
AS $$
BEGIN
    IF NOT EXISTS (SELECT 1 FROM fluggesellschaft WHERE name = p_fluggesellschaft) THEN
        RAISE EXCEPTION 'Die Fluggesellschaft "%" existiert nicht.', p_fluggesellschaft;
    END IF;

    IF NOT EXISTS (SELECT 1 FROM airport WHERE iata_code = p_iataDep) THEN
        RAISE EXCEPTION 'Der Start-Flughafen mit IATA-Code "%" existiert nicht.', p_iataDep;
    END IF;

    IF NOT EXISTS (SELECT 1 FROM airport WHERE iata_code = p_iataArr) THEN
        RAISE EXCEPTION 'Der Ziel-Flughafen mit IATA-Code "%" existiert nicht.', p_iataArr;
    END IF;

    INSERT INTO flugplan (flugnr, fluggesellschaft, iatadep, iataarr, departure, arrival, status)
    VALUES (p_flugnr, p_fluggesellschaft, p_iataDep, p_iataArr, p_departure, p_arrival, p_status);
END;
$$;
````

## 4. Trigger

Trigger anlegen:

```SQL
CREATE OR REPLACE FUNCTION audit_column()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $Body$
BEGIN
    NEW.changed_at = NOW(); 
    NEW.changed_by = current_user;
    RETURN NEW;
END;
$Body$;

CREATE TRIGGER before_update_trigger
BEFORE UPDATE
ON flugplan
FOR EACH ROW
EXECUTE FUNCTION audit_column();
```

# Übung 4: Zugriff über eine Programmiersprache (Python)
## Voraussetzungen
1. Postgres + PGAdmin muss installiert sein. [Hier](https://sbp.enterprisedb.com/getfile.jsp?fileid=1258792) Postgres v16.1 herunterladen und installieren.

2. Programmierumgebung installieren (z.B. [Visual Studio Code](https://vscode.download.prss.microsoft.com/dbazure/download/stable/0ee08df0cf4527e40edc9aa28f4b5bd38bbff2b2/VSCode-darwin-universal.zip))

3. Datenbank in PGAdmin anlegen (hier mit dem Namen *postgres*)

4. Es muss das Modul psycopg2 per cmd installiert werden, damit die Verbindung zwischen der Programmierumgebung und PGAdmin/PostgreSQL hergestellt werden kann
```bash
pip install psycopg2
```

## Python-Code
Dieser Python Code wartet auf den Userinput und entscheidet anhand der gegebenen Variablen welche der beiden Funktionen (saveStatistic und showStatistic) ausgeführt wird. Sollte nur die Passagieranzahl oder das Jahr leer sein, wird in der Konsole eine 'Fehlermeldung' zurückgegeben.

```Python
# Import von psycopg2 als Verbindung zwischen Python und PGAdmin
import psycopg2 as p

# Einloggen in die lokale Datenbank
conn = p.connect(host="localhost", dbname="postgres", user="postgres", password="postgres", port=5432)
cur = conn.cursor()

# Tabelle PassengerStatistic anlegen
cur.execute("""CREATE TABLE IF NOT EXISTS PassengerStatistic(id SERIAL PRIMARY KEY, airport_code VARCHAR(3),passenger INT,year INT)""")

# Methoden saveStatistic und showStatistic definieren
def saveStatistic(fhc, psng, yr):
    cur.execute("""INSERT INTO PassengerStatistic(airport_code,passenger,year) VALUES(%s, %s, %s)""", (fhc, psng, yr))
    return

def showStatistic(fhc):
    cur.execute("""SELECT * FROM PassengerStatistic WHERE airport_code = %s""", (fhc,))
    for row in cur.fetchall():
        print(row)
    return

# Über runStatistic wird entschieden welche der beiden oberen Methoden ausgeführt wird
def runStatistic(fhc, psng, yr):
    if psng is None:
        if yr is None:
            showStatistic(fhc)
            conn.commit()
            cur.close()
            conn.close()
            return
        else:
            return print("Die Passagieranzahl sollte nicht leer sein.")
    else:
        if yr is None:
            return print("Das Jahr sollte nicht leer sein.")
        else:
            saveStatistic(fhc, psng, yr)
            conn.commit()
            cur.close()
            conn.close()
            return

# Benutzer gibt Daten ein
fhc = input('Flughafencode: ')
psng = input('Fluggaeste: ') or None
yr = input('Jahr: ') or None

# Ausführen des eigentlichen Programms
runStatistic(fhc, psng, yr)
```
