# Hermes MakerLab Installation Plan

## Phase 1: Verify Remote Environment (via Tailscale SSH)

1. SSH into the iMac and confirm connectivity
2. Check macOS version, Python version, disk space
3. Verify Tailscale is running and stable
4. Install Homebrew + Python 3.11+ if needed
5. Set up SSH config alias on laptop (`Host makerlab`)

## Phase 2: Install Hermes Agent

1. Run the Hermes installer:
   ```bash
   curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
   ```
2. Run `hermes doctor` to verify installation
3. Run `hermes setup` — configuration wizard

## Phase 3: Configure OpenAI Provider

1. Run `hermes model` and select OpenAI
2. Set `OPENAI_API_KEY` in Hermes config
3. Test with a simple prompt to verify model connectivity
4. Consider model selection: gpt-4o for tasks, gpt-4o-mini for lightweight scheduled checks

## Phase 4: Set Up Telegram Gateway

1. Create bot via @BotFather on Telegram
   - `/newbot` → name it (e.g., "MakerLab Hermes")
   - Copy the bot token
2. Run `hermes gateway` and configure Telegram
3. Set `TELEGRAM_BOT_TOKEN` in Hermes config
4. Test: send a message to the bot, verify response
5. Set up DM pairing (security — only you can interact)

## Phase 5: Scheduled Tasks (Proof of Concept)

Ideas for first scheduled tasks:
- **Health check**: Daily system status report (disk, RAM, uptime) → Telegram
- **Research digest**: Weekly ArXiv/RSS digest on a topic → Telegram
- **MakerLab monitor**: Check if 3D printers/equipment are online (if networkable)

Configure via Hermes cron system, deliver results to Telegram.

## Phase 6: Research Experiment Runner

1. Create a Hermes skill for running experiments
2. Skill should: accept a research question, search papers, summarize findings
3. Can chain with scheduled tasks (e.g., weekly literature scan)
4. Results stored in Hermes memory for cross-session continuity

## Operational Notes

- **Persistence**: Hermes should survive iMac restarts → set up as launchd service
- **Logs**: Check `hermes doctor` and system logs for issues
- **Updates**: `hermes update` — run monthly
- **Backup**: Hermes stores data in SQLite — back up periodically
- **Security**: Command approval system, DM pairing, Tailscale ACLs
