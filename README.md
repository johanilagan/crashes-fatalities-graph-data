# 🚧 Solving Business Queries using Cypher and Graph Database Modelling on the Crashes and Fatalities in Australia

This project uses a graph database approach to explore road crash data from Australia and answer complex business-related queries. By modelling data in Neo4j using the property graph model, we efficiently analyze relationships between crashes, people, vehicles, dates, and locations.

---

## 📊 Objectives

- Convert crash data into a graph model (nodes and relationships).
- Load data into Neo4j using CSV files and Cypher.
- Answer 8 key business queries (plus 2 custom ones) using Cypher queries.
- Apply Graph Data Science (GDS) techniques like Breadth-First Search (BFS) for deeper insights.

---

## 🧱 Graph Model Structure

- **Nodes**: `Crash`, `Location`, `Person`, `Vehicle`, `Datetime`
- **Relationships**:
  - `:INVOLVED`
  - `:OCCURRED_ON`
  - `:OCCURRED_AT`
  - `:INVOLVED_VEHICLE`
  - `:CONNECTED_TO` (custom for path traversal)

---

## 📂 Project Structure

```
graph-crash-analysis/
│
├── 📁 data/
│   ├── processed/             
│   │      ├── crash.csv                     
│   │      ├── datetime.csv
│   │      ├── location.csv
│   │      ├── person.csv
│   │      ├── rel_involved.csv
│   │      ├── rel_involvedVehicle.csv             
│   │      ├── rel_occurredAt.csv             
│   │      ├── rel_occurredOn.csv             
│   │      └── vehicle_type.csv             
│   ├── raw/             
│   │      └── Project2_Dataset_Corrected.csv             
│   ├── scripts/             
│   │      ├── cypher-scripts.txt                     
│   │      ├── etl.ipynb
├── 📄 Crashes-Fatalities-Graph-Report.md      # Full write-up of queries and GDS analysis
└── 📄 README.md    # Project overview (this file)
```

---

## 🛠️ How to Run

### 1. Clone this repository:
```bash
git clone https://github.com/yourusername/graph-crash-analysis.git
cd graph-crash-analysis
```

### 2. Set up Neo4j:
- Use [Neo4j Desktop](https://neo4j.com/download/) or [AuraDB](https://neo4j.com/cloud/aura/).
- Create a new database project.

### 3. Import data:
- Use the Cypher scripts in `cypher_queries.cypher` to:
  - Create nodes from filtered CSVs.
  - Create relationships between nodes.
  - Run analysis queries.

### 4. (Optional)
- Open `etl_pipeline.ipynb` to regenerate or modify filtered CSVs from the raw crash data.

### 5. View full analysis and report in `Crash_Fatalities_Complete_Report.md`


---

## 📌 Key Features

- 🔍 Answer complex questions involving time, geography, gender, age group, road type, etc.
- 🔄 Reusable ETL workflow for transforming relational data into graph format.
- 🧠 Graph Data Science (GDS) with BFS to uncover hidden relationships between high-risk locations.

---

## 📑 Sample Queries

Some example queries tackled:

- Find all crashes in WA from 2020-2024 where articulated trucks were involved and multiple fatalities (Number Fatalities>1) occurred. For each crash, provide the road user, age of each road user, gender of each road user, LGA Name, month and year of the crash, and the total number of fatalities.
- Identify all crashes in WA that occurred Friday (but categorised as a weekend) and resulted in multiple deaths, with victims being both male and female. For each crash, output the SA4 name, national remoteness areas, and national road type.
- For each age group, how many crashes occurred in WA on roads with a speed limit of 100 or higher during weekdays? Sort the age groups based on the total crash count from highest to lowest.

View full results and discussion in [`Crashes-Fatalities-Graph-Report.md`](./Crashes-Fatalities-Graph-Report.md).

---

## 📌 License

This project uses public datasets and complies with the **Copyright Act 1968** and the [CC BY 4.0 License](https://creativecommons.org/licenses/by/4.0/).

---

## 🙋🏻‍♂️ Author

**Johan Carlo Ilagan** 
- [GitHub](https://github.com/johanilagan)
- [LinkedIn](www.linkedin.com/in/johan-ilagan)
  
Created as part of a data warehousing portfolio project.  
