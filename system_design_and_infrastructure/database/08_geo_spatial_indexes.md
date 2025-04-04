<div align='center'>
  <h1> Geospatial Indexes </h1>
</div>

# Table of Contents

- [About](#about)
  - [Key Features of Geospatial Indexes](#key-features-of-geospatial-indexes)
  - [Applications of Geospatial Indexes](#applications-of-geospatial-indexes)
- [Geohashes](#geohashes)
- [QuadTrees](#quadtrees)
  - [Applications of quadtrees](#applications-of-quadtrees)

# About

Geospatial indexes are specialized data structures designed to efficiently store, query, and retrieve spatial data, such as geographical coordinates, shapes, and points on a map. They are specifically designed to handle queries on geospatial data and run geospatial queries efficiently by indexing data in a way that preserves spatial relationships in two dimensions. 

## Applications of Geospatial Indexes

Applications include locating nearby points of interest, finding objects within a specific geographical area, and calculating distances between spatial entities.

## Key Features of Geospatial Indexes 

- Efficient Query Performance: geospatial indexes allow for rapid querying of spatial data by reducing the search space. For example, a query to find all points within a 10-mile radius of a location can be executed faster with a geospatial index compared to a `full table scan`. They are topically more efficient than creating separate indexes for latitude and longitude, as geospatial indexes preserve the two-dimensional relationships of the data allowing for `spatial queries`.

Geospatial indexing is supported by PostgreSQL, Elasticsearch, MongoDB, and DynamoDB.

# Geohashes

A geohash is a geospatial index used to convert geographic coordinates (latitude and longitude) into a short alphanumeric string. Geohashes use a base-32 encoding system, i.e., each character in the geohash string represents a subdivision of the previous cell into 32 smaller cells. Each character in a geohash represents a specific region or cell on the Earth's surface, with subsequent characters narrowing down the region (more characters means smaller cell and more precise location). Geohashing allows for fast geospatial searches, such as finding nearby points of interest or performing proximity queries, by comparing string prefixes.

# QuadTrees

A QuadTree is a hierarchical data structure (a tree) used to recursively partition a two-dimensional space into four equal-sized quadrants or regions allowing efficient storage, querying, and retrieval of spatial data such as `points, lines, curves, and areas`. 

Why QuadTrees?

QuadTrees are `less CPU-intensive to compute than raw latitude and longitude` data when stored without any spatial indexing structure since in that case it would require a linear search to locate a specific point or to compute distances.

A quadrant with multiple points has four children (a.k.a leaf) nodes used to store spatial information. QuadTrees are particularly useful for range queries, nearest neighbor searches, and collision detection in applications like geographic information systems (GIS), computer graphics, and gaming.

They can be classified into:

- Region quadtree.
- Point quadtree.
- Point-region (PR) quadtree.
- Edge quadtree.
- Polygonal map (PM) quadtree.
- Compressed quadtree.

# Applications of Geohashes and QuadTrees

- Geohashes:
  - Used in location-based services, such as finding nearby drivers (e.g., Uber), restaurants, gas stations, hotels, and ATMs by encoding raw latitude and longitude into an alphanumeric string.
- QuadTrees:
  - Used for spatial partitioning, terrain representation, and multi-resolution image storage in applications such as GIS, mapping, and computer graphics.

Note1: Both Geohash and QuadTree support efficient route calculations (e.g., road networks), but graph-based data structures are more common for pathfinding using [Dijkstra's](https://github.com/camponogaraviera/ds-and-algo/blob/dev/theory/algorithms/06_shortest_path/2_dijkstra.md) or [A*](https://github.com/camponogaraviera/ds-and-algo/blob/dev/theory/algorithms/06_shortest_path/1_aStar.md) algorithms.

Note2: ["A range query is a common database operation that retrieves all records where some value is between an upper and lower boundary."](https://en.wikipedia.org/wiki/Range_query_(database))
