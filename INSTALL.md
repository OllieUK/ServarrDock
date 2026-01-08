# **ServarrDock — Installation Guide**

This guide describes how to set up **ServarrDock**, a modular, Docker-based home server environment for media management, home utilities, and supporting infrastructure.

The stack is split into multiple Docker Compose files with clear responsibilities and independent lifecycles.

---

## Prerequisites

* A Linux host with:

  * Docker Engine
  * Docker Compose (plugin-based `docker compose`)
* A domain name (recommended)
* Basic familiarity with Docker networking and volumes

Optional but assumed in this setup:

* Cloudflare-managed DNS (for Traefik TLS certificates)
* Split-horizon DNS (internal resolution to Traefik LAN IP)

---

## Repository Setup

Clone the repository and enter the working directory:

```bash
git clone https://github.com/OllieUK/ServarrDock.git
cd ServarrDock/docker
```

All commands below are assumed to be run from this `docker/` directory.

---

## Environment Configuration

Create and populate the shared environment file:

```bash
cp .env.example .env
```

Edit `.env` to define:

* paths for persistent configuration and media storage
* network parameters (where applicable)
* the canonical domain name (`DOMAIN`)
* API tokens and credentials (where applicable)

This `.env` file is shared by **all** stacks.

---

## Traefik TLS Prerequisites (HTTPS)

Create the ACME storage file for Traefik and secure it:

```bash
mkdir -p ./config/traefik
touch ./config/traefik/acme.json
chmod 600 ./config/traefik/acme.json
```

Traefik terminates TLS for **all user-facing services** under a single hostname.
Only one certificate is required.

---

## DNS Prerequisites (recommended)

This setup uses **a single public hostname** for all user-facing services.

### Canonical model

* `${DOMAIN}` resolves to Traefik’s LAN IP (split-horizon)
* No per-service DNS records are required
* No wildcard subdomains are required

User-facing services are exposed via **URL paths**, not subdomains, e.g.:

```
https://${DOMAIN}/sonarr
https://${DOMAIN}/radarr
https://${DOMAIN}/qbittorrent
```

### Split-horizon DNS

Internal DNS should resolve `${DOMAIN}` to Traefik’s LAN IP.
External DNS should resolve `${DOMAIN}` to the public IP or DDNS endpoint.

This avoids DNS sprawl and certificate SAN leakage.

---

## Step 1 — Deploy Core Network Infrastructure

This stack:

* creates the shared Docker networks used by all other stacks

Run:

```bash
docker compose up -d
```

Verify networks were created:

```bash
docker network ls | egrep 'media-net|my-network'
```

Nothing else should be started before this step.

---

## Step 2 — Deploy Edge Services (Ingress & Authentication)

This stack provides external access and authentication.

```bash
docker compose -p edge -f compose.edge.yml up -d
```

At this point:

* Traefik is reachable via `https://${DOMAIN}`
* Authelia protects user-facing services
* HTTP (80) is globally redirected to HTTPS (443)

---

## Step 3 — Deploy Operations Services

This stack runs background operational tooling.

```bash
docker compose -p ops -f compose.ops.yml up -d
```

By default, Watchtower updates all containers unless explicitly excluded via labels.

---

## Step 4 — Deploy Media Frontend Services

This stack contains user-facing services such as media servers and dashboards.

```bash
docker compose -p frontend -f compose.frontend.yml up -d
```

Examples of reachable services:

```
https://${DOMAIN}/        (landing page)
https://${DOMAIN}/ombi
https://${DOMAIN}/tautulli
```

---

## Step 5 — Deploy Media Acquisition & Automation Pipeline

This stack handles downloading, indexing, and automation.

```bash
docker compose -p pipeline -f compose.pipeline.yml up -d
```

This stack is designed for frequent updates and restarts without impacting user-facing services.

---

## Indexer Proxy Configuration (Prowlarr + Gluetun)

When using **Gluetun** as a VPN gateway and exposing its built-in HTTP proxy to other services (e.g. Prowlarr), it is **not sufficient** to configure a generic proxy in Prowlarr’s global settings.

Prowlarr requires an explicit **HTTP Indexer Proxy** object.

### Correct Configuration Pattern

1. Ensure Gluetun’s HTTP proxy is enabled and reachable (e.g. `gluetun:8888`)
2. In Prowlarr, navigate to:
   *Settings → Indexer Proxies*
3. Create a new proxy of type:
   **HTTP Indexer Proxy**
4. Configure:

   * Host: `gluetun`
   * Port: `8888`
   * Authentication: none
5. Assign this Indexer Proxy to the relevant indexers

### Important Notes

* The global proxy setting alone is insufficient
* HTTPS indexers require CONNECT tunnelling
* Misconfiguration typically results in `503` or tunnel errors

---

## Verification

Check container status and network attachment:

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Networks}}"
```

Verify user-facing access:

```
https://${DOMAIN}/sonarr
https://${DOMAIN}/radarr
https://${DOMAIN}/sabnzbd
```

---

## Notes on Path-Based Routing

* Path-based routing is the **default**
* Applications are configured with native `urlbase` / `baseUrl` settings
* Traefik does **not** rewrite paths by default
* `StripPrefix` is used only as a targeted workaround for misbehaving applications

---

## Troubleshooting

* Inspect logs:

  ```bash
  docker logs <container>
  ```
* Verify network attachment:

  ```bash
  docker inspect <container> | grep -A5 Networks
  ```
* Ensure `.env` values match the actual environment

---

## Philosophy

This setup prioritises:

* clarity over cleverness
* minimal DNS and certificate exposure
* small blast radius
* boring, predictable ingress
