# README

---

# ChEMBL Query Patterns (Graph-Based)

This repository contains a small set of query patterns exploring how multi-hop relationships in ChEMBL-like data can be expressed and extended more directly.

The focus is not on a specific implementation (e.g. graph database), but on identifying **useful query structures** that may be relevant to users and could be supported in different ways (SQL views, APIs, UI features, etc.).

Each pattern includes:
- a simple structural representation
- a minimal query
- an example result
- a brief explanation of why it may be useful

---

## Overview

These patterns demonstrate a common structure applied in different directions:

- Gene ↔ Gene via shared drugs
- Drug ↔ Drug via shared targets

This symmetry allows similar query logic to be reused across entity types.

---

## Pattern 1 — Gene similarity via shared drugs

### Structure

Gene ← Drug → Gene

### Query

```cypher
MATCH (g1:Gene)<-[:TARGETS]-(d:Drug)-[:TARGETS]->(g2:Gene)
WHERE g1 <> g2 AND g1.symbol < g2.symbol
WITH g1.symbol AS gene1, g2.symbol AS gene2, count(DISTINCT d) AS shared_drugs
RETURN gene1, gene2, shared_drugs
ORDER BY shared_drugs DESC
LIMIT 10
```

### Example result

| gene1  | gene2  | shared_drugs |
| ------ | ------ | ------------ |
| SCN10A | SCN11A | 9            |
| ...    | ...    | ...          |

### Why it’s useful

Identifies genes that are targeted by similar compounds, which can indicate shared biological roles or overlapping pharmacology.

### How it can be extended

* apply thresholds on shared drug count
* restrict to subsets of compounds (e.g. approved drugs)
* incorporate confidence or activity filters

### Product interpretation

“Show similar targets based on shared compounds.”

---

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

| drug1 | drug2 | shared_targets |
| ----- | ----- | -------------- |
| ...   | ...   | ...            |

### Why it’s useful

Identifies compounds that act on similar biological targets, which can suggest shared mechanisms or potential alternatives.

### How it can be extended

* increase overlap thresholds
* restrict to specific target classes
* include activity or confidence measures

### Product interpretation

“Show similar compounds based on shared targets.”

---

## Notes

* These examples are derived from a subset of data containing Gene, Drug, and Protein entities.
* Multi-hop patterns involving proteins were explored but did not yield meaningful results due to sparse connectivity in this subset.
* The queries are intended as **specifications of useful questions**, rather than proposed implementations.

