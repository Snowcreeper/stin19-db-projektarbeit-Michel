# stin19-db-projektarbeit-Michel

## 6

### Bereinigung der Tabellen

#### Schritt 1

Einfügen einer neuen Spalte id in der Tabelle fahrzeiten_soll_ist als Primary Key

![tram 6 1](tram_6_1.JPG)

#### Schritt 2

Einfügen der Foreign Key Constraints fahrzeiten_soll_ist nach haltepunkt

![tram 6 2](tram_6_2.JPG)

#### Schritt 3

Einfügen der Foreign Key Constraints haltepunkt nach haltestelle

![tram 6 3](tram_6_3.JPG)

#### Schritt 4

Zusätzliche Spalten für DatumZeit

![tram 6 4](tram_6_4.JPG)

#### Schritt 5

Konvertierung der Datentypen 

![tram 6 5](tram_6_5.JPG)

### Script von Schritt 1 bis 5

    USE vbzdata;
    -- Schritt1
    ALTER TABLE fahrzeiten_soll_ist ADD id INT PRIMARY KEY AUTO_INCREMENT FIRST;
    -- Schritt2
    ALTER TABLE fahrzeiten_soll_ist ADD CONSTRAINT fahrzeiten_soll_ist_FK FOREIGN KEY (halt_punkt_id_von) REFERENCES haltepunkt(halt_punkt_id);
    ALTER TABLE fahrzeiten_soll_ist ADD CONSTRAINT fahrzeiten_soll_ist_FK_1 FOREIGN KEY (halt_punkt_id_nach) REFERENCES haltepunkt(halt_punkt_id);
    -- Schritt 3
    ALTER TABLE haltepunkt ADD CONSTRAINT haltepunkt_FK FOREIGN KEY (halt_id) REFERENCES haltestelle(halt_id);
    -- Schritt 4
    ALTER TABLE fahrzeiten_soll_ist ADD betriebs_datum DATE NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_soll_an_von DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_ist_an_von DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_soll_ab_von DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_ist_ab_von DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datum__nach DATE NULL;
    UPDATE fahrzeiten_soll_ist SET betriebs_datum = STR_TO_DATE(betriebsdatum ,'%d.%m.%Y');
    -- Schritt 5
    UPDATE fahrzeiten_soll_ist SET datumzeit_soll_an_von = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL soll_an_von SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_ist_an_von = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL ist_an_von SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_soll_ab_von = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL soll_ab_von SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_ist_ab_von = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL ist_ab_von SECOND);
    UPDATE fahrzeiten_soll_ist SET datum__nach =STR_TO_DATE(datum_nach ,'%d.%m.%Y');
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_soll_an_nach DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_ist_an_nach DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_soll_ab_nach DATETIME NULL;
    ALTER TABLE fahrzeiten_soll_ist ADD datumzeit_ist_ab_nach DATETIME NULL;
    UPDATE fahrzeiten_soll_ist SET datumzeit_soll_an_nach = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL soll_an_nach SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_ist_an_nach = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL ist_an_nach SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_soll_ab_nach = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL soll_ab_nach SECOND);
    UPDATE fahrzeiten_soll_ist SET datumzeit_ist_ab_nach = DATE_ADD(STR_TO_DATE(datum_von,'%d.%m.%Y'), INTERVAL ist_ab_nach SECOND);

## 7

### Erstellen Sie eine Abfrage über Zeitdifferenzen

![tram 7 1](tram_7_1.JPG)


Script:

        use vbzdat;
        create or replace view timedifference
        as select
        fsi.linie, fsi.richtung, fsi.fahrzeug, fsi.kurs, fsi.seq_von,
        fsi.halt_id_von, fsi.halt_id_nach, fsi.halt_punkt_id_von,
        fsi.halt_punkt_id_nach, fsi.fahrt_id, fsi.fahrweg_id, fsi.fw_no,
        fsi.fw_typ, fsi.fw_kurz, fsi.fw_lang, fsi.betriebs_datum,
        fsi.datumzeit_soll_an_von, fsi.datumzeit_ist_an_von,
        fsi.datumzeit_soll_ab_von, fsi.datumzeit_ist_ab_von, fsi.datum__nach,
        TIMEDIFF (datumzeit_soll_an_von, datumzeit_ist_an_von) as timediff_an,
        TIMESTAMPDIFF(SECOND, datumzeit_soll_an_von, datumzeit_ist_an_von) as timediff_an_seconds,
        TIMEDIFF (datumzeit_soll_ab_von, datumzeit_ist_ab_von) as timediff_ab,
        TIMESTAMPDIFF(SECOND, datumzeit_soll_ab_von, datumzeit_ist_ab_von) as timediff_ab_seconds,
        TIMESTAMPDIFF(SECOND, datumzeit_soll_an_von, datumzeit_soll_ab_von) as halt_soll_time_seconds,
        TIMESTAMPDIFF(SECOND, datumzeit_ist_an_von, datumzeit_ist_ab_von) as halt_ist_time_seconds
        from fahrzeiten_soll_ist fsi
        where fsi.fahrt_id=403 and fsi.linie=3 and fsi.datum_von='01.01.21'
        limit 40000;

## 8

### Linien Tabelle

#### a & b

![tram 8 1](tram_8_1.JPG)

Script:

        use vbzdat;
        create or replace view abfrage8a
        as SELECT DISTINCT
            fsi.fahrweg_id,
            fsi.linie,
            fsi.richtung,
            fsi.fw_no,
            fsi.fw_lang
        from fahrzeiten_soll_ist fsi
        where fsi.linie=3

#### c

![tram 8 2](tram_8_2.JPG)

## 9

### Ankunftszeitentabelle

![tram 9 1](tram_9_1.JPG)


Script:

        USE vbzdat;
        CREATE TABLE ankunftszeiten 
        SELECT  
            fsi.halt_punkt_id_nach as haltpunkt_id,
            fsi.fahrweg_id,
            fsi.fahrt_id,
            fsi.datumzeit_ist_an_nach AS datumszeit_ist_an,
            fsi.datumzeit_soll_an_nach AS datumzeit_soll_an,
            fsi.datumzeit_soll_ab_nach AS datumzeit_soll_ab,
            TIMESTAMPDIFF (second, datumzeit_soll_an_nach, datumzeit_ist_an_nach) AS delay
        FROM 
           fahrzeiten_soll_ist fsi
        WHERE
            fsi.linie =3

        UNION

        SELECT 
            fsi.halt_punkt_id_von AS haltpunkt_id,
            fsi.fahrweg_id,
            fsi.fahrt_id, 
            fsi.datumzeit_ist_an_von AS datumszeit_ist_an,
            fsi.datumzeit_soll_an_von AS datumzeit_soll_an,
            fsi.datumzeit_soll_ab_von AS datumzeit_soll_ab,
            TIMESTAMPDIFF (second, datumzeit_soll_an_von, datumzeit_ist_an_von) AS DELAY
        FROM 
           fahrzeiten_soll_ist fsi
        WHERE
            fsi.seq_von=1 AND fsi.linie =3; 

        ALTER TABLE ankunftszeiten ADD id INT PRIMARY KEY auto_increment FIRST;
        ALTER TABLE ankunftszeiten ADD CONSTRAINT ankunftszeiten_FK FOREIGN KEY (haltpunkt_id) REFERENCES haltepunkt(halt_punkt_id);
        ALTER TABLE ankunftszeiten ADD CONSTRAINT ankunftszeiten_FK_1 FOREIGN KEY (fahrweg_id) REFERENCES linie(fahrweg_id);

## 10

### Verspätungsliste pro Linie

![tram 10 1](tram_10_1.JPG)

Script:

    use vbzdat;
    SELECT
      a.id,
      a.haltpunkt_id,
      h2.halt_lang,
      h.GPS_Latitude,
      h.GPS_Longitude,
      a.fahrweg_id,
      l.linie,
      a.datumszeit_ist_an,
      a.datumzeit_soll_an,
      a.delay
    FROM ankunftszeiten a
    INNER JOIN haltepunkt h ON
      a.haltpunkt_id = h.halt_punkt_id
    INNER JOIN haltestelle h2 ON
      h.halt_id = h2.halt_id
    INNER JOIN linie l ON
      a.fahrweg_id = l.fahrweg_id
    ORDER BY a.delay DESC
    LIMIT 20;

Export Tabelle für Map

![tram 10 2](tram_10_2.JPG)

Script:

    use vbzdat;
    CREATE TABLE delay_export_linie_3
    SELECT
      h.GPS_Latitude AS lat,
      h.GPS_Longitude AS lng,
      h2.halt_lang AS name,
      '#299438' AS color,
      a.delay AS note
    FROM ankunftszeiten a
    INNER JOIN haltepunkt h ON
      a.haltpunkt_id = h.halt_punkt_id
    INNER JOIN haltestelle h2 ON
      h.halt_id = h2.halt_id
    INNER JOIN linie l ON
      a.fahrweg_id = l.fahrweg_id
    ORDER BY a.delay DESC
    LIMIT 20;
    
Map mit Delay Standorten
    
![tram 10 3](tram_10_3.JPG)

## 11

Die Linie 3 auf der Map

![tram 11 1](tram_11_1.JPG)

Script:

        use vbzdat;
        DROP TABLE IF EXISTS map_export_linie_3;
        CREATE TABLE map_export_linie_3
        SELECT
            h.GPS_Latitude AS lat,
            h.GPS_Longitude AS lng,
            h2.halt_lang AS name,
            '#299438' AS color,
            a.delay AS note
        FROM ankunftszeiten a
        INNER JOIN haltepunkt h ON
            a.haltpunkt_id = h.halt_punkt_id
        INNER JOIN haltestelle h2 ON
            h.halt_id = h2.halt_id
        INNER JOIN linie l ON
            a.fahrweg_id = l.fahrweg_id
        where a.fahrt_id=403 and Date(a.datumszeit_ist_an)='21-01-01'
        ORDER BY a.haltpunkt_id;

## 12

Fahrzeiten Linie 3

![tram 12 1](tram_12_1.JPG)

Script:

        use vbzdat;
        CREATE or replace view aufgabe12view
        as SELECT
            h2.halt_lang AS Verkehrshinweis,
            time(a.datumzeit_soll_ab) AS Abfahrt
        FROM ankunftszeiten a
        INNER JOIN haltepunkt h ON
            a.haltpunkt_id = h.halt_punkt_id
        INNER JOIN haltestelle h2 ON
            h.halt_id = h2.halt_id
        where a.fahrt_id=403 and Date(a.datumszeit_ist_an)='21-01-01'
        ORDER BY a.datumszeit_ist_an;

## 13

### Nächste Station zu meinem Standort

Vier nächste Stationen zum Paradeplatz: 47.36975, 8.53890

Tabelle

![tram 13 1](tram_13_1.JPG)

Map

![tram 13 2](tram_13_2.JPG)

Script:

        use vbzdat;
        set @lat = 47.36975;
        set @lng = 8.53890;
        DROP TABLE IF EXISTS map_export_4closest_stops;
        CREATE TABLE map_export_4closest_stops
        SELECT
            h.GPS_Latitude AS lat,
            h.GPS_Longitude AS lng,
            h2.halt_lang AS name,
            '#299438' AS color,
            round(st_distance_sphere(point(@lng, @lat), point(h.GPS_Longitude, h.GPS_Latitude)), 2) AS note
        FROM ankunftszeiten a
        INNER JOIN haltepunkt h ON
            a.haltpunkt_id = h.halt_punkt_id
        INNER JOIN haltestelle h2 ON
            h.halt_id = h2.halt_id
        INNER JOIN linie l ON
            a.fahrweg_id = l.fahrweg_id
        where a.fahrt_id=403 and Date(a.datumszeit_ist_an)='21-01-01'
        ORDER BY note
        LIMIT 4;

## 14

### Distanz zwischen zwei Haltestellen

![tram 14 1](tram_14_1.JPG)

Script:

        use vbzdat;
        DROP TABLE IF EXISTS listenDistanzen;
        CREATE TABLE listenDistanzen
        SELECT h2.halt_lang as Erste_Station,
        h4.halt_lang as Zweite_Station,
        h.GPS_Latitude as lat1,
        h.GPS_Longitude as lng1,
        h3.GPS_Latitude as lat2,
        h3.GPS_Longitude as lng2,
        ROUND(ST_Distance_Sphere(point(h.GPS_Longitude , h.GPS_Latitude), point(h3.GPS_Longitude , h3.GPS_Latitude)),2) as distanz
        FROM fahrzeiten_soll_ist fsi
        LEFT JOIN haltepunkt h ON h.halt_punkt_id = fsi.halt_punkt_id_von
        LEFT JOIN haltestelle h2 on h.halt_id = h2.halt_id
        LEFT JOIN haltepunkt h3 ON h3.halt_punkt_id = fsi.halt_punkt_id_nach
        LEFT JOIN haltestelle h4 on h3.halt_id = h4.halt_id
        WHERE fsi.linie = 3 AND fsi.fahrt_id = 403 AND DATE(fsi.datumzeit_ist_an_von) = '2021-01-01'
        ORDER BY distanz;
