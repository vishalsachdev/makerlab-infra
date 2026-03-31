# makerlab-infra

MakerLab infrastructure: Hermes agent, Cloudflare Tunnel, self-hosted apps for UIUC campus.

## Repo Type
type: code

## Architecture

- **Host**: iMac 2019, 24GB RAM, Intel i5-9600K, macOS 14.7.5 Sonoma
- **Agent**: [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) (Python 3.11+, GPT-5.4)
- **Tunnel**: Cloudflare Tunnel `makerlab` → `*.illinihunt.org`
- **Gateway**: Telegram (remote interaction from anywhere)
- **Network**: Tailscale mesh VPN (SSH access: `ssh makerlab`)
- **Persistence**: launchd (all services survive reboots)

## Services

| Subdomain | Service | Port | Status |
|-----------|---------|------|--------|
| lab.illinihunt.org | Test page / MakerLab site | 8000 | Live |
| db.illinihunt.org | pgAdmin (student DB) | 5050 | Live |
| bot.illinihunt.org | BADM 554 bot (pending) | 3000 | Setup needed |
| admin.illinihunt.org | Portainer (pending) | 9443 | Not yet installed |

## Key Commands

```bash
ssh makerlab                           # SSH via Tailscale
hermes                                 # Hermes CLI (on iMac)
hermes doctor                          # Diagnostics
~/.local/pgsql/bin/psql -p 5433        # PostgreSQL CLI
launchctl list | grep -E "cloudflare|postgres|pgadmin"  # Check services
```

## Persistence (launchd)

Services in `~/Library/LaunchAgents/`:
- `com.cloudflare.tunnel.plist` — Cloudflare Tunnel
- `com.postgresql.server.plist` — PostgreSQL 16.8 on port 5433
- `com.pgadmin.server.plist` — pgAdmin4 on port 5050

## Remaining Setup

- [ ] Deploy badm554-bot to bot.illinihunt.org (needs clawdbot gateway + WhatsApp pairing)
- [ ] Install Portainer for container management
- [ ] Investigate Hermes multi-agent for running experiments
- [ ] Add more iMacs to Tailscale mesh
- [ ] Set up PostgreSQL databases for BADM 554 student exercises
- [ ] Recover/reset sudo password for Homebrew install

## Session Log
### 2026-03-31
- Installed Tailscale on MacBook, connected to MakerLab tailnet (16ms direct)
- Set up SSH key auth + alias (`ssh makerlab`)
- Installed Xcode CLI tools headlessly (softwareupdate trick)
- Installed Hermes agent: GPT-5.4 via OpenAI direct + Telegram gateway
- Installed cloudflared, PostgreSQL 16.8, pgAdmin4 — all user-space (no sudo)
- Created Cloudflare Tunnel `makerlab` with 4 subdomains on illinihunt.org
- Added null worker routes to bypass illinihunt-reverse-proxy catch-all
- Set up launchd persistence for cloudflared, PostgreSQL, pgAdmin
- Verified: lab.illinihunt.org (200), db.illinihunt.org (401/login), PostgreSQL (alive)
- Created consolidated Cloudflare reference: ~/.claude/references/cloudflare-illinihunt.md
- Graduating to standalone repo: makerlab-infra
