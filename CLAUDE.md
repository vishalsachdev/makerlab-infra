# makerlab-infra

MakerLab infrastructure: Hermes agent, Cloudflare Tunnel, self-hosted apps for UIUC campus.

## Repo Type
type: code

## Architecture

- **Host**: iMac 2019, 24GB RAM, Intel i5-9600K, Radeon Pro 580X, macOS 14.7.5 Sonoma
- **Agent**: [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) (Python 3.11+, GPT-5.4)
- **Tunnel**: Cloudflare Tunnel `makerlab` (ID: `5e3ec194-8515-4638-9cf8-ee26e6f4958a`)
- **Gateway**: Telegram (remote interaction from anywhere)
- **Network**: Tailscale mesh VPN (SSH access: `ssh makerlab`)
- **Persistence**: launchd (all services survive reboots)
- **No sudo**: Everything installed to user-space (`~/.local/`)

## Services

| Subdomain | Service | Port | Status |
|-----------|---------|------|--------|
| lab.illinihunt.org | Test page / MakerLab site | 8000 | Live |
| db.illinihunt.org | pgAdmin (student DB admin) | 5050 | Live |
| sql.illinihunt.org | SQL API (Colab access) | 5480 | Live |
| bot.illinihunt.org | BADM 554 bot (pending) | 3000 | Setup needed |
| admin.illinihunt.org | Portainer (pending) | 9443 | Not yet installed |

## Database Infrastructure (BADM 554)

Migrated from AWS RDS MySQL ($100+/mo) to self-hosted PostgreSQL ($0) — March 2026.

| Database | Tables | Rows | Content |
|----------|--------|------|---------|
| ch07_review | 7 | 85 | Textbook Ch. 7 exercises |
| sales | 4 | 46 | Invoices, customers, vendors |
| northwind | 13 | 184 | Classic Northwind traders |
| yelp_champaign | 11 | 85,489 | Real Yelp data for C-U |

**Student access:**
- pgAdmin: `db.illinihunt.org` (login: `vishal@illinois.edu` / `makerlab2026`)
- SQL API: `sql.illinihunt.org` — POST `{"database":"sales","query":"SELECT ..."}`
- Credentials: `badmstudent` / `badmstudent` (read-only)
- Canvas page: "Database Access (New - PostgreSQL)" in course 68237

**SQL dump:** `badm554_all_dbs.sql` (8.2 MB MySQL format, converted to PG on import). Also on S3: `s3://badm554-temp-export/` (delete after confirming stability).

## Key Commands

```bash
ssh makerlab                           # SSH via Tailscale
hermes                                 # Hermes CLI (on iMac)
hermes doctor                          # Diagnostics

# PostgreSQL
~/.local/pgsql/bin/psql -p 5433 -d sales            # CLI as owner
PGPASSWORD=badmstudent ~/.local/pgsql/bin/psql -p 5433 -U badmstudent -d sales  # CLI as student

# Service health
launchctl list | grep -E "cloudflare|postgres|pgadmin|sql-api"
curl http://localhost:5480/            # SQL API
curl http://localhost:5050/login       # pgAdmin (expect 401)
tail ~/.cloudflared/tunnel.log         # Tunnel logs
```

## Persistence (launchd)

Services in `~/Library/LaunchAgents/`:
- `com.cloudflare.tunnel.plist` — Cloudflare Tunnel
- `com.postgresql.server.plist` — PostgreSQL 16.8 on port 5433
- `com.pgadmin.server.plist` — pgAdmin4 on port 5050
- `com.badm554.sql-api.plist` — SQL query API on port 5480

## Software Locations (all user-space)

| Software | Path |
|----------|------|
| cloudflared | `~/.local/bin/cloudflared` |
| PostgreSQL 16.8 | `~/.local/pgsql/` (data: `~/.local/pgsql/data/`) |
| pgAdmin4 | `~/apps/pgadmin-venv/` |
| SQL API | `~/apps/sql-api.py` (runs via pgadmin-venv Python) |
| Hermes agent | `~/.hermes/hermes-agent/` |
| Node.js 22 | `~/.hermes/node/` |
| Python 3.11 | `~/.hermes/hermes-agent/venv/` |
| Tunnel config | `~/.cloudflared/config.yml` |
| badm554-bot | `~/apps/badm554-bot/` (code deployed, not running) |

## Cloudflare Tunnel Config

```yaml
# ~/.cloudflared/config.yml
tunnel: 5e3ec194-8515-4638-9cf8-ee26e6f4958a
ingress:
  - hostname: lab.illinihunt.org   → localhost:8000
  - hostname: db.illinihunt.org    → localhost:5050
  - hostname: sql.illinihunt.org   → localhost:5480
  - hostname: bot.illinihunt.org   → localhost:3000
  - hostname: admin.illinihunt.org → localhost:9443
  - service: http_status:404
```

Each subdomain also has a **null worker route** on Cloudflare to bypass the `illinihunt-reverse-proxy` catch-all worker. See `~/.claude/references/cloudflare-illinihunt.md` for full Cloudflare docs.

## Remaining Setup

- [ ] Deploy badm554-bot to bot.illinihunt.org (needs clawdbot gateway + WhatsApp pairing)
- [ ] Install Portainer for container management
- [ ] Investigate Hermes multi-agent for running experiments
- [ ] Add more iMacs to Tailscale mesh (2 of 10 on tailnet, 1 active)
- [ ] Recover/reset sudo password for Homebrew install
- [ ] Delete S3 bucket `badm554-temp-export` after confirming DB stability
- [ ] Set up launchd for Hermes gateway (currently manual start)
- [ ] Enable auto-login on iMac so services survive power cycles without physical access

## Session Log
### 2026-05-02
- Confirmed MakerLab iMac (`bus-c02z11ypjv40`, 100.91.229.88) still offline — last seen 7d ago. Apr 1 power-cycle issue never resolved; all services (lab/db/sql/bot/admin.illinihunt.org) down.
- Probed other tailnet peers via `tailscale ping` (ICMP fails since Tailscale uses encapsulated traffic):
  - `bus-c02z11yajv40` (100.108.80.45) reachable on campus net 130.126.255.131 — SSH port 22 closed (Remote Login likely off)
  - `bus-mm903h0cyk` (100.120.104.11) reachable at home 192.168.68.66 — SSH port 22 closed; user is physically at it
- Next: enable Remote Login on home Mac (`bus-mm903h0cyk`), then physical access at the iMac to log in + turn on auto-login so power cycles self-recover.

*Older entries archived to `docs/session-archive.md`.*
