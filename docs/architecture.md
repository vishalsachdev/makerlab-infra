# MakerLab Infrastructure Architecture

## Machines

| Machine | Tailscale IP | Status | Role |
|---|---|---|---|
| bus-c02z11ypjv40 | 100.91.229.88 | Active | Primary (Hermes + apps + DB) |
| bus-gh24mj9rcq | 100.97.63.66 | Online | Available |
| bus-c02z11yajv40 | 100.108.80.45 | Offline | Available (needs power on) |

All machines: iMac 2019, 24GB RAM, Intel i5-9600K, Radeon Pro 580X, macOS 14.7.5 Sonoma

## Stack

| Layer | Tool | Purpose |
|---|---|---|
| Tunnel/Ingress | Cloudflare Tunnel (cloudflared) | Outbound-only, SSL, *.illinihunt.org subdomains |
| Container Runtime | Colima + Docker | Headless Docker on macOS (no GUI needed) |
| Management | Portainer CE | Web UI for containers across machines |
| Machine Mesh | Tailscale | Admin SSH, inter-machine networking |
| Agent | Hermes | Scheduled tasks, Telegram bot, remote management |

## Services (Planned)

| Subdomain | Service | Port |
|---|---|---|
| bot.illinihunt.org | BADM 554 WhatsApp bot | 3000 |
| lab.illinihunt.org | MakerLab website | 8000 |
| db.illinihunt.org | pgAdmin (student DB exercises) | 5050 |
| admin.illinihunt.org | Portainer dashboard | 9443 |

## Network Topology

```
Internet → Cloudflare Edge (SSL) → Cloudflare Tunnel → iMac services
Admin → Tailscale mesh → SSH to any iMac
Students → *.illinihunt.org → Cloudflare → Tunnel → local containers
```

## Key Decisions

- **No Linux VMs**: Coolify/CapRover/Dokku are Linux-only. VM overhead wastes 6GB/machine.
- **Colima over Docker Desktop**: Docker Desktop needs GUI for license. Colima is headless.
- **Cloudflare over Tailscale Funnel**: Funnel can't do custom domains (TLS mismatch). Cloudflare handles SSL + domains natively. We already own illinihunt.org on Cloudflare.
- **University firewall**: All tunnels are outbound-only. No IT approval needed.

## Access

```bash
ssh makerlab                    # SSH alias (Tailscale)
hermes                          # Telegram bot on the iMac
```

macOS user: MakerLabGuru
SSH key: ~/.ssh/id_ed25519 (authorized on primary iMac)
