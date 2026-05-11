# VEKA Ecosystem — Context Document

> **This is a living document.** It is the single authoritative reference for the entire VEKA ecosystem — intended for use by developers, AI agents, and contributors working across any VEKAgg repository. Update it whenever key information changes.

**Last updated:** May 2026  
**Maintained in:** `VEKAgg/vekaapi` (main branch)

---

## 1. Organisation Overview

**Name:** VEKA (VorteK Academy → rebranded to VEKA)  
**Domain:** veka.gg  
**GitHub Org:** https://github.com/VEKAgg  
**Type:** Online community and content platform, originally an esports organisation  
**Status:** Active — multiple properties in active development  
**Contributors:** MJkhan1400 (lead developer), twistedvortek / Shafaat (infrastructure, secondary dev)  

---

## 2. Properties & Repositories

### 2.1 veka.gg — Main Platform
- **Repo:** `VEKAgg/veka` (Private)
- **Live URL:** https://veka.gg
- **Tech:** SvelteKit 2.x + Svelte 5 (runes), TypeScript strict, Tailwind CSS 4.x, Directus SDK 18.x, adapter-node
- **Backend CMS:** Directus at `https://vekams.veka.gg` (PostgreSQL + Redis)
- **Auth:** Discord OAuth → auto-creates Directus profile
- **Features:**
  - Blog posting and listing
  - Author profiles with social links
  - Real-time nested comments (Best/Newest/Top sorting via WebSockets)
  - Like system (optimistic UI)
  - Rich text editor (Tiptap: images, YouTube embeds, mentions)
  - Route feature flags per environment
- **Status:** Phase 0 complete, Phase 1 (social links + enhanced profiles) in progress
- **Key files:**
  - `src/lib/services/api.ts` — all Directus SDK calls
  - `src/lib/services/authStore.ts` — auth state
  - `src/lib/config/routes.ts` — feature flags
  - `src/lib/types/directus.ts` — full schema types
- **Directus collections:** `blogs`, `profiles`, `comments`, `blog_likes`
- **Environment vars:** `PUBLIC_DIRECTUS_URL=https://vekams.veka.gg`, `PUBLIC_WEBSOCKET_URL=wss://vekams.veka.gg/websocket`

### 2.2 IGFV — Elite:Dangerous Squadron Site
- **Repo:** `VEKAgg/IGFV` (Public)
- **Live URL:** https://igfv.veka.gg
- **Tech:** Next.js 15, React 19, TypeScript, Tailwind CSS, Framer Motion, Docker, Nginx, Cloudflare, GitHub Actions
- **Community:** Interstellar Goodfellas (IGFV) — Elite:Dangerous squadron
- **Features:** Squadron info, fleet carrier management (Valhall), events/expeditions, gallery, Discord integration, INARA/EDSM live data
- **External APIs used:** EDSM (Elite Dangerous Star Map), INARA (commander/squadron data)
- **Branch `2026`:** In-progress major improvements branch
- **Branch `tttr`:** Was the GTA5 crew site (now split to its own repo)
- **Key file:** `src/lib/inara.ts` — INARA API integration

### 2.3 TTTR — GTA 5 Crew Site
- **Repo:** `VEKAgg/TTTR` (Private)
- **Tech:** Next.js 15, TypeScript (same codebase base as IGFV, GTA-branded)
- **Community:** GTA 5 crew / Discord community
- **Status:** Split from IGFV repo (was `tttr` branch). Needs full git push to populate the repo.
- **Note:** To fully populate: `git push tttr-dest tttr:main --force` from IGFV clone

### 2.4 DiscordBot — VEKA Community Bot
- **Repo:** `VEKAgg/DiscordBot` (Public)
- **Tech:** Python, MongoDB (MONGODB_URI), Redis (REDIS_URL), Discord API (DISCORD_TOKEN), Docker
- **Purpose:** Professional networking and community development bot
- **Commands:** `!profile`, `!setupprofile`, `!connect`, `!help`
- **Features:** Profile management, connection requests, networking — career dev, events, games (planned)
- **Status:** Active — 74 commits, 10 branches, 1 fork

### 2.5 vekasteambot — Steam Discord Bot
- **Repo:** `VEKAgg/vekasteambot` (Private)
- **Tech:** Python
- **Purpose:** Steam integration for Discord community

### 2.6 vekams — Management System
- **Repo:** `VEKAgg/vekams` (Private)
- **Tech:** Go
- **Purpose:** VEKA internal management/ops tooling

### 2.7 vekams-strapi — Management System (Strapi)
- **Repo:** `VEKAgg/vekams-strapi` (Private)
- **Tech:** TypeScript, Strapi
- **Description:** veka management system using strapi

### 2.8 homelab — Self-Hosted Infrastructure
- **Repo:** `VEKAgg/homelab` (Public)
- **Live:** Hosts all VEKA properties
- **Hardware:** Repurposed gaming PC — Intel i7-7700K, 32GB+ DDR4, GTX 1080 Ti, 1TB+4TB storage
- **Hypervisor:** Proxmox VE
- **Storage:** TrueNAS SCALE (ZFS pools: media/, photos/, documents/, backups/)
- **Containers:** Docker LXC managed via Portainer
- **VMs:** Arch Linux (web hosting), OBS Headless (streaming)
- **Network:** Pi-hole DNS (.veka local domains), Tailscale VPN, Nginx Proxy Manager (planned), OPNSense (planned)
- **Active services:** Portainer, Pi-hole, Immich, TrueNAS
- **Planned services:** Jellyfin (media.veka.gg), Nextcloud (cloud.veka.gg), Home Assistant, Vaultwarden, Grafana+Prometheus, Paperless-ngx, Ollama

### 2.9 Legacy / Deprecated Repos
- `VASite` — (DEPRECATED) Legacy Vue site from VorteK Academy Esports era
- `DiscordLiteBot` — (DEPRECATED) JavaScript lite bot
- `DiscordMusicBot` — (DEPRECATED) TypeScript music bot
- `DiscordRadioBot` — (Public) 24/7 radio Discord bot (JavaScript, MIT License)
- `vekamj` — (Public) Python, Feb 2025
- `nginx-setup` — (Public) nginx configuration
- `ms-veka` — (Private) purpose unclear
- `VEKASite` — (Private) TypeScript, likely newer main site iteration

---

## 3. Infrastructure & Deployment

```
Physical: Gaming PC (i7-7700K, 32GB RAM, GTX 1080 Ti)
  └─ Proxmox VE (hypervisor)
      ├─ TrueNAS SCALE VM (ZFS storage — NFS mounts to containers)
      ├─ Docker LXC (Portainer, Pi-hole, Immich, all web apps)
      ├─ Arch Linux VM (web hosting)
      └─ OBS Headless VM (streaming)

Networking:
  - Pi-hole: internal .veka DNS resolution
  - Tailscale VPN: secure remote access
  - Cloudflare: DNS + CDN for public domains
  - Domain: veka.gg + subdomains

Public subdomains:
  - veka.gg — main platform
  - vekams.veka.gg — Directus CMS API
  - igfv.veka.gg — Elite:Dangerous squadron
  - cloud.veka.gg — planned (Nextcloud)
  - media.veka.gg — planned (Jellyfin)

CI/CD: GitHub Actions (per-repo deploy workflows)
Containerisation: Docker + docker-compose on all services
```

---

## 4. Shared Services & Credentials Pattern

| Service | URL | Used by |
|---------|-----|---------|
| Directus CMS | `https://vekams.veka.gg` | veka.gg, vekaapi |
| PostgreSQL | via Directus | veka.gg |
| Redis | via Directus | veka.gg (WebSockets, caching) |
| Discord OAuth | discord.com/api | veka.gg, IGFV, TTTR |
| MongoDB | MONGODB_URI | DiscordBot |
| Redis (bot) | REDIS_URL | DiscordBot |
| EDSM API | https://www.edsm.net/api | IGFV |
| INARA API | https://inara.cz/inapi | IGFV |

---

## 5. vekaapi — This Repo

- **Purpose:** Centralised REST API layer for all VEKA properties
- **Replaces:** Each property doing its own isolated API calls
- **Planned modules:** `/auth`, `/community`, `/blog`, `/squadron`, `/edsm`, `/inara`, `/discord`, `/webhooks`, `/health`
- **Tech candidates:** Node.js + TypeScript, Hono/Fastify, Discord OAuth2 + JWT, Redis caching, Docker deployment
- **Status:** Planning / initial setup (May 2026)

---

## 6. Key Decisions & Conventions

- All VEKA web apps use **Docker + GitHub Actions** for CI/CD
- **SvelteKit apps** (veka.gg): Svelte 5 runes mandatory, no legacy `$:` syntax
- **Next.js apps** (IGFV, TTTR): Next.js 15 App Router, TypeScript strict, Tailwind CSS
- **Discord** is the primary community hub — OAuth is the auth method of choice across all properties
- **No public cloud** — everything self-hosted on the homelab
- Branch naming: feature branches, community-specific branches (e.g. `rta` for tailored portfolio versions, `2026` for yearly improvement branches)
- `context.md` in vekaapi is the canonical reference document for all repos

---

## 7. Open Tasks / Known Issues (as of May 2026)

- [ ] `VEKAgg/TTTR` repo needs git push from IGFV `tttr` branch to populate fully
- [ ] `VEKAgg/IGFV` branch `2026` — functional improvements in progress (broken API integrations, EDSM/INARA calls)
- [ ] `VEKAgg/IGFV` branch `tttr` — to be deleted once TTTR repo is populated
- [ ] vekaapi — initial codebase to be scaffolded
- [ ] veka.gg — Phase 1 (social links, enhanced profiles) in progress
- [ ] Homelab — Nginx Proxy Manager, Jellyfin, Nextcloud, Grafana+Prometheus pending setup

---

## 8. Change Log

| Date | Change | By |
|------|--------|----|
| May 2026 | Initial context.md created; vekaapi repo scaffolded | twistedvortek |
| May 2026 | Shafaat-Portfolio `rta` branch created with RTA-tailored content | twistedvortek |
| May 2026 | TTTR repo created (private); pending git population from IGFV tttr branch | twistedvortek |
| May 2026 | twistedvortek GitHub profile README updated (IT infra specialist, orange theme) | twistedvortek |
