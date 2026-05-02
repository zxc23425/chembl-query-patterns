## Pattern 2 — Drug similarity via shared targets

### Structure

Drug → Gene ← Drug

### Query

```cypher
MATCH (d1:Drug)-[:TARGETS]->(g:Gene)<-[:TARGETS]-(d2:Drug)
WHERE d1 <> d2 AND d1.name < d2.name
WITH d1.name AS drug1, d2.name AS drug2, count(DISTINCT g) AS shared_targets
RETURN drug1, drug2, shared_targets
ORDER BY shared_targets DESC
LIMIT 10
```

### Example result

| drug1                   | drug2                   | shared_targets |
| ----------------------- | ----------------------- | -------------- |
| PHLOROGLUCINOL          | SULOCTIDIL              | 26             |
| CLORAZEPATE DIPOTASSIUM | METHOHEXITAL            | 16             |
| CLORAZEPATE DIPOTASSIUM | GANAXOLONE              | 16             |
| ACAMPROSATE CALCIUM     | CLORAZEPATE DIPOTASSIUM | 16             |
| ACAMPROSATE CALCIUM     | METHOHEXITAL            | 16             |
| GANAXOLONE              | METHOHEXITAL            | 16             |
| ALPRAZOLAM              | CLORAZEPATE DIPOTASSIUM | 16             |
| ACAMPROSATE CALCIUM     | GANAXOLONE              | 16             |
| ALPRAZOLAM              | GANAXOLONE              | 16             |
| ALPRAZOLAM              | METHOHEXITAL            | 16             |

### Why it’s useful

Identifies compounds that act on similar biological targets, which can suggest shared mechanisms or potential alternatives.

### How it can be extended

-   increase overlap thresholds
-   restrict to specific target classes
-   include activity or confidence measures

### Product interpretation

> “Show similar compounds based on shared targets.”