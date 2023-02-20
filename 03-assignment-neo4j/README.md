# CSC8101 - Neo4j Assignment

Contents:

- [Scenario](#scenario)
- [Task details](#task-details)
- [Connection details](#connection-details)

## Scenario

A taxi company operating in New York City (NYC) is rethinking its fleet allocation strategy. Their
restructuring process is motivated by recent changes in passenger travel patterns (provoked by
Covid) and the arrival of new competitors. To minimise costs, the company wants to station its
fleet in a maximum of 20 city zones, spread across the city. At the same time, the company wants to
maximise trips served and therefore choose zones that have hub-like characteristics, i.e. that are
well-connected and near centres of high user activity. Lastly, the company considers operating
purely outside the Manhattan borough where there is less competition.

<center>
  <img src="images/plot_boroughs.png" alt="NYC Zones and Boroughs" width="600"/>
</center>

To help with this task, the taxi company contracts you to analyse its historical trip records and
recommend a list of city zones where they should station their fleets. To that end, the company
gives you a copy of its Neo4j graph database, available here, containing aggregate statistics of
taxi trips undertaken in 2021. There are two types of nodes: `borough` and `zone`. Two zones are
connected by a relationship of type `:CONNECTS` if a minimum of one trip was recorded between
them. In addition, `:CONNECTS` relationships have property `trips` representing the total number of
trips observed in the year. Lastly, a node is related to a borough via a relationship of type `:IN`. 

<center>
  <img src="images/capture_neo4j.png" alt="Subset of input neo4j graph" width="600"/>
</center>

Based on preliminary exploration of the database and the NYC zones and boroughs map (shown above),
you decide to tackle the challenge by combining two methods of _network analysis_ available in the
[Neo4j Data Science Library](https://neo4j.com/docs/graph-data-science/current/). Your action plan
is divided into three parts:

1. Perform [community
   detection](https://neo4j.com/docs/graph-data-science/current/algorithms/community/) to identify
   clusters of strongly connected zones.
2. Perform [centrality
   analysis](https://neo4j.com/docs/graph-data-science/current/algorithms/centrality/) to measure
   the hub-like features of each zone.
3. Combine the results above by identifying the **top 3 zones** with **highest centrality** score within
   each **community** cluster. This processed is done twice for the entire city:
   1. once including Manhattan, and
   2. once excluding Manhattan.

In addition to your Neo4j analysis, you will use the visualisation app below to help interpret the
results of your network analysis and present them to your client (the taxi company). To use the app,
export your results at the end of each stage to a csv file and load it into the app (more details
given in each task).

- http://csc8101-neo4j-shiny.uksouth.cloudapp.azure.com/

## Task details

There are four tasks:

0. Find isolated nodes
1. Compute the community cluster of each node
2. Compute the centrality score of each node
3. Find the top centrality zones within each community

In task 0 you will develop a cypher query to find nodes and relationship of a certain type.

In tasks 1 and 2 you will execute two different algorithms available in the [Neo4j Data Science
Library (GDS)](https://neo4j.com/docs/graph-data-science/current/algorithms/). Both tasks follow the
typical workflow described [here](https://neo4j.com/docs/graph-data-science/current/common-usage/):

- Create an (in-memory) [graph
  projection](https://neo4j.com/docs/graph-data-science/current/common-usage/creating-graphs/).
- Estimate the memory necessary to run the algorithm (optional).
- Run the algorithm in [stats
  mode](https://neo4j.com/docs/graph-data-science/current/common-usage/running-algos/#running-algos-stats)
  to summarise the output of the algorithm.
- Run the algorithm in [stream
  mode](https://neo4j.com/docs/graph-data-science/current/common-usage/running-algos/#running-algos-stream)
  to run the algorithm but not store the results in the original graph.

In task 3 you will develop a cypher query that combines the outcomes of tasks 1 and 2.

Due to database writing restrictions, the coursework is split across three neo4j databases - one for
Task 0, another for tasks 1 and 2 and a third for task 3. Go [here](#connection-details) for connection details.

### Task 0 - Find isolated nodes

Find all:

1. Self-pointing relationships, that is, all relationship instances of type `:CONNECTS` where the
   start node is the same as the end node.
2. Find all isolated nodes, i.e. all nodes that have no relationship instances of type `:CONNECTS`
   to other nodes except possibly to themselves.

> **Important:** Do not delete the nodes/relationships found this way. Execute `MATCH` only queries.

### Task 1 - Community detection

Run the Louvain algorithm for community detection, available in the [GDS
library](https://neo4j.com/docs/graph-data-science/current/algorithms/louvain/), with the following
arguments:

- Graph projection of type `UNDIRECTED`.
- Name the graph projection as `USERNAME-communities` where USERNAME is your student number (this
  is necessary to avoid naming conflicts between students).
- Weighted by the `trips` property in `:CONNECTS` type of relationships.

As specified above, run the algorithm in 2 modes: `stats` and `stream`:

- Report the number of communities using the `stats` mode,
- In stream mode, return the `id` and `community` properties of each zone (in this order),
- Export the results of running the algorithm in `stream` as a CSV file with two columns named `zone_id`, `community_id` and
- Visualise the results in the [app](http://csc8101-neo4j-shiny.uksouth.cloudapp.azure.com/) by
  uploading the produced csv file (right-click to download image).

> **Tip:** See the
[examples page](https://neo4j.com/docs/graph-data-science/1.8/algorithms/page-rank/#algorithms-page-rank-examples).
>
>  **Important:** Do not run the algorithm in `merge` mode.
>
>  **Note:** Isolated nodes have been removed for this task (reflecting the results of task 0).

### Task 2 - Centrality analysis

Run the Page Rank algorithm for centrality analysis, available in the [GDS
library](https://neo4j.com/docs/graph-data-science/current/algorithms/page-rank), with the following
arguments:

- Directed graph projection
- Name the graph projection as `USERNAME-centrality` where USERNAME is your username (this
  is necessary to avoid naming conflicts between students).
- Damping factor: 0.75
- Weighted by the `trips` property in `:CONNECTS` type of relationships.

As specificed above, run the algorithm in 2 modes: `stats` and `stream`:

- Report the maximum and minimum centrality score using the `stats` mode,
- In stream mode, return the `id`, `centrality` properties of each zone (in this order),
- Export the results of running the algorithm in `stream` as a CSV file with two columns named `zone_id` and `centrality_score` and
- Visualise the results in the [app](http://csc8101-neo4j-shiny.uksouth.cloudapp.azure.com/) by
  uploading the produced csv file (right-click to download image).

> **Tip:** See the
[examples page](https://neo4j.com/docs/graph-data-science/1.8/algorithms/page-rank/#algorithms-page-rank-examples).
>
>  **Important:** Do not run the algorithm in `merge` mode.
>
>  **Note:** Isolated nodes have been removed for this task (reflecting the results of task 0).

### Task 3 - Zone recommendation

Find the top 3 highest centrality zones per each community:

1. Including zones in the 'Manhattan' borough. 
2. Excluding zones in the 'Manhattan' borough.

Do this using the available `zone` properties `community` (representing the community
id obtained in 1) and `centrality` (representing the centrality score obtained in 2).

For each case, return two columns: `zone_id` and `community_id` and export the query results to a
csv file. Then, using the visualisation
[app](http://csc8101-neo4j-shiny.uksouth.cloudapp.azure.com/), upload these (one at a time) to the
'Task-3' file input and produce a separate and produce two separate images.

> **Important:** Do not delete any nodes/relationships. Execute `MATCH` queries only.
>
> **Note:** Zone nodes now have two new properties - 'centrality' and 'community' (reflecting the result of tasks 1 and 2).

## Connection details

Neo4j connection details for each task.

### Task 0

- url: http://csc8101-neo4j-task0.uksouth.cloudapp.azure.com:7474/browser/
- Neo4j Connect URL: neo4j://csc8101-neo4j-task0.uksouth.cloudapp.azure.com:7687

### Tasks 1 and 2

- url: http://csc8101-neo4j-task1.uksouth.cloudapp.azure.com:7474/browser/
- Neo4j Connect URL: neo4j://csc8101-neo4j-task1.uksouth.cloudapp.azure.com:7687

### Task 3

- url: http://csc8101-neo4j-task3.uksouth.cloudapp.azure.com:7474/browser/
- Neo4j Connect URL: neo4j://csc8101-neo4j-task3.uksouth.cloudapp.azure.com:7687

### Username and Password

The Neo4j database username and password are the same in all instances:

- username: neo4j
- password: neo4j

### Visualisation App

- url: http://csc8101-neo4j-shiny.uksouth.cloudapp.azure.com/