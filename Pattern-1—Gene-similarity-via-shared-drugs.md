## Pattern 1 — Gene similarity via shared drugs

### Structure
Gene ← Drug → Gene

---

#### Query (aggregated)
```cypher
MATCH (g1:Gene)<-[:TARGETS]-(d:Drug)-[:TARGETS]->(g2:Gene)
WHERE g1 <> g2
WITH g1.symbol AS gene1, g2.symbol AS gene2, count(DISTINCT d) AS shared_drugs
WHERE shared_drugs >= 3
RETURN gene1, gene2, shared_drugs
ORDER BY shared_drugs DESC
LIMIT 10
```

---

#### Example result
| gene1  | gene2  | shared_drugs |
| ------ | ------ | ------------ |
| SCN10A | SCN11A | 9            |
| GABRA1 | GABRB1 | 9            |
| GABRB1 | GABRG2 | 9            |
| GABRG2 | GABRB1 | 9            |
| GABRB1 | GABRA1 | 9            |
| SCN11A | SCN10A | 9            |
| GABRG2 | GABRA1 | 9            |
| GABRA1 | GABRG2 | 9            |
| ADRA2C | ADRA2A | 8            |
| GABRB2 | GABRA1 | 8            |

### Why it’s useful
Identifies genes that are targeted by similar compounds, which can indicate:

-   related biological function
-   shared pharmacological profiles
-   potential substitution or redundancy in targeting

* * *

### How it can be extended

-   increase overlap threshold (≥5, ≥10)
-   restrict to subsets (e.g. approved drugs, high-confidence interactions)
-   rank by proportion instead of count

* * *

### Product interpretation

> "Show similar targets based on shared compounds, with adjustable overlap threshold."