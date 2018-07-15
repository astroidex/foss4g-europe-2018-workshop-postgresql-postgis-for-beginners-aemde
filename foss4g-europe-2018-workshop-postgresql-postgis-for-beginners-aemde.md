# Workshop PostgreSQL/PostGIS for beginners

[FOSS4G Europe 2018 Workshop Guimarães (Portugal)](https://europe.foss4g.org/2018)

![](img/foss4g-eu-2018.png ) ![](img/postgresql_postgis.png)


## Astrid Emde

* Astrid Emde
* WhereGroup GmbH & Co. KG ![](img/WhereGroup.png )
* astrid.emde@wheregroup.com
* [@astroidex](https://twitter.com/astroidex)


## What we learn 
* Why database?
* PostgreSQL / PostGIS
* SQL Basics
* Import data / Export data
* Spatial Functions
* Spatial Index
* Roles & handle access to your data


## OSGeoLive

![](img/osgeolive.png )

This Workshop uses OSGeoLive (https://live.osgeo.org) Version 11.0 (August 2017). OSGeoLive is based on Lubuntu and contains a collection of more than 50 pre-installed software projects. OSGeoLive also contains example data which will be used for the workshop.

You can download OSGeoLive with the following link. You can install OSGeoLive, run it in a virtual machin (recommended) or use it on an USB stick.

* Download OSGeoLive Image http://live.osgeo.org/en/download.html
* Dokumentation https://live.osgeo.org/
* PostGIS Overview (OSGeoLive Overview) https://live.osgeo.org/en/overview/postgis_overview.html
* PostGIS Quickstart (OSGeoLive Quickstart) https://live.osgeo.org/en/quickstart/postgis_quickstart.html


## actual Software Versions

* PostgreSQL 10.4 (2018-05-10) https://www.postgresql.org/
* PostGIS 2.4.0 (2017-10-1) http://www.postgis.org/


### OSGeoLive 11.0

* PostgreSQL 9.5.11
* PostGIS 2.3.2

```sql
SELECT version(), postgis_version(), postgis_full_version();
```

## Daten

* Natural Earth 
 * as ESRI Shapes - countries, provinces, rivers, populated places & more
 * database: natural_earth2
* OpenStreetMap
 * database: osm_local


## additional information

* PostGIS in Action (August 2015, 2. Auflage) Regine Obe, Leo Hsu ISBN 9781617291395
* Paul Ramsey Blog Clever Elephant http://blog.cleverelephant.ca/
* Clever Elephant ;) https://www.youtube.com/watch?v=Gw_Q1JClH58
* Postgres OnLine Journal Regine Obe, Leo Hsu http://www.postgresonline.com/
* Modern SQL Blog Markus Winand https://modern-sql.com/slides https://use-the-index-luke.com/
* PGConf.DE 13. April 2018 in Berlin http://2018.pgconf.de/
* PostgreSQL books https://www.postgresql.org/docs/books/
* pgRouting: A Practical Guide (Mai 2017, 2. Auflage) Regine Obe, Leo Hsu ISBN: 9780989421737


## Why database?

* central storage of data
* multi-user access
* restricted access via access control and access management
  

## PostgreSQL

* supported by other programs
* fast, powerfull, reliable, robust
* easy to maintain
* follows SQL standard
* API to many programming languages
* subselects, functions, trigger, Foreign Data Wrapper & more
* https://www.postgresql.org/about/


## PostGIS

* Extension for PostgreSQL
* follows standard - OGC Simple Feature Spezification for SQL and OGC ISO SQL/MM Spezification 
* provides many spatial functions 
* widly supported by other programms
* easy import / export of spatial data (QGIS, shp2pgsql, pgsql2shp, ogr2ogr, dxf2postgis, osm2pgsql)
* can use the advantages from PostgreSQL (user management, replication, indexing & more)
* Very powerul: vector & raster data, geometry (planar) and geography (spheroid), circular objects, 3D, 4D, point cloud, pg_routing for routing, topography
* Stores data as WKB (Well-known Binary) and displays it as WKT (Well-known text)
* http://www.postgis.org/
* http://postgis.net/docs/manual-2.4/


## Database Clients

* pgAdmin III and new pgAdmin 4 https://www.pgadmin.org/
* psql command line client https://www.postgresql.org/docs/current/static/app-psql.html
* QGIS DB Manager integrated in QGIS

### Excercise 1: pgAdmin & first steps in the database

1. Open pgAdmin
2. Connect to database natural_earth2
3. Go to schema public and look for tables
4. Open a table and look for the geometry column. Can you read the geometry?
5. Open table spatial_ref_sys and filter by srid = 4326
7. Go to schema public -> views and open the metadata view geometry_columns


## How to communicate with the database?

* via SQL - Structured Query Language
* DDL - data definition language
* DML - data manipulation language
* DQL - data query language 


### DQL 

* DQL - to query your data (DQL is part of DML
) f.e to show all data from table spatial_ref_sys with srid = 4326

```sql
SELECT * FROM spatial_ref_sys WHERE srid=4326;
```

### DDL 

* DDL to create database, table, user, schema and more

```sql
CREATE DATABASE demo;
```

```sql
CREATE TABLE pois(
 gid serial PRIMARY KEY,
 name varchar,
 year int,
 info varchar,
 geom geometry(point,4326)
);
```

Modify your table

```sql
ALTER TABLE pois ADD COLUMN land varchar;
ALTER TABLE pois RENAME land TO country;
ALTER TABLE pois DELETE COLUMN country;
ALTER TABLE pois ADD CONSTRAINT pk_gid PRIMARY KEY (gid); 
```

Delete your table

```sql
DROP TABLE pois;
```

### DML

* manipulate your data - create data, delete data, change data

```sql
INSERT INTO pois (name, year, info, geom) VALUES 
(
'Kölner Dom',
1248,
'https://en.wikipedia.org/wiki/Cologne_Cathedral', 
ST_SetSRID(ST_MakePoint(50.941357,6.958307),4326)
);
```

```sql
DELETE FROM pois WHERE name = 'Kölner Dom';
DELETE FROM pois; -- deletes all data
DELETE FROM pois WHERE gid = 1111;
```

```sql
UPDATE pois SET name = 'Cologne Cathedral' WHERE name = 'Kölner Dom';
```

### Excercise 2: Create your own database with PostGIS extension 

1. Create your own database with the name foss4g 

Notice: Use lower case and no spaces as name for your database, tables columns! Makes live easier. As you do not have to use quotations - like "FOSS4G"

2. Change the connection and connect to your new database.

3. Load the extension postgis to your new database to be able to handle spatial data.

4. Check whether the postgis functions, spatial_ref_sys table and the metadata views are there


```sql
CREATE DATABASE foss4g;

CREATE EXTENSION postgis;
```


### Excercise 3: createdb via command line 

* check the PostGIS Quickstart and see how the dabase demo was created 
* https://live.osgeo.org/en/quickstart/postgis_quickstart.html
* PostgreSQL provides utility programs like createdb and dropdb to communicate with the database

```sql
createdb -U user demo
createdb --help

psql -U user demo
CREATE EXTENSION postgis;
```

### Excercise 4: Create your own table cities

* create a new table cities with gid, name, country and geom (see example above)
* create a point for Guimarães with ST_MakePoint - find the coordinate http://latitudelongitude.org/pt/guimaraes/


```sql
CREATE TABLE cities(
 gid serial PRIMARY KEY,
 name varchar,
 country varchar,
 geom geometry(point,4326)
);
```

```sql
INSERT INTO cities(
            name, geom, country)
    VALUES ('Guimarães',ST_SetSrid(ST_MakePoint(-8.29619,41.44443),4326),'Portugal');
```



## QGIS to visualize your data

* You can visualize, edit and import/export data from a PostgreSQL/PostGIS database
* You need the information how to connect to the database - only authorized users can connect
 

### Excercise 5: Load data from natural_earth2 and from your new database

1. Load countries from the database natural_earth2
2. Create a new PostGIS connection to your new database foss4g
3. Load your new table cities
4. Add a new point to your cities table and mark the place where you come from (approximately)


## QGIS import data to PostgreSQL via QGIS DB Manager

You can use the QGIS DB Manager to import/export data to/from your database. You find the QGIS DB Manager in the menu at Database -> DB Manager. You already need a connection to the PostgreSQL database that you would like to use.

Best way is to add the data you would like to import to a QGIS project. You can filter the data if you only want to import a subset of your data. 

* Open the DB Manager
* Connect to your database
* use the Import layer/file button
* choose your data
* define a name for your table, define the SRID, add a primary key (gid recommended)
* Create a spatial index
* start the import
* add the imported data via drag & drop to your QGIS project


### Excercise 6: Load data from natural_earth2 shapes to your database

* import ne_10m_admin_0_countries.shp to table ne_10m_admin_0_countries
* /home/user/data/natural_earth2/ne_10m_admin_1_states_provinces_shp.shp to table ne_10m_admin_1_states_provinces_shp
* also import provinces and only import the provinces from Portugal to table provinces_pt (use Filter)
* import all ne_10m_populated_places to table ne_10m_populated_places
* have a look to your metadata view geometry_columns


## Get to know PostGIS functions 

* PostGIS Documentation http://postgis.net/docs/manual-2.4/
* PostGIS Vector Functions see Chapter 8: http://postgis.net/docs/manual-2.4/reference.html

### ST_AsEWKT or ST_AsText - to display the geometry as text

```sql
SELECT ST_AsText(geom) FROM pois; -- without SRID
SELECT ST_AsEWKT(geom) FROM pois; -- with SRID
SELECT ST_AsEWKT(geom) FROM provinces_pt;
``` 

### Geometry Constructors

* there are many functions to create geometries see Geometry Constructors
* http://postgis.net/docs/manual-2.4/reference.html#Geometry_Constructors
* we used ST_MakePoint already - 2D,3DZ or 4D is possible http://postgis.net/docs/manual-2.4/ST_MakePoint.html

ST_GeomFromText - can be used for different geometry types
* http://postgis.net/docs/manual-2.4/ST_GeomFromText.html
* http://postgis.net/docs/manual-2.4/using_postgis_dbmanagement.html#OpenGISWKBWKT

```sql
Update pois set geom = ST_GeomFromText('POINT(50.941357 6.958307)',4326) where name = 'Cologne Cathedral';
```

```sql
Update ne_10m_admin_0_countries set geom = ST_GeomFromText('MULTIPOLYGON(((0 0,4 0,4 4,0 4,0 0),(1 1,2 1,2 2,1 2,1 1)), ((-1 -1,-1 -2,-2 -2,-2 -1,-1 -1)))',4326) where name = 'Germany';
```

## Spatial Relationships and Measurements

* get information about your data f.e. area, length, centroid


### Excercise 7: Calculate the area for each country

* http://postgis.net/docs/manual-2.4/ST_Area.html
* Note that the area is calculated using the srid of the geometry. Use the calculation on the spheroid to get the r result in meters.

```sql
SELECT gid, name, st_Area(geom)
  FROM public.ne_10m_admin_0_countries;
```

```sql
SELECT gid, name, st_Area(geom, true)
  FROM public.ne_10m_admin_0_countries;
```


### Excercise 8: Create a view with the centroid for each country

* Create a view with the centroid for each province
* try to load the view in QGIS
* have a look at your geometry_columns view

```sql
Create view qry_country_centroid AS
SELECT gid, name, st_centroid(geom)
  FROM public.ne_10m_admin_0_countries;
```

Recreate the view and typecast your geometry column to point 

```sql
Drop view qry_country_centroid;
Create view qry_country_centroid AS
SELECT gid, name, st_centroid(geom)::geometry(point,4326) as geom
  FROM public.ne_10m_admin_0_countries;
```


## Spatial Index and functional Index

* Your geometry column should have an index - makes spatial queries faster
* the bounding box for every geometry will we stored in the index 
* you also can create a functional index - f.e. with ST_Transform

```sql
CREATE INDEX gist_pois_geom
ON pois 
USING GIST (ST_Transform(geom,25832));
```

## Geometry Processing

* There are many functions for geometry processing f.e. buffering, intersection, union, subdivide
* http://postgis.net/docs/manual-2.4/reference.html#Geometry_Processing

### Exercise 9: Buffer populated places with 10 km

* buffer the table ne_10m_populated_places with 10 km
* http://postgis.net/docs/manual-2.4/ST_Buffer.html
* note that you have to use geography to create a buffer in meter - use typecast ::geography

```sql
CREATE TABLE places_buffer_10_km as
SELECT gid, name, ST_Buffer(geom::geography, 10000)::geometry(polygon,4326) as geom 
  FROM public.ne_10m_populated_places;

```sql
Select a.* from places_buffer_10_km a, places_buffer_10_km b
where a.geom && b.geom AND st_intersects(a.geom, b.geom) and a.gid != b.gid
```

```sql
Create index gist_places_buffer_10_km_geom
ON places_buffer_10_km 
USING GIST (geom);
```

Run the query again and check whether the index is used.


### Exercise 10: Union all 19 provinces from country Portugal to one area 

* create a view called qry_portugal_union
* use ST_UNION http://postgis.net/docs/manual-2.4/ST_Union.html
* use table ne_10m_admin_1_states_provinces_shp and filter by admin Portugal
* add column admin to your view (contains Portugal) - you have to use GROUP BY 
* typecast the geomety column 
* have a look at your result with QGIS


```sql
SELECT ST_UNION(geom)
  FROM public.ne_10m_admin_1_states_provinces_shp WHERE admin = 'Portugal';
```

```sql
SELECT 1 as gid, admin, st_AsText(ST_UNION(geom))
  FROM public.ne_10m_admin_1_states_provinces_shp WHERE  admin = 'Portugal'
  GROUP BY admin ;
```

```sql
CREATE VIEW qry_portugal_union AS
SELECT 1 as gid, admin, ST_UNION(geom)::geometry(multipolygon,4326) as geom
  FROM public.ne_10m_admin_1_states_provinces_shp WHERE  admin = 'Portugal'
  GROUP BY admin ;
```

### ST_Subdivide

* Divides a Multi-/Polygon in multiple smaller Polygons
* define max_vertices (default is 256, can't be < 8)
* Objekt should not have more than max_vertices
* http://postgis.net/docs/manual-2.4/ST_Subdivide.html
* from PostGIS 2.3.0

```sql
Create Table provinces_subdivided AS 
Select name, admin, st_subdivide(the_geom) as  the_geom
from ne_10m_admin_1_states_provinces_shp ;
ALTER TABLE provinces_subdivided ADD COLUMN gid serial PRIMARY KEY;
```

```sql
CREATE INDEX provinces_subdivided_the_geom_gist
  ON public.provinces_subdivided
  USING gist
  (the_geom);
```

* with definition of max_vertices (default is 256, not < 8)

```sql
Create Table provinces_subdivided AS 
Select name, admin, st_subdivide(geom,20) as geom
from ne_10m_admin_1_states_provinces_shp ;
ALTER TABLE provinces_subdivided ADD COLUMN gid serial PRIMARY KEY;
VACUUM ANALYZE;
```

### Example 11: ST_Subdivide

* sometimes it makes sense to divide huge geometries in smaler parts for calculations
* this example should show the use 
* Create a new function getCountrynameSubdivided() for table provinces_subdivided
* have a look at EXPLAIN to check the performance

```sql
CREATE OR REPLACE FUNCTION getCountrynameSubdivided(mygeometry geometry) 
RETURNS character varying 
 AS 'SELECT c.name from provinces_subdivided c where st_intersects(c.geom,$1);' 
LANGUAGE 'sql'; 
```

```sql
Select getCountrynameSubdivided(geom) from 
public.ne_10m_populated_places where gid < 5;
```

```sql
ALTER TABLE ne_10m_populated_places ADD COLUMN countryname varchar;
Update ne_10m_populated_places set countryname = getCountrynameSubdivided(geom);
```

