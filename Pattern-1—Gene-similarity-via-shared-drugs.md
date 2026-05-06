## Pattern 1 — Gene similarity via shared drugs

### Structure
Gene ← Drug → Gene

---

#### Query

```cypher
MATCH (g1:Gene)<-[:TARGETS]-(d:Drug)-[:TARGETS]->(g2:Gene)
WHERE g1 <> g2

WITH g1, g2, count(DISTINCT d) AS sharedDrugs
WHERE sharedDrugs >= 2

RETURN g1.symbol AS gene,
       g2.symbol AS neighbor,
       sharedDrugs
ORDER BY sharedDrugs DESC
LIMIT 20;
```

---

#### Example result
|        |          |             |
| ------ | -------- | ----------- |
| gene   | neighbor | sharedDrugs |
| GABRA1 | GABRG2   | 9           |
| SCN11A | SCN10A   | 9           |
| SCN10A | SCN11A   | 9           |
| GABRB1 | GABRA1   | 9           |
| GABRG2 | GABRA1   | 9           |
| GABRA1 | GABRB1   | 9           |
| GABRB1 | GABRG2   | 9           |
| GABRG2 | GABRB1   | 9           |
| GABRG3 | GABRA1   | 8           |
| GABRB2 | GABRA1   | 8           |
| GABRQ  | GABRA1   | 8           |
| ADRA2B | ADRA2C   | 8           |
| GABRB3 | GABRA1   | 8           |
| GABRA4 | GABRA1   | 8           |
| GABRA2 | GABRA1   | 8           |
| GABRD  | GABRA1   | 8           |
| ADRA2A | ADRA2B   | 8           |
| GABRP  | GABRA1   | 8           |
| GABRG1 | GABRA1   | 8           |
| ADRA2C | ADRA2B   | 8           |

### Why it’s useful
-   Identifies genes that are targeted by similar compounds, which reflects shared pharmacological context in the data.

* * *

### How it can be extended

-   apply thresholds on shared drug count
-   restrict to subsets of compounds (e.g. approved drugs)
-   incorporate confidence or activity filters

* * *

### Product interpretation

> "Show similar targets based on shared compounds, with adjustable overlap threshold."