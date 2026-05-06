## Pattern 3 — Ranking drugs by target breadth

### Structure

Drug → Gene

### Query

```cypher
MATCH (d:Drug)-[:TARGETS]->(g:Gene)
WITH d, count(DISTINCT g) AS targetCount
WHERE targetCount > 5
RETURN d.name AS drug, targetCount
ORDER BY targetCount DESC
LIMIT 20;
```

### Example result

|       drug                  |      targetCount       |
| ----------------------- | ----------- |
| CITATUZUMAB BOGATOX     | 78          |
| BORTEZOMIB D-MANNITOL   | 38          |
| PHLOROGLUCINOL          | 26          |
| SULOCTIDIL              | 26          |
| ACAMPROSATE CALCIUM     | 23          |
| METHOHEXITAL            | 16          |
| PENTOBARBITAL           | 16          |
| GANAXOLONE              | 16          |
| PRAZEPAM                | 16          |
| REMIMAZOLAM             | 16          |
| ALPRAZOLAM              | 16          |
| CLORAZEPATE DIPOTASSIUM | 16          |
| OMBRABULIN              | 15          |
| INDATUXIMAB RAVTANSINE  | 15          |
| VINFLUNINE              | 15          |
| BELANTAMAB MAFODOTIN    | 15          |
| IXABEPILONE             | 15          |
| MIDOSTAURIN             | 14          |
| ERGOLOID                | 12          |
| ETHOXZOLAMIDE           | 12          |

### Observation

Target coverage is highly uneven—few drugs connect broadly, most are narrow.

### Why it matters

Helps distinguish selective vs broad compounds and highlights where downstream analysis may be non-specific.