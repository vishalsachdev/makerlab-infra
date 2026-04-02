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
### 2026-04-01
- Troubleshot iMac connectivity after unexpected power-off/reboot
  - Checked Tailscale admin console: iMac (`bus-c02z11ypjv40`, 100.91.229.88) last seen 6:35 PM CDT — offline since
  - Local IPs: 10.192.174.87 / 128.174.135.157
  - Root cause: iMac likely sitting at login screen — LaunchAgents (cloudflared, PostgreSQL, pgAdmin, SQL API) and Tailscale don't start until user logs in
  - Workaround: VPN to campus network → SSH to local IP (Remote Login may work from login screen)
  - Identified permanent fix needed: enable auto-login for admin account so power cycles recover automatically
- Next: Get physical/remote access to log in as admin. Enable auto-login. Consider adding `- [ ] Enable auto-login on iMac` to remaining setup.

### 2026-03-31
- Installed Tailscale on MacBook, connected to MakerLab tailnet (16ms direct)
- Set up SSH key auth + alias (`ssh makerlab`)
- Installed Xcode CLI tools headlessly (softwareupdate trick)
- Installed Hermes agent: GPT-5.4 via OpenAI direct + Telegram gateway
- Installed cloudflared, PostgreSQL 16.8, pgAdmin4 — all user-space (no sudo)
- Created Cloudflare Tunnel `makerlab` with 5 subdomains on illinihunt.org
- Added null worker routes to bypass illinihunt-reverse-proxy catch-all
- Set up launchd persistence for cloudflared, PostgreSQL, pgAdmin, SQL API
- Migrated BADM 554 databases from AWS RDS MySQL → self-hosted PostgreSQL
  - Exported 4 databases (ch07_review, northwind, sales, yelp_champaign) via mysqldump
  - Converted MySQL → PostgreSQL with custom Python script
  - Imported: ch07_review (7/7), northwind (13/13), sales (4/4), yelp_champaign (11/11)
  - Created `badmstudent` read-only user, verified SELECT works + INSERT blocked
- Built SQL API (`sql.illinihunt.org`) for Google Colab access — HTTP POST with JSON
- Deleted AWS RDS instance `badmstudent` (db.m7g.large, ~$100+/mo saved)
- Created Canvas page "Database Access (New - PostgreSQL)" with Colab + pgAdmin instructions
- Graduated from helloworld experiment to standalone repo: github.com/vishalsachdev/makerlab-infra
- Created consolidated Cloudflare reference: ~/.claude/references/cloudflare-illinihunt.md
