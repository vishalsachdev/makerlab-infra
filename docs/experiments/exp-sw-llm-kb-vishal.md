# Experiment: exp-sw-llm-kb-vishal
- Owner: vishal
- Date: 2025-12-31
- Branch: exp/sw-llm-kb-vishal
- Worktree: ../sw-llm-kb/
- Goal: Build a queryable knowledge base from Simon Willison's insights on using AI tools effectively

## Context
Simon Willison is a prolific writer on AI/LLM usage and has practical, battle-tested insights from building 110+ tools. His "Year in LLMs" retrospectives are gold mines of techniques, mental models, and frameworks.

## Plan
- [x] Extract insights from 2025 Year in LLMs article
- [ ] Design knowledge base schema (frameworks, techniques, tools, trends, learnings)
- [ ] Build extraction pipeline
- [ ] Create query interface (CLI or web)
- [ ] Expand to more Simon Willison content
- [ ] Possibly expand to other AI thought leaders

## Key Insights Captured
**Frameworks:**
- Lethal Trifecta (prompt injection risk: untrusted content + private data + external communication)
- Context Engineering > Prompt Engineering
- Vibe Coding (forget code exists, embrace LLM prompting for throwaway projects)

**Techniques:**
- Conformance Suites as leverage for agent reliability
- Asynchronous coding agents (YOLO mode)
- Normalization of Deviance (Challenger disaster parallel for AI risk)

**Tools:**
- LLM CLI tool
- tools.simonwillison.net (110+ demos)
- MCP vs Skills format

**Trends:**
- Reasoning models as game-changer (2025)
- Chinese open-weight models leading benchmarks
- $200/month subscription tier emergence

## Changes
- Created orphan branch and worktree structure
- Initial CLAUDE.md with proposed schema
- WebFetch extraction from source article
- Implemented knowledge base structure:
  - Markdown + YAML frontmatter format
  - 5 categories: frameworks, techniques, tools, trends, learnings
  - Shell-based query tool (./query.sh)
- Extracted 4 initial insights:
  - Lethal Trifecta framework
  - Context Engineering mental model
  - Vibe Coding technique
  - Conformance Suites leverage

## Results
✅ **Working knowledge base with query interface**
- Clean orphan branch at ../sw-llm-kb/
- Query by category, tag, or keyword
- Human-readable Markdown, machine-parseable YAML
- Ready to expand with more insights from Simon's writings

## Follow-ups
- Design JSON/YAML schema for insights
- Build search tool (grep-based? sqlite? vector embeddings?)
- Consider automation for extracting from more articles
- Think about how to surface "related insights" when querying
