# ServarrDock — Installation Guide

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

```
git clone https://github.com/OllieUK/ServarrDock.git
cd ServarrDock/docker
```

All commands below are assumed to be run from this `docker/` directory.

---

## Environment Configuration

Create and populate the shared environment file:

```
cp .env.example .env
```

Edit `.env` to define:

* paths for persistent configuration and media storage
* network parameters (where applicable)
* domain names and API tokens (where applicable)

This `.env` file is shared by **all** stacks.

---

## Traefik TLS Prerequisites (if using HTTPS)

Create the ACME storage file for Traefik and secure it:

```
mkdir -p ./config/traefik
touch ./config/traefik/acme.json
chmod 600 ./config/traefik/acme.json
```

Ensure any dynamic Traefik configuration files referenced in the compose files exist.

---

## DNS Prerequisites (recommended)

This setup assumes host-based routing (subdomains) for services behind Traefik, e.g.

* `sonarr.<domain>`
* `radarr.<domain>`
* `jellyseerr.<domain>`

Internal DNS should resolve `*.<domain>` to Traefik’s LAN IP (split-horizon).

One implementation pattern:

* dnsmasq returns Traefik’s LAN IP for that zone (via an `address=/*.<domain>/<traefik-ip>` style rule)
  This avoids having to create a DNS host (A) or CNAME entry for every service.

---

## Step 1 — Deploy Core Network Infrastructure

This stack:

* creates the shared Docker networks used by all other stacks

Run:

```
docker compose up -d
```

Verify networks were created:

```
docker network ls | egrep 'media-net|my-network'
```

Nothing else should be started before this step.

---

## Step 2 — Deploy Edge Services (Ingress & Authentication)

This stack provides external access and authentication.

```
docker compose -p edge -f compose.edge.yml up -d
```

At this point, Traefik and Authelia should be reachable via their configured domains.

---

## Step 3 — Deploy Operations Services

This stack runs background operational tooling.

```
docker compose -p ops -f compose.ops.yml up -d
```

By default, Watchtower will update all containers unless explicitly excluded via labels.

---

## Step 4 — Deploy Media Frontend Services

This stack contains user-facing services such as media servers and dashboards.

```
docker compose -p frontend -f compose.frontend.yml up -d
```

Verify services are reachable through Traefik using their subdomains.

---

## Step 5 — Deploy Media Acquisition & Automation Pipeline

This stack handles downloading, indexing, and automation.

```
docker compose -p pipeline -f compose.pipeline.yml up -d
```

This stack is designed for frequent updates and restarts without impacting user-facing services.

---

## Indexer Proxy Configuration (Prowlarr + Gluetun)

When using **Gluetun** as a VPN gateway and exposing its built-in HTTP proxy to other services (e.g. Prowlarr), it is **not sufficient** to configure a generic proxy in Prowlarr’s global settings.

Prowlarr requires an explicit **HTTP Indexer Proxy** object to be created.

### Correct Configuration Pattern

1. Ensure Gluetun’s HTTP proxy is enabled and reachable (e.g. `gluetun:8888`) and that the proxy supports HTTPS CONNECT tunnelling.

2. In Prowlarr, navigate to:

   *Settings → Indexer Proxies*

3. Create a new proxy of type:

   **HTTP Indexer Proxy**

4. Configure it with:

   * Host: `gluetun`
   * Port: `8888`
   * Authentication: none (unless explicitly required)

5. Assign this Indexer Proxy to the relevant indexers.

### Important Notes

* Adding proxy details only under *Settings → General → Proxy* is **insufficient** for indexer traffic.
* Indexer HTTPS requests use explicit CONNECT tunnelling and will fail unless an **Indexer Proxy** is defined.
* This distinction is critical when troubleshooting `503` or "proxy tunnel" errors in Prowlarr.

This behaviour is by design in Prowlarr and must be documented to avoid repeated misconfiguration.

---

## Verification

Check that all containers are running and attached to the expected networks:

```
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Networks}}"
```

For service-to-service communication, prefer Docker DNS service names on `media-net`, e.g. `http://sonarr:8989`.

---

## Updating and Maintenance

* Individual stacks can be restarted independently.
* Watchtower manages container updates unless explicitly disabled per service.
* Infrastructure changes (DNS, networks, ingress) should be performed deliberately and infrequently.

---

## Troubleshooting

* Inspect container logs:

  docker logs <container-name>

* Verify network attachment:

  docker inspect <container-name> | grep -A5 Networks

* Ensure `.env` values match your actual host and network environment.

---

## Notes

This setup prioritises:

* clear service boundaries
* low blast radius
* operational clarity over convenience shortcuts

It is expected to evolve over time; documentation is updated when structure stabilises.
