# OpenAI API Skill Design

## Goal
Create an `openai-api` skill for Claude Code, modeled after the Anthropic `claude-api` skill, that helps build apps using the OpenAI API and SDKs.

## Approach
Tiered depth (Approach C): SKILL.md routing layer + deep Python/TypeScript coverage + light Java/Go/C#/cURL.

## Structure (~19 files)

```
~/.claude/skills/openai-api/
├── SKILL.md                          # Routing layer
├── python/openai-api/                # Deep: README, streaming, tool-use, structured-outputs, batches
├── python/agents-sdk/                # Deep: README, patterns
├── typescript/openai-api/            # Deep: README, streaming, tool-use, structured-outputs, batches
├── typescript/agents-sdk/            # Deep: README, patterns
├── java/openai-api.md                # Light: single file
├── go/openai-api.md                  # Light: single file
├── csharp/openai-api.md              # Light: single file
├── curl/examples.md                  # Raw HTTP
└── shared/                           # models, error-codes, tool-use-concepts, live-sources
```

## Key Design Decisions
- Default model: gpt-5.4 (unless user specifies otherwise)
- Default API: Responses API for new projects, Chat Completions for existing
- Covers both Chat Completions and Responses API with clear decision tree
- Agents SDK included for Python + TypeScript
- Language detection mirrors claude-api skill logic

## Research Sources
- Three research agents completed 2026-03-05 covering models/pricing, SDK patterns, error handling
- All data verified via web searches against official OpenAI docs
