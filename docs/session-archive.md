# Session Archive

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
