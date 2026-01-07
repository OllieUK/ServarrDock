# ServarrDock — Self-Hosted Media & Home Services Stack

---
**WARNING** - This stack is currently being rebuilt and is not stable at this current time.
---

ServarrDock is a modular, Docker-based home server setup for media management, home utilities, and supporting infrastructure.
It is designed around **clear service boundaries**, **low blast radius**, and **independent lifecycles** for different parts of the stack.

The environment is intentionally split into multiple Docker Compose stacks, each with a distinct responsibility, while sharing common networks and configuration.

---

## High-Level Architecture

ServarrDock is organised into several **logical stacks**, each managed by its own Docker Compose file.

### Core Network Infrastructure
**Purpose:** foundational networks required by everything else.

Managed by the root `compose.yml` file.

Includes:
- **Docker networks**
  - `my-network` (general internal Docker bridge used by non-media stacks as needed)
  - `media-net` (shared control-plane bridge for media and edge stacks, enables Docker DNS/service-name resolution across stacks)

This stack must be running before any other stack is started.

---

### Edge Services (Ingress & Authentication)
**Purpose:** secure external access to internal services.

Managed by `compose.edge.yml`.

Includes:
- **Traefik** — reverse proxy, TLS termination, routing
- **Authelia** — authentication and access control

Traefik attaches to `media-net` and routes to backend services on that network.
User-facing access is via HTTPS (443/tcp) only.

---

### Operations Services
**Purpose:** background maintenance and resilience tooling.

Managed by `compose.ops.yml`.

Includes:
- **Watchtower** — automated container updates
  (updates everything by default unless explicitly excluded via labels)

This stack is intentionally isolated from user-facing services.

---

### Media Frontend Services
**Purpose:** user-facing media access and dashboards.

Managed by `compose.frontend.yml`.

Examples:
- **Plex** — media server
- **Jellyfin** — alternative media server
- **Tautulli** — Plex monitoring and statistics
- **Heimdall** — service dashboard
- **Ombi** — media request management
- **Audiobookshelf** (if enabled)

These services prioritise **UX stability** and should not be affected by media acquisition restarts.

---

### Media Acquisition & Automation Pipeline
**Purpose:** downloading, indexing, and media automation.

Managed by `compose.pipeline.yml`.

Examples:
- **Gluetun** — VPN gateway
- **SABnzbd**, **qBittorrent** — download clients (routed via Gluetun)
- **Prowlarr**, **Sonarr**, **Radarr**, **Lidarr**, **Bazarr**, etc.

This stack is high-churn by design and can be restarted or updated independently of the frontend.

---

## Repository Layout (Logical)

docker/  
├── compose.yml                # Core network infrastructure (networks)  
├── compose.edge.yml           # Traefik + Authelia  
├── compose.ops.yml            # Watchtower and ops tooling  
├── compose.frontend.yml       # Plex, Jellyfin, dashboards  
├── compose.pipeline.yml       # VPN, downloaders, *arr stack  
├── .env                       # Shared configuration  
└── config/                    # Persistent service configuration  

---

## Operating the Stack

### Bring up core infrastructure
    docker compose up -d

### Bring up additional stacks
    docker compose -p edge     -f compose.edge.yml up -d
    docker compose -p ops      -f compose.ops.yml up -d
    docker compose -p frontend -f compose.frontend.yml up -d
    docker compose -p pipeline -f compose.pipeline.yml up -d

Stacks can be restarted, updated, or removed independently.

---

## Networking and Name Resolution

### Docker networks
- **media-net**
  - Shared Docker bridge used by edge + media stacks
  - Enables service-to-service communication via Docker DNS (service name resolution)
- **my-network**
  - General internal Docker bridge used where appropriate by non-media stacks

Networks are created by the Core Infrastructure stack and consumed as external networks by all others.

### Service-to-service (internal) addressing
Services should talk to each other using Docker DNS service names and native ports, e.g.
- `http://sonarr:8989`
- `http://radarr:7878`
- `http://plex:32400`
- Gluetun-shared services are addressed via the Gluetun service name, e.g. `http://gluetun:9696` (Prowlarr)

This avoids dependency on LAN DNS for internal container communications.

### User-facing ingress (HTTPS)
User-facing access is host-based routing via Traefik (443/tcp), using subdomains under a shared zone, e.g.
- `https://sonarr.<domain>`
- `https://radarr.<domain>`
- `https://jellyseerr.<domain>`

Internal DNS is handled via split-horizon:
- AdGuard forwards queries for `/*.<domain>/` to OpenWrt dnsmasq
- dnsmasq returns Traefik’s LAN IP for that zone

---

## Getting Started

Initial setup, prerequisites, and first-run instructions are documented in
[INSTALL.md](./INSTALL.md).

---

## Philosophy

* Prefer **clarity over cleverness**
* Keep **blast radius small**
* Separate **user experience**, **automation**, and **infrastructure**
* Let Docker do what it is good at — no orchestration theatre

---

## Status

This is a living setup, evolved over time for a specific home environment.
Documentation is updated when architecture stabilises — not before.
