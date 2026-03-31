# From Cloud to Campus: How an AI Agent Built Our Lab's Infrastructure in One Afternoon

*An afternoon that started with "install this agent on a remote computer" and ended with a fully operational campus server platform — zero cloud bills, zero IT tickets.*

---

## The Starting Point

I walked into this session with a simple idea: install an open-source AI agent called [Hermes](https://github.com/NousResearch/hermes-agent) on an iMac sitting in our university MakerLab. Hermes is a self-improving agent by Nous Research that creates skills from experience, connects to messaging platforms, and runs scheduled tasks. I wanted it as a remote experiment runner I could talk to via Telegram.

What I didn't expect was that three hours later, we'd have:

- An always-on AI agent reachable via Telegram from anywhere
- A Cloudflare Tunnel serving five subdomains from a machine behind a university firewall
- Four teaching databases migrated from AWS (saving $100+/month)
- A custom SQL API that lets students query PostgreSQL from Google Colab
- A Canvas LMS page teaching students how to use it all
- A graduated GitHub repository documenting the entire stack

All on a 2019 iMac with 24 GB of RAM that was just sitting there.

## The Constraints That Shaped Everything

University computing is a special kind of constrained environment. The MakerLab iMac sits behind an institutional firewall that blocks all inbound connections. I don't have the sudo password. I can't install Docker Desktop (it needs a GUI license click). There's no Homebrew. GitHub Actions is disabled on my account. Oh, and I can't physically walk to the machine — I'm doing everything remotely.

Every one of those constraints forced a creative solution:

**No physical access?** Tailscale mesh VPN. The iMac was already on Tailscale. I installed Tailscale on my MacBook, and suddenly I had a 16ms direct WireGuard tunnel between them. `ssh makerlab` just works.

**No inbound ports?** Cloudflare Tunnel. One binary (`cloudflared`) creates an outbound-only encrypted tunnel to Cloudflare's edge. Students hit `sql.illinihunt.org` and traffic routes through Cloudflare's CDN, down the tunnel, to a Python process on localhost. The university firewall never knows, because all connections are outbound HTTPS.

**No sudo?** Everything installs to `~/.local/`. PostgreSQL 16.8 runs from `~/.local/pgsql/`. Cloudflared lives in `~/.local/bin/`. The Hermes agent has its own venv. No root privileges needed, which is actually *more secure* — a compromised service can't escalate to root.

**No Xcode CLI tools?** There's a headless trick: touch a magic file, then `softwareupdate` lists the CLI tools package and installs it without the GUI dialog. This is how CI systems get Git on Mac machines.

## The Stack That Emerged

What's interesting is that we didn't plan this stack. It emerged from solving one problem at a time:

1. **Hermes Agent** — the original goal. Installed, configured with OpenAI's GPT-5.4, Telegram gateway running. Now I can message my lab iMac from my phone.

2. **Cloudflare Tunnel** — needed to expose services. One tunnel, five subdomains, all routed through `config.yml`. The `illinihunt.org` domain was already on Cloudflare from a student project (IlliniHunt — a Product Hunt for UIUC), so the DNS and SSL came free.

3. **PostgreSQL** — started as "maybe we should host a database for students." Ended with four teaching databases migrated from AWS RDS, a custom read-only student user, and a web-based SQL editor (pgAdmin) at `db.illinihunt.org`.

4. **SQL API** — the bridge that makes it all work for teaching. A 60-line Python HTTP server that accepts `POST {"database": "sales", "query": "SELECT ..."}` and returns JSON. Students paste two cells into Google Colab and they're running SQL against real data. No packages to install, no connection strings to debug.

5. **launchd persistence** — four `.plist` files in `~/Library/LaunchAgents/` so everything survives reboots. The iMac can lose power and come back with all services running.

## The Database Migration: AWS to a Closet

The most satisfying part was deleting the AWS RDS instance.

We'd been running a `db.m7g.large` MySQL instance on Amazon RDS for the BADM 554 (Enterprise Database Management) course. Four databases, 85,000+ rows of real Yelp data for Champaign-Urbana, classic Northwind traders, textbook exercises. Cost: roughly $100/month for a machine that students only use during Monday evening class.

The migration went like this:

1. Connected to RDS via AWS CloudShell, dumped all four databases with `mysqldump`
2. Uploaded to S3 as a transfer mechanism (CloudShell can't SSH out)
3. Downloaded on my MacBook, SCP'd to the iMac
4. Wrote a Python script to convert MySQL syntax to PostgreSQL (backticks to double quotes, `AUTO_INCREMENT` to `SERIAL`, `datetime` to `timestamp`, fixing `0000-00-00` dates)
5. Imported into PostgreSQL on the iMac
6. Created a read-only `badmstudent` user
7. Verified all queries work, including confirming that `INSERT` is properly blocked
8. Deleted the RDS instance. Watched "Deleting" appear in the AWS console. 

The Canvas page update took about two minutes — replace the MySQL connection instructions with a simple `sql()` helper function that hits our new API.

## The Meta-Layer: An Agent Managing Its Own Machine

Here's the part that still makes me smile. During setup, I needed to install Homebrew on the iMac. The installer needs sudo, and I'd forgotten the password. I told Hermes — via Telegram, from my phone — to run the install command. It offered a safer two-step version (download, then execute), asked for approval, and attempted the install. The agent I'd just installed was already helping manage the machine it lives on.

This is the promise of always-on agents. Not replacing human judgment, but being a persistent presence on a machine you can't always reach. When a student reports that the database is down at 11 PM, I can message the Hermes bot: "check if PostgreSQL is running." It has shell access. It can diagnose and potentially restart services. I don't need to VPN in, find the SSH key, remember the Tailscale hostname.

## What I Learned

**Constraints breed creativity.** The no-sudo, no-Docker, no-inbound-ports environment forced a stack that's arguably better than what I would have built with full access. User-space installs are more secure. Cloudflare Tunnel is more reliable than port forwarding. A lightweight SQL API is a better student experience than raw database connections.

**The compound effect is real.** Each tool we installed made the next one easier. Tailscale enabled SSH. SSH enabled everything else. Cloudflare Tunnel enabled student access. Student access justified migrating the database. The database migration justified deleting AWS. Each step created momentum for the next.

**Teaching infrastructure should be self-hosted.** Cloud databases for teaching are an odd tax. Students use them for 3 hours a week, 15 weeks a semester. The MakerLab has 10 iMacs sitting idle most of the time. Running PostgreSQL on one of them is free, fast, and teaches students something real about infrastructure.

## The Numbers

| Metric | Before | After |
|--------|--------|-------|
| Monthly cloud cost | ~$100 (AWS RDS) | $0 |
| Student setup steps | 4 cells (install packages, connection string) | 2 cells (just the helper function) |
| Database engine | MySQL 8.0 | PostgreSQL 16.8 |
| Hosting | AWS us-east-2 | MakerLab closet, Champaign IL |
| Latency (from campus) | ~30ms to Ohio | <1ms to localhost via tunnel |
| Services running | 1 (database) | 5 (agent, database, pgAdmin, SQL API, web) |
| Time to build | N/A (pre-existing) | ~3 hours |

## What's Next

The iMac is now a platform, not just a database host. The Cloudflare Tunnel can route any number of subdomains. The MakerLab has 10 machines total — only one is on Tailscale right now. The roadmap includes:

- **Deploying the BADM 554 WhatsApp bot** (currently on a VPS) to `bot.illinihunt.org`
- **Portainer** for container management across all 10 machines
- **Hermes multi-agent experiments** — spawning sub-agents for parallel research tasks
- **Student project hosting** — giving student teams their own `project.illinihunt.org` subdomains

The most interesting question is what happens when you give students access to infrastructure that costs nothing to run. When the marginal cost of a new subdomain is zero, experimentation becomes free. That's the kind of environment where interesting things happen.

---

*This article was written as part of a live collaboration session using Claude Code. The entire infrastructure — from first SSH to live databases — was built in a single afternoon.*

*Repository: [makerlab-infra](https://github.com/vishalsachdev/makerlab-infra)*
