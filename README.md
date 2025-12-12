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

### Core DNS & Network Infrastructure
**Purpose:** foundational services required by everything else.

Managed by the root `compose.yml` file.

Includes:
- **Pi-hole** — network-wide DNS and ad-blocking
- **Docker networks**
  - `home-macvlan` (LAN / DMZ-facing services)
  - `my-network` (internal container-to-container communication)

This stack must be running before any other stack is started.

---

### Edge Services (Ingress & Authentication)
**Purpose:** secure external access to internal services.

Managed by `compose.edge.yml`.

Includes:
- **Traefik** — reverse proxy, TLS termination, routing
- **Authelia** — authentication and access control

This stack depends on the networks created by the Core Infrastructure stack.

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

Includes:
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

Includes:
- **Gluetun** — VPN gateway
- **SABnzbd**, **qBittorrent** — download clients
- **Prowlarr**, **Sonarr**, **Radarr**, **Lidarr**, **Bazarr**, etc.

This stack is high-churn by design and can be restarted or updated independently of the frontend.


---

## Repository Layout (Logical)

docker/ <br>
├── compose.yml                # Core DNS & network infrastructure (Pi-hole, networks) <br>
├── compose.edge.yml           # Traefik + Authelia <br>
├── compose.ops.yml            # Watchtower and ops tooling <br>
├── compose.frontend.yml       # Plex, Jellyfin, dashboards <br>
├── compose.pipeline.yml       # VPN, downloaders, *arr stack <br>
├── .env                       # Shared configuration <br>
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

## Networks

* **home-macvlan**

  * Used for services that must appear as first-class hosts on the LAN
* **my-network**

  * Internal Docker bridge for service-to-service communication

Networks are **created by the Core Infrastructure stack** and consumed as external networks by all others.

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
