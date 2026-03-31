# Experiment: exp-claude-code-tools-test-vishal

- Owner: vishal
- Date: 2025-12-26
- Goal: Test and evaluate pchalasani/claude-code-tools productivity toolkit

## Context
[claude-code-tools](https://github.com/pchalasani/claude-code-tools) is a popular (639 stars) collection of tools for Claude Code:
- **aichat**: Session management with search, resume, trim, rollover
- **tmux-cli**: Terminal automation ("Playwright for terminals")
- **Plugins**: Safety hooks, workflow logging, UI testing
- **Utilities**: vault (.env backup), env-safe (safe .env inspection)

## Plan
- [x] Install claude-code-tools via uv
- [x] Install aichat-search TUI (Homebrew)
- [x] Test aichat search/resume functionality
- [ ] Test tmux-cli in tmux session (requires tmux)
- [x] Try plugin installation

## Changes
- Created experiment worktree at `../claude-code-tools-test/`
- Installed `claude-code-tools` via uv (4 executables: aichat, env-safe, tmux-cli, vault)
- Installed `aichat-search` via Homebrew
- Built Tantivy index (20,639 sessions indexed)
- Installed `aichat` and `safety-hooks` plugins

## Results
| Tool | Status | Notes |
|------|--------|-------|
| aichat | ✅ Works | Session search, info, resume strategies |
| aichat-search | ✅ Works | Fast full-text search via Tantivy |
| tmux-cli | ⚠️ Requires tmux | Not installed in test environment |
| env-safe | ✅ Works | Safe .env inspection |
| vault | ✅ Works | Encrypted .env backup |
| Plugins | ✅ Works | aichat and safety-hooks installed |

**Key features:**
- Session lineage preserves links to parent sessions when context fills up
- `>resume` hook copies session ID to clipboard when context is filling up
- Full-text search is more powerful than built-in session search

## Recommendations (Based on My Usage Patterns)

**My stats:** 3,052 Claude sessions, 1,495 in tldraw alone, 768 in helloworld

### What to Keep Installed

| Tool | Verdict | Why |
|------|---------|-----|
| `aichat` + `aichat-search` | ✅ **Keep** | Full-text search across 3K+ sessions is invaluable |
| `aichat` plugin | ✅ **Keep** | Session-Searcher sub-agent recovers historical context |
| `>resume` hook | ✅ **Keep** | Quick escape when context fills, preserves lineage |
| `vault` / `env-safe` | ⚠️ Optional | Useful utilities but not core to workflow |
| `safety-hooks` | ⚠️ Optional | May conflict with existing plugin infrastructure |
| `tmux-cli` | ❌ Skip | Not relevant without tmux |

### Comparison with claude-code-transcripts

These tools are **complementary, not redundant**:

| Aspect | `aichat` | `claude-code-transcripts` |
|--------|----------|---------------------------|
| Purpose | Session **management** | Session **export/sharing** |
| Primary use | Internal workflow | External communication |
| Output | Search index | Beautiful HTML |
| Who benefits | Me (finding past work) | Others (sharing work) |
| Key feature | Full-text search + resume | Mobile-friendly, Gist upload |

**Workflow integration:**
1. Use `aichat search` to **find** sessions worth sharing
2. Use `claude-code-transcripts --gist` to **export** them as shareable HTML
3. Link to Gist previews in newsletters/documentation

### Key Insight

> **aichat = "What sessions exist about X?"** (for you to navigate)
> **transcripts = "How can I share this session?"** (for others to read)

They're like `grep` vs `cat` — one finds things, one displays them.

## Follow-ups
- Test `tmux-cli` when running inside tmux
- Explore `workflow` plugin for work logging
- Integrate `aichat search --json` into standard workflow
- Add transcript export to close-shop agent for newsletter-worthy sessions
