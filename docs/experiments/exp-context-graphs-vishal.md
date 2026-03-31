# Experiment: Context Graphs

**Branch**: `exp/context-graphs-vishal`
**Worktree**: `../context-graphs`
**Started**: 2025-12-27
**Status**: Graduated to https://github.com/vishalsachdev/context-graphs
**Type**: Research + Prototype

## Hypothesis

Context graphs are the "action layer" of a three-tier intelligence infrastructure:
1. **Knowledge graphs** - semantic layer (what things mean)
2. **Datagraphs** - learning layer (behavioral data with network effects)
3. **Context graphs** - action layer (moment-specific projections for human-machine coordination)

The strategic insight: **context graphs are projections of datagraphs**, not standalone assets. Competitive advantage comes from the underlying datagraph, not the context graph itself.

## Source Materials

### Foundation Capital Essay (Dec 2025)
- [Context Graphs: AI's Trillion-Dollar Opportunity](https://foundationcapital.com/context-graphs-ais-trillion-dollar-opportunity/)
- Authors: Jaya Gupta, Ashu Garg
- Key thesis: Next trillion-dollar platforms will be "systems of record for decisions, not just objects"

**Their framing:**
- Current systems of record (Salesforce, Workday, SAP) own canonical data
- Missing layer: **decision traces** - the reasoning connecting data to action
- Examples of missing context:
  - Exception logic ("we always give healthcare companies 10% extra")
  - Approval chains (VP approves discount on Slack, not in CRM)
  - Tribal knowledge passed through onboarding
- Solution: Instrument agent orchestration to emit decision traces

### Venkatraman Strategic Framework
- Source: [Why Context Graphs Make Datagraphs Strategically Decisive](https://www.linkedin.com/pulse/why-context-graphs-make-datagraphs-strategically-venkat-venkatraman-xfdqf/) (LinkedIn, Dec 2025)
- HBR 2022 article on datagraphs (with Vijay Govindarajan)
- Key thesis: Datagraphs create data network effects through product-in-use data

**Their framing:**
- Knowledge graphs solve semantic problem (meaning at scale)
- Datagraphs solve strategic problem (learning compounds through network effects)
- Context graphs solve action problem (human-machine coordination in moments)
- Examples: Facebook social graph, Google search graph, Netflix movie graph, Amazon purchase graph

## Research Questions

1. **Architectural**: How do you technically represent the three layers (knowledge → datagraph → context)?
2. **Strategic**: If context graphs are projections, what defines the "projection function"?
3. **Practical**: What does a decision trace actually look like? How do you capture it?
4. **Competitive**: Can you build context graph value without owning the underlying datagraph?

## Experiment Plan

### Phase 1: Framework Synthesis ✅
- [x] Map Foundation Capital's "decision traces" to Venkatraman's "datagraph" concept
- [x] Identify where the two frameworks agree/diverge
- [x] Create unified conceptual model → `research/frameworks/synthesis.md`

### Phase 2: Technical Exploration ✅
- [x] Prototype decision trace extraction → `prototypes/decision_trace_extractor.py`
- [x] Cross-session pattern analysis → `prototypes/cross_session_analyzer.py`
- [x] Projection function prototype → `prototypes/projection_function.py`
- [x] Analyze Claude Code transcripts as decision trace source

### Phase 3: Strategic Implications (Future)
- [ ] Case study: How would an incumbent (e.g., Salesforce) build this?
- [ ] Case study: How would an AI-native startup attack?
- [ ] Identify defensibility mechanisms

## Notes

### 2025-12-27: Initial Setup

**Key insight from Foundation Capital essay:**
> "Decision traces operate at a higher level of abstraction than execution traces—not 'the agent called this tool with these parameters' but 'this decision was made under this policy, with this exception, approved by this person, based on this precedent.' It's business-level semantics layered on top of execution telemetry."

This is fascinating because it suggests a **semantic gap** between:
- Execution logs (what happened technically)
- Decision traces (what happened strategically)

The value is in bridging that gap.

**Meta-observation**: Claude Code session transcripts are execution traces. What would it take to extract decision traces from them?

---

## Key Findings

### 1. Claude Code Thinking Blocks ARE Decision Traces
Claude Code's `thinking` blocks in JSONL transcripts contain explicit reasoning:
- Task decomposition ("Let me break down...")
- Error recovery ("Failed, let me try...")
- Sequencing logic ("First... then...")

This is the "business-level semantics on execution telemetry" that Foundation Capital describes.

### 2. Cross-Session Pattern Analysis
Analyzed 165 decision traces across 5 projects:

| Decision Type | % | Insight |
|---------------|---|---------|
| General | 43.6% | Pattern matcher needs refinement |
| Sequencing | 20.6% | Ordering operations is common |
| Recovery | 15.8% | 1 in 6 decisions handle errors |
| Planning | 9.7% | Task decomposition |
| Clarification | 9.1% | Understanding user intent |

**Core tool vocabulary**: Bash (533) → Edit (343) → Read (116)

### 3. Projection Function Works
Built working prototype that:
- Indexes 153 traces, 464 keywords, 44 recovery patterns into a "datagraph"
- Given current task, finds relevant precedents via keyword similarity
- Returns `ContextGraph` with precedents, patterns, warnings, suggested tools

### 4. Strategic Insight: The Projection Function is Key
Venkatraman's insight that context graphs are "projections" raises the key question:
**Who controls the projection function controls what the AI can consider.**

```
projection_function(datagraph, moment_context) → context_graph
```

The projection function itself is a strategic asset.

## Log

| Date | Activity | Outcome |
|------|----------|---------|
| 2025-12-27 | Created experiment | Initial framework synthesis |
| 2025-12-27 | Built decision trace extractor | Successfully extracts from thinking blocks |
| 2025-12-27 | Cross-session analysis | 165 traces, 15.8% recovery rate |
| 2025-12-27 | Projection function | Working prototype with relevance scoring |
