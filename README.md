# **ServarrDock — Self-Hosted Media & Home Services Stack**

**WARNING**
This stack is actively evolving. Documentation reflects the **current stable architecture**, not historical layouts.

---

## Overview

ServarrDock is a modular, Docker-based home server environment for media management, home utilities, and supporting infrastructure.

It is designed around:

* clear service boundaries
* low blast radius
* independent lifecycles
* deliberate ingress design

---

## High-Level Architecture

ServarrDock is organised into several **logical stacks**, each managed by its own Docker Compose file.

---

### Core Network Infrastructure

**Purpose:** foundational networks required by everything else.

Managed by `compose.yml`.

Includes:

* `media-net`
  Shared control-plane network for edge and media stacks
* `my-network`
  General internal bridge for non-media services

This stack must be running before any other stack.

---

### Edge Services (Ingress & Authentication)

**Purpose:** secure external access.

Managed by `compose.edge.yml`.

Includes:

* **Traefik** — reverse proxy, TLS termination, routing
* **Authelia** — authentication and access control

**Ingress model:**

* HTTPS only (443/tcp)
* Single canonical hostname
* Path-based routing by default

---

### Operations Services

**Purpose:** background maintenance.

Managed by `compose.ops.yml`.

Includes:

* Watchtower (container updates)

Isolated from user-facing services.

---

### Media Frontend Services

**Purpose:** user-facing access and dashboards.

Managed by `compose.frontend.yml`.

Examples:

* Plex
* Jellyfin
* Tautulli
* Heimdall (landing page)
* Ombi
* Audiobookshelf

---

### Media Acquisition & Automation Pipeline

**Purpose:** downloading and automation.

Managed by `compose.pipeline.yml`.

Examples:

* Gluetun (VPN gateway)
* SABnzbd
* qBittorrent
* Prowlarr
* Sonarr / Radarr / Lidarr / Bazarr / LazyLibrarian

High-churn by design.

---

## User-Facing Ingress Model

### Canonical access pattern

```
https://${DOMAIN}/
```

This resolves to the landing page.

### Path-based services (default)

```
/sonarr
/radarr
/lidarr
/bazarr
/prowlarr
/sabnzbd
/qbittorrent
/ombi
/audiobookshelf
/tautulli
```

Characteristics:

* single hostname
* no DNS sprawl
* minimal certificate exposure
* application-native `urlbase` configuration

---

### Host-based exceptions (intentional)

Some services are published under dedicated hostnames due to technical constraints:

* Plex
* Jellyfin
* Jellyseerr
* Traefik (control plane)
* Authelia (authentication)

These are **explicit exceptions**, not legacy artefacts.

---

## Networking and Name Resolution

### Internal communication

Services communicate using Docker DNS on `media-net`, e.g.:

```
http://sonarr:8989
http://radarr:7878
http://gluetun:8888
```

LAN DNS is never required for container-to-container traffic.

---

### External DNS

Only `${DOMAIN}` must resolve to Traefik.
No per-service records are required.

---

## Design Principles

* Prefer **path-based ingress**
* Let applications own their URL space
* Keep Traefik thin and predictable
* Avoid inventory disclosure via DNS and TLS
* Make exceptions explicit and documented

---

## Status

This is a living system.
Documentation is updated **after architecture stabilises**, not before.
