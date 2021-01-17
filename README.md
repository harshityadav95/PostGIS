# PostGIS
 Learning PostGIS
Geometry Data Types
-Point (Long , Lat) (google map use opposite)
-Line
-Polygon
- (Multi Geometric)
- (Geometry Collections)
# Task 1 - Introduction

SELECT * 
FROM listings;

# Task 2 - Basic SQL

-- Statement-1

SELECT name, host_neigh, price
FROM listings;

-- Statement-2

SELECT name, host_neigh, price
FROM listings
LIMIT 10;

-- Statement-3

SELECT name, host_neigh, price
FROM listings
ORDER BY price
LIMIT 10;

-- Statement-4

SELECT name, host_neigh, price
FROM listings
WHERE host_neigh = 'El Raval';


-- Statement-5

SELECT host_neigh, count(*) AS TOTAL
FROM listings
GROUP BY host_neigh;
ORDER BY TOTAL;

-- Statement-6

SELECT host_neigh, count(*) AS TOTAL
FROM listings
GROUP BY host_neigh;
ORDER BY TOTAL DESC;


# Task 3: Geometries - Points

-- Statement-1
Select ST_MakePoint(11.965339, 57.703805);

-- Statement-2
Select ST_AsText(ST_MakePoint(11.965339, 57.703805));

-- Statement-3
Select ST_SetSRID(ST_MakePoint(11.965339, 57.703805), 4326);

# Task 4: Geometries - Line & Polygon

-- Statement-1
SELECT ST_MakeLine(
    ST_MakePoint(x, Y),
    ST_MakePoint(X, Y)
    );

-- Statement-2
SELECT ST_SetSRID(
    ST_MakeLine( 
        ST_MakePoint(x, Y),
        ST_MakePoint(X, Y)
    ), 4326)

-- Statement-3
SELECT ST_SetSRID(ST_MakePolygon(ST_MakeLine(ARRAY [ST_MakePoint(11.958241, 57.704103),
ST_MakePoint(11.959550, 57.704718),
ST_MakePoint(11.960324, 57.704211),
ST_MakePoint(11.959102, 57.703686),
ST_MakePoint(11.958241, 57.704103)
])), 4326);


# Task 5: Set Geometries of a table to

-- Statement-1
-- create a gemoetry column (table name,name of column , spatial refernce system , type of temoetry )
-- if altitutude add 3



SELECT AddGeometryColumn ('listings','geom',4326,'POINT',2);


-- Statement-2

-- 

UPDATE listings
SET geom = ST_SetSRID(ST_MakePoint(longitude, latitude), 4326);

-- Statement-3

SELECT name, ST_AsText(geom)
from listings

-- Statement-4

SELECT ST_Distance(ST_GeometryFromText('POINT(2.22063 41.41291)'),ST_GeometryFromText('POINT(2.1511 41.3921)')); 

-- Distance not in meters

-- Statement-5

SELECT ST_Distance(ST_GeometryFromText('POINT(2.22063 41.41291)
')::geography,ST_GeometryFromText('POINT(2.1511 41.3921)')::geography);

-- Distance in meters

# Task 6: Spatial Relationships

-- Statement-1 - return 5 closes Airbnb

SELECT name, geom
FROM listings
ORDER BY ST_Distance(geom, ST_SetSRID(ST_MakePoint(2.121275,41.380775),4326))
LIMIT 5;

-- Statement-2
-- close facilities in distance 200m


SELECT name, geom
FROM listings
WHERE ST_DWithin(geom::geography, ST_SetSRID(ST_MakePoint(2.121275,41.380775),4326)::geography, 200)


# Task 7: Spatial Join

-- Statement-1

SELECT *
FROM stat_polygon

-- Statement-2

SELECT ST_SRID(geom)
FROM stat_polygon
LIMIT 1;

-- Statement-3
SELECT l.name AS l,
       l.room_type AS room,
       l.price AS Price
FROM listings AS l

-- Statement-4
SELECT l.name AS l,
       l.room_type AS room,
       l.price AS Price,
	     s.aeb AS code
FROM listings AS l
JOIN stat_polygon AS s
ON ST_Within(l.geom, s.geom)


-- Statement-5
SELECT  COUNT(*) AS NUM,
s.aeb AS code
FROM listings AS l
JOIN stat_polygon AS s
ON ST_Within(l.geom, s.geom)
GROUP BY s.aeb
