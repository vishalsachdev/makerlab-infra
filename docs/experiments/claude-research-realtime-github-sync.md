# Experiment: claude/research-realtime-github-sync
- Owner: claude
- Date: 2025-11-13
- Goal: Research feasibility of real-time GitHub ↔ Canvas bidirectional sync

## Plan
- Evaluate Canvas API capabilities via MCP server
- Identify sync-able content types
- Design recommended architecture
- Document limitations and trade-offs

## Changes
- `GITHUB_CANVAS_SYNC_RESEARCH.md` - Comprehensive research document

## Results
- **Conclusion: YES, it's practical and achievable**
- Easy to sync: Pages, Assignments, Syllabus, Announcements, Files
- Medium difficulty: Discussion Boards, Modules
- Not recommended: Quizzes, Gradebook, Student submissions
- Recommended approach: GitHub as source of truth with Actions → Canvas API

## Follow-ups
- Proceed to implementation if approved
- Set up GitHub Actions workflow for sync
- Create Canvas MCP integration
