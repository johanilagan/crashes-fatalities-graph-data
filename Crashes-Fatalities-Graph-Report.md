# 📊 Graph Database Modelling: Crashes and Fatalities in Australia

## 🎨 Design and Implementation Process

Graph data modelling is a method of organizing data that emphasizes the relationships between entities [1]. It uses nodes to represent entities, which can also contain properties to help categorize or filter the data. Relationships, on the other hand, connect these nodes together and are assigned clear, descriptive labels. Graph data modelling allows efficient querying by enabling operations that naturally traverse the relationships between data items. One of the most common graph data models is the property graph model, which follows the principle that the logical structure should easily reflect the data and its relationships [2].

The dataset used in this report contains information about crashes that occurred over the past ten years. It includes details about the crash setting, the individuals involved, outcomes, and more. By applying graph data modelling, the aim is to answer business-related queries by using the relationships and node structures within the dataset, executed through Cypher queries.

The initial stage involves defining the domain [3]. These include all relevant entities that need to be represented in the model. In this crash dataset, entities can be classified into five categories:
- ***Crashes***
- ***People***
- ***Location***
- ***Datetime***
- ***Vehicles Involved***

Two example queries:
1. For each age group, how many crashes occurred in WA on roads with a speed limit of 100 or higher during weekdays?
2. For each road type in NSW, how many fatal crashes involving multiple fatalities occurred in the post-COVID period (2023-2024)? Also, distinguish between day and night.

### 💪 Strengths and Limitations

**Strengths:**
- Straightforward and interpretable
- Clear node/entity definitions
- Business-oriented relationship names

**Limitations:**
- Centralized around Crash node
- Limited advanced relationship modeling

---

## ⏯️ Extract, Transform, and Load (ETL) Process

Performed in Jupyter Notebook using pandas:

```python
import pandas as pd
df = pd.read_csv('Project2_Dataset_Corrected.csv')
duplicates = df[df.duplicated()]
```

No duplicates were found. Data types were converted for clarity. Column renaming example:

```python
df = df.rename(columns={
    'Crash ID': 'crash_id',
    'State': 'state',
    'Month': 'month',
    ...
    'Christmas Period': 'christmas_period',
    'Easter Period': 'easter_period'
})
```

### Node CSV Generation Example

```python
location_df = df[["state", "remoteness_area", "sa4_name", "lga_name", "road_type"]].drop_duplicates()
location_df["location_id"] = ["L" + str(i) for i in range(1, 1 + len(location_df))]
```

### Relationship Generation Example

```python
rel_involved = df[['crash_id', 'person_id']]
```

### CSV Export

```python
for i in range(len(csv_name)):
    dfs[i].to_csv(csv_name[i], index=False)
```

---

## 📊 Graph Database Implementation

CSV files were loaded into Neo4j using:

```cypher
// Node import
LOAD CSV WITH HEADERS FROM 'file:///{file_node}.csv' AS row
CREATE (n:{Node}) SET n.property = row.property

// Relationship import
LOAD CSV WITH HEADERS FROM 'file:///{file_rel}.csv' AS row
MATCH (a:{Node1} {{id: row.id1}}), (b:{Node2} {{id: row.id2}})
CREATE (a)-[:REL_TYPE]->(b)
```

---

## ❓ Answering Business Queries

<details>
<summary>1. <strong>Articulated trucks with multiple fatalities in WA (2020–2024)</strong></summary>

<br>

***Query***: *Find all crashes in WA from 2020-2024 where articulated trucks were involved and multiple fatalities (Number Fatalities>1) occurred. For each crash, provide the road user, age of each road user, gender of each road user, LGA Name, month and year of the crash, and the total number of fatalities.*

***Result***: 449 matching records returned.
</details>

The query identifies all crashes in WA from 2020 to 2024 that involved articulated trucks and resulted in multiple fatalities. The Cypher query applies these filters and retrieves the relevant information for each crash. The query successfully returns 449 records, each satisfying all specified conditions. While some rows may appear similar or even identical, this is expected. Multiple individuals may be involved in the same crash, resulting in duplicate information but different person-level attributes th...

<br>

<details>
<summary>2. <strong>Age range of male/female motorcycle riders during holiday periods in inner regional Australia</strong></summary>

<br>

***Query***: Find the maximum and minimum age for female and male motorcycle riders who were involved in fatal crashes during the Christmas Period or Easter Period in inner regional Australia. Output the following information: gender, maximum age and minimum age.

***Result***: Only male results returned, suggesting no female matches.
</details>

The query identifies the maximum and minimum ages of male and female motorcycle riders who were involved in crashes during either the Christmas Period or Easter Period, specifically in Inner Regional Australia. It filters the data based on the required conditions and then groups the data by gender and returns the maximum and minimum age for each group.

The query returns results only for the male gender, indicating that there are no records matching all conditions where the gender is ‘Female’. This suggests that within the given conditions, no female motorcycle riders were involved in fatal crashes that occurred during these holiday periods in inner regional areas.

<br>

<details>
<summary>3. <strong>Young drivers (17–25) crash breakdown by weekday/weekend by state (2024)</strong></summary>

<br>

***Query***: How many young drivers (Age Group = '17_to_25') were involved in fatal crashes on weekends vs. weekdays in each state during 2024? Output 4 columns: State name, weekends, weekdays, and the average age for all young drivers (Age Group = '17_to_25') who were involved in fatal crashes in each State.

***Result***: State, weekday/weekend counts, and average age.
</details>

The query determines how many young drivers in age groups 17 to 25 were involved in fatal crashes during weekdays and weekends across each state in 2024. The query first applies filters based on the required conditions. It then uses the WITH clause to prepare values for aggregation. To get the number of weekday and weekend crashes, a CASE WHEN clause is applied inside a SUM() function. The query calculates the average age of the young drivers using the avg() function. The final output includes four column...

<br>

<details>
<summary>4. <strong>Friday crashes in WA (categorized as weekend), multiple deaths, both genders</strong></summary>

<br>

***Query***: Identify all crashes in WA that occurred Friday (but categorised as a weekend) and resulted in multiple deaths, with victims being both male and female. For each crash, output the SA4 name, national remoteness areas, and national road type.

***Result***: 2 crashes found with expected criteria.
</details>

This query identifies all crashes in WA that occurred on a Friday (but were classified as a weekend) and involved multiple fatalities, with victims of both male and female genders. The query first filters the data using the required conditions. A second WHERE clause is then applied to ensure that both ‘Male’ and ‘Female’ appear in the list of involved genders. The final output includes the SA4 name, national remoteness area, and national road type for each qualifying crash.

<br>

<details>
<summary>5. <strong>Top 5 SA4 regions with highest peak-hour fatalities</strong></summary>

<br>

***Query***: Find the top 5 SA4 regions where the highest number of fatal crashes occur during peak hours (Time between 07:00-09:00 and 16:00-18:00). For each SA4 region, output the name of the region and the separate number of crashes that occurred during morning peak hours and afternoon peak hours (Renamed Morning Peak and Afternoon Peak).
</details>

This query identifies the top five SA4 regions with the highest number of fatal crashes that occurred during peak hours. Crashes are conditionally counted as either ‘Morning Peak’ or ‘Afternoon Peak’, aggregated using SUM(), and sorted by total. The query returns SA4 names and crash counts for both time periods.

<br>

<details>
<summary>6. <strong>Find paths of length 3 between LGAs</strong></summary>

<br>

***Query***: Find paths with a length of 3 between any two LGAs. Return the top 3 paths, including the starting LGA and ending LGA for each path. Order results alphabetically by starting LGA and then ending LGA.
</details>

This query identifies paths of length 3 between any two LGAs. To enable such queries, a [:CONNECTED_TO] relationship was introduced using the following code:

```cypher
MATCH (c1:Crash)-[:INVOLVED_VEHICLE]->(v1:Vehicle),
    (c1)-[:OCCURRED_ON]->(d1:Datetime),
    (c1)-[:OCCURRED_AT]->(l1:Location),
    (c2:Crash)-[:INVOLVED_VEHICLE]->(v2:Vehicle),
    (c2)-[:OCCURRED_ON]->(d2:Datetime),
    (c2)-[:OCCURRED_AT]->(l2:Location)
WHERE c1.crash_type = c2.crash_type
    AND c1.crashID <> c2.crashID
    AND c1.speed_limit = c2.speed_limit
    AND (
      v1.bus_involved = 'True' OR
      v1.articulated_truck_involved = 'True' OR
      v1.heavy_rigid_truck_involved = 'True'
)
AND (
      v2.bus_involved = 'True' OR
      v2.articulated_truck_involved = 'True' OR
      v2.heavy_rigid_truck_involved = 'True'
    )
    AND d1.year = '2024'
    AND d2.year = '2024'
    AND id(l1) < id(l2)
WITH DISTINCT l1, l2
MERGE (l1)-[:CONNECTED_TO]->(l2);
```
Once the :CONNECTED_TO relationships were created, paths of length 3 between LGAs became traversable. The final query successfully returns the top three paths, including the starting LGA, ending LGA, and the complete path ordered alphabetically by LGAs.


<br>

<details>
<summary>7. <strong>Custom Query 1: Crashes in WA on roads ≥100 speed limit during weekdays by age group</strong></summary>

<br>

***Query***: For each age group, how many crashes occurred in WA on roads with a speed limit of 100 or higher during weekdays? Sort the age groups based on the total crash count from highest to lowest.
</details>

The query uses a combination of WHERE, COUNT(DISTINCT ...), and ORDER BY clauses to filter and aggregate the relevant data. The query is looking for crashes in WA that occurred on a weekday and at a road that has a speed limit of 100 or above. It then groups the data by age group and counts the number of distinct crashes that satisfy these conditions. The output successfully returns a ranked list of age groups, showing which was most frequently involved in high-speed weekday crashes in WA.

<br>

<details>
<summary>8. <strong>Custom Query 2: Post-COVID (2023–2024) fatal crashes in NSW by road type and time of day</strong></summary>

<br>

***Query***: For each road type in NSW, how many fatal crashes involving multiple fatalities occurred in the post-COVID period (2023-2024)? Additionally, among these crashes, output how many occurred during the day and how many during the night.
</details>

The query aims to identify, for each road type in NSW, the number of fatal crashes involving multiple fatalities that occurred during the post-COVID period (2023-2024). Additionally, it distinguishes how many of these crashes occurred during the day, and how many during the night.

The query uses CASE WHEN to classify crashes based on the time of day property, separating them into daytime and nighttime crashes. These are aggregated using SUM() to count the number of crashes in each category. The output returns for each road type, the total number of fatal crashes that involve multiple fatalities, the number of crashes that occurred during the day and the number of crashes that occurred during the night.




---

## 🌐 Graph Data Science (GDS)

Graph Data Science (GDS) involves applying graph theory and algorithms to analyze connected data [4]. It emphasizes the importance of relationships just as much as the entities themselves. By leveraging nodes, properties, and relationships, GDS helps uncover hidden patterns and structures that may not be immediately apparent through traditional data analysis methods.

```cypher
MATCH
  (c:Crash)-[:OCCURRED_AT]->(l:Location),
  (c)-[:OCCURRED_ON]->(d:Datetime),
  (c)-[:INVOLVED]->(p:Person)
WHERE d.year IN ['2023','2024']
  AND toInteger(c.number_fatalities) > 1
  AND l.state = 'NSW'
WITH l.road_type AS roadType, d.time_of_day AS timeOfDay
WITH roadType,
  SUM(CASE WHEN timeOfDay = 'Day' THEN 1 ELSE 0 END) AS daytimeCrashes,
  SUM(CASE WHEN timeOfDay = 'Night' THEN 1 ELSE 0 END) AS nighttimeCrashes,
  COUNT(*) AS totalCrashes
RETURN roadType, totalCrashes, daytimeCrashes, nighttimeCrashes
```

In the context of the crash dataset used in this report, GDS can help identify locations that play a key role in the network of fatal crashes. Even if these locations don't have the highest number of crashes, they may serve as critical links between other high-risk areas. By identifying these locations, targeted upgrades and interventions can be implemented to improve road safety.

One particularly useful algorithm for this type of analysis is Breadth-First Search (BFS). BFS is a graph traversal algorithm that begins at a specific node and explores all adjacent nodes, repeating the process layer by layer [5]. It can be used to find the shortest paths, detect cycles, and reveal hidden structures and patterns in the data. When applied to the crash dataset, BFS enables exploration of spatial and relational proximity — helping identify locations a few steps away from crash-prone areas.

This method offers a deeper insight that may not be evident through standard analysis, allowing for a broader perspective on crash networks. It can guide proactive measures, such as improving infrastructure or deploying warnings, to reduce the likelihood of fatal crashes.

---

### References

[1] “What Are Graph Models?,” Hypermode, 2022. [Online]. Available:
https://hypermode.com/blog/graph-models

[2] J. Webber, “RDF vs. Property Graphs: Choosing the Right Approach for Implementing a Knowledge Graph,” Neo4j Blog, June 4, 2024. [Online]. Available: https://neo4j.com/blog/knowledge-graph/rdf-vs-property-graphs-knowledge-graphs/

[3] “Neo4j Documentation – Data Modeling Tutorial”, Neo4j, 2024. [Online]. Available:
https://neo4j.com/docs/getting-started/data-modeling/tutorial-data-modeling/ 

[4] “ChatGPT,” OpenAI, 2025. [Online]. Available: https://chat.openai.com/

[5] “Breadth-First Search or BFS for a Graph,” GeeksforGeeks, April 21, 2025. [Online]. Available: https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/#what-is-breadth-first-sea rch
