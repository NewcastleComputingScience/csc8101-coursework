# 1st Neo4j Practical - 14 Feb 2023

## Neo4j server details

- url: http://csc8101-neo4j-task0.uksouth.cloudapp.azure.com:7474/browser/
- Neo4j Connect URL: `neo4j://csc8101-neo4j-task0.uksouth.cloudapp.azure.com:7687`
- Username: `neo4j`
- Password: `neo4j`

## Tasks

### Initial exploration

Determine:

1. what labels and relationships exist;
2. what properties each node label and relationship can have;

### Counting

Determine the number of:

1. zones;
2. boroughs;
3. relationships between zones;
4. relationships between zones and boroughs;
5. zones within the `Manhattan` borough

### Matching

Find:

1. all unique borough names;
2. all zone pairs with at least 5000 daily trips (return the names of the origin and destination zones and number of trips sorted in ascending order)
3. the top 5 most popular origin-destination pairs (return the names of the origin and destination zone and number of trips);
4. the top 5 most popular origin-destination pairs within Brooklyn (return the names of the origin and destination zone and number of trips);
5. the total number of trips per origin borough.

