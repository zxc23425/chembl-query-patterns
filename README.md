# README

# ChEMBL Query Patterns (Graph-Based)

## What this shows (quickly)

-   Simple graph queries can reveal **non-obvious structure** in drug–target data
-   Connectivity is **uneven**—some regions support multi-hop reasoning, others don’t
-   A small set of reusable patterns can surface:
    
    -   target similarity
    -   compound similarity
    -   compound selectivity (broad vs narrow)

→ See patterns below for concrete examples

This repository explores a small set of multi-hop query patterns over biomedical data, focusing on how relationship-based queries can be expressed and extended more directly.

The focus is not on a specific implementation (e.g. graph database), but on identifying **useful query structures** that may be relevant to users and could be supported in different ways (SQL views, APIs, UI features, etc.).

Each pattern includes:
- a simple structural representation
- a minimal query
- an example result
- a brief explanation of why it may be useful

## Overview

These patterns demonstrate a common structure—and highlight where that structure is present or absent in real data.

- Gene ↔ Gene via shared drugs
- Drug ↔ Drug via shared targets

This symmetry allows similar query logic to be reused across entity types.

## Pattern 1 — Gene similarity via shared drugs

### Structure

Gene ← Drug → Gene

### Query

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

### Example result

| gene  | neighbor  | sharedDrugs |
| ------ | ------ | ------------ |
| GABRA1 | GABRG2 | 9            |
| ...    | ...    | ...          |

### Why it’s useful

Identifies genes that are targeted by similar compounds, which reflects shared pharmacological context in the data.

### How it can be extended

* apply thresholds on shared drug count
* restrict to subsets of compounds (e.g. approved drugs)
* incorporate confidence or activity filters

### Product interpretation

“Show similar targets based on shared compounds.”


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

| drug1          | drug2      | shared_targets |
| -------------- | ---------- | -------------- |
| PHLOROGLUCINOL | SULOCTIDIL | 26             |
| ...            | ...        | ...            |

### Why it’s useful

Identifies compounds that act on similar biological targets, which can suggest overlapping target profiles or potential alternatives.

### How it can be extended

* increase overlap thresholds
* restrict to specific target classes
* include activity or confidence measures

### Product interpretation

“Show similar compounds based on shared targets.”

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

| drug | targetCount |
| --- | --- |
| CITATUZUMAB BOGATOX | 78  |
| ... | ... |

### Observation

Target coverage is highly uneven—few drugs connect broadly, most are narrow.

### Why it matters

Helps distinguish selective vs broad compounds and highlights where downstream analysis may be non-specific.

## Notes

* These examples are derived from a subset of data containing Gene, Drug, and Protein entities.
* Multi-hop patterns involving proteins were explored but did not yield meaningful results in this subset.
* The queries are intended as **specifications of useful questions**, rather than proposed implementations.

## Observations on data structure

Applying these patterns to a real subset highlights an important constraint:

-   Connectivity is uneven across the graph
-   Some regions form dense clusters (e.g. receptor families)
-   Many genes have no shared-drug connections

This means:

-   multi-hop queries are highly effective in certain regions
-   but return no results in sparse areas

**Implication**

These patterns are not just queries—they can also be used to identify:

-   where relational reasoning is possible
-   where additional data or integration is needed

> In practice, these patterns can be used not only to query data, but to assess where a dataset supports meaningful multi-hop exploration.
