# Experiment: kg-learning

- **Owner:** vishal
- **Started:** 2025-12-29
- **Status:** Graduated
- **Graduated repo:** https://github.com/vishalsachdev/kg-learning

## Goal

Create a knowledge graph-structured repository for learning about knowledge graphs (meta!).

## What Was Built

- **Ontology schema** (`schema/ontology.yaml`) - Entity types (Concept, Article, Author, Category) and relationship types (discusses, written_by, relates_to)
- **Graph data** (`graph/entities.json`, `graph/relationships.json`) - 19 entities, 18 relationships
- **6 Concepts** with detailed markdown files - knowledge-graph, ontology, neuro-symbolic-integration, context-management, uri-identifiers, semantic-layer
- **4 Articles** extracted from Knowledge Graph Guys blog
- **Navigation indexes** - by-concept, by-source, by-category
- **Agent-friendly manifest** (`manifest.json`) - single entry point with learning path

## Key Insights

1. **Dogfooding works** - Using KG principles to organize KG knowledge proved the patterns are practical
2. **Two-audience problem** - Serving both humans (markdown) and AI agents (JSON) requires discipline to keep them in sync
3. **manifest.json pattern** - Pre-computing learning paths reduces agent exploration cost from O(n) to O(1)

## Follow-ups (in graduated repo)

- [ ] Article ingestion workflow (GitHub Issues + Actions + Copilot)
- [ ] Auto-generation of indexes from JSON (single source of truth)
- [ ] Graph visualization
- [ ] More articles from pending list (16 remaining)

## Related

- Connects to context-graphs experiment (graduated to vishalsachdev/context-graphs)
- Both explore AI context management from different angles
