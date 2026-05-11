# vekaapi

> The centralised backend API for the entire VEKA ecosystem — powering veka.gg, IGFV, TTTR, community Discord bots, and all connected sub-properties.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Org](https://img.shields.io/badge/org-VEKAgg-blue)](https://github.com/VEKAgg)
[![Status](https://img.shields.io/badge/status-in%20development-orange)](#)

---

## What is VEKA?

**VEKA** (veka.gg) is an online community and content platform originally founded as VorteK Academy, an esports organisation. It has since evolved into a multi-purpose community hub spanning:

- **veka.gg** — The main VEKA platform: a modern blogging and community site built with SvelteKit + Directus CMS. Features Discord OAuth login, blog posts, author profiles, real-time nested comments, likes, and a rich text editor (Tiptap). Hosted on a self-built homelab server.
- **IGFV (igfv.veka.gg)** — Public website for the *Interstellar Goodfellas* Elite:Dangerous squadron. Built with Next.js 15 + TypeScript. Features squadron info, fleet carrier management, events, gallery, Discord integration, and live INARA/EDSM data.
- **TTTR** — Website for a GTA 5 crew/Discord community. Built on the same Next.js codebase as IGFV, adapted with GTA-specific branding and content.
- **DiscordBot** — The VEKA community Discord bot (Python + MongoDB + Redis). Handles professional networking, member profiles, connection requests, and community management across VEKA-linked Discord servers.
- **vekasteambot** (Private) — Steam-integrated Discord bot for the community.
- **vekams / vekams-strapi** — VEKA management system (Go + Strapi/TypeScript). Internal tooling for managing community operations.
- **Homelab** — The self-hosted private cloud running all of the above. A repurposed gaming PC running Proxmox VE, TrueNAS SCALE (ZFS storage), Docker LXC, Tailscale VPN, and Nginx Proxy Manager. Hosts all VEKA web properties.

---

## What is vekaapi?

`vekaapi` is the **single shared REST API** that connects and powers all VEKA web properties. Rather than each site/bot implementing its own isolated API calls, vekaapi acts as a unified backend layer providing:

- **Shared data** accessible across all properties (community members, events, announcements, stats)
- **Authentication bridge** between Discord OAuth, Directus sessions, and external sites
- **Cross-property integrations** — e.g. IGFV querying community-wide Discord member data, DiscordBot syncing profile data with veka.gg
- **Third-party proxying** — wrapping external APIs (EDSM, INARA, Steam) with caching and rate-limit handling so frontends don't call them directly
- **Webhook endpoints** for Discord bots, GitHub Actions, and automation flows

---

## Ecosystem Overview

```
                        ┌─────────────────────────────────┐
                        │         VEKA Homelab            │
                        │  Proxmox VE + Docker LXC        │
                        │  TrueNAS (ZFS) + Tailscale VPN  │
                        └────────────┬────────────────────┘
                                     │ hosts
          ┌──────────┬───────────────┼───────────────┬──────────────┐
          │          │               │               │              │
     ┌────▼────┐ ┌───▼───┐   ┌──────▼──────┐ ┌─────▼────┐ ┌──────▼──────┐
     │ veka.gg │ │  IGFV │   │    TTTR     │ │DiscordBot│ │  vekams     │
     │SvelteKit│ │Next.js│   │   Next.js   │ │  Python  │ │  Go+Strapi  │
     └────┬────┘ └───┬───┘   └──────┬──────┘ └─────┬────┘ └──────┬──────┘
          │          │               │               │              │
          └──────────┴───────────────┼───────────────┴──────────────┘
                                     │ all consume
                              ┌──────▼──────┐
                              │   vekaapi   │
                              │  (this repo)│
                              └──────┬──────┘
                                     │
              ┌──────────────────────┼────────────────────────┐
              │                      │                        │
     ┌────────▼──────┐    ┌──────────▼────────┐   ┌──────────▼───────┐
     │ Directus CMS  │    │  Discord API       │   │ External APIs    │
     │ vekams.veka.gg│    │  (OAuth + Bot)     │   │ EDSM, INARA,     │
     │ PostgreSQL    │    │                    │   │ Steam, etc.      │
     │ Redis         │    └───────────────────-┘   └──────────────────┘
     └───────────────┘
```

---

## Planned API Modules

| Module | Description | Consumers |
|--------|-------------|----------|
| `/auth` | Discord OAuth token exchange, session validation | veka.gg, IGFV, TTTR |
| `/community` | Member profiles, roles, Discord presence | veka.gg, DiscordBot |
| `/blog` | Blog posts, comments, likes (Directus proxy) | veka.gg |
| `/squadron` | IGFV squadron data, fleet carrier info, events | IGFV |
| `/edsm` | Cached EDSM (Elite:Dangerous Star Map) lookups | IGFV |
| `/inara` | Cached INARA API calls (commander/squadron data) | IGFV |
| `/discord` | Guild stats, member count, role lists | All sites |
| `/webhooks` | GitHub Actions CI/CD hooks, Discord notifications | Internal |
| `/health` | Service health checks for all VEKA properties | Monitoring |

---

## Tech Stack

> To be finalised — candidates below.

| Layer | Candidate |
|-------|-----------|
| Runtime | Node.js (TypeScript) |
| Framework | Hono / Fastify / Express |
| Auth | Discord OAuth2 + JWT |
| Database | Shared Directus (PostgreSQL) via SDK |
| Cache | Redis (rate limiting, third-party API cache) |
| Deployment | Docker + homelab (Proxmox/Docker LXC) |
| CI/CD | GitHub Actions (VEKAgg workflows) |

---

## Repository Structure (Planned)

```
vekaapi/
├── src/
│   ├── routes/          # API route handlers per module
│   │   ├── auth/
│   │   ├── blog/
│   │   ├── community/
│   │   ├── squadron/
│   │   ├── edsm/
│   │   ├── inara/
│   │   ├── discord/
│   │   └── webhooks/
│   ├── middleware/      # Auth, rate limiting, logging
│   ├── services/        # External API clients (Directus, Discord, EDSM, INARA)
│   ├── lib/             # Shared utilities, types, config
│   └── index.ts         # Entry point
├── context.md           # Living document: full VEKA ecosystem context
├── Dockerfile
├── docker-compose.yml
├── .env.example
└── package.json
```

---

## Related Repositories

| Repo | Visibility | Description |
|------|------------|-------------|
| [veka](https://github.com/VEKAgg/veka) | Private | veka.gg — Main SvelteKit + Directus platform |
| [IGFV](https://github.com/VEKAgg/IGFV) | Public | igfv.veka.gg — Elite:Dangerous squadron site |
| [TTTR](https://github.com/VEKAgg/TTTR) | Private | GTA 5 crew community website |
| [DiscordBot](https://github.com/VEKAgg/DiscordBot) | Public | VEKA community Discord bot (Python) |
| [vekasteambot](https://github.com/VEKAgg/vekasteambot) | Private | Steam Discord bot |
| [homelab](https://github.com/VEKAgg/homelab) | Public | Self-hosted infrastructure (Proxmox + Docker) |
| [vekams](https://github.com/VEKAgg/vekams) | Private | VEKA management system (Go) |
| [vekams-strapi](https://github.com/VEKAgg/vekams-strapi) | Private | VEKA management system (Strapi/TS) |

---

## Contributing

This is an internal VEKAgg project. External contributions are not currently open, but the codebase is public for transparency and reference.

For questions, find us on Discord or open an issue.

---

## License

MIT — see [LICENSE](LICENSE).
