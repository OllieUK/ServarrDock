# ServarrDock — Installation Guide

This guide describes how to set up **ServarrDock**, a modular, Docker-based home server environment for media management, home utilities, and supporting infrastructure.

The stack is split into multiple Docker Compose files with clear responsibilities and independent lifecycles.

---

## Prerequisites

- A Linux host with:
  - Docker Engine
  - Docker Compose (plugin-based `docker compose`)
- A domain name (optional but recommended)
- Basic familiarity with Docker networking and volumes

Optional but assumed in this setup:
- Cloudflare-managed DNS (for Traefik TLS certificates)
- A host network interface suitable for macvlan use

---

## Repository Setup

Clone the repository and enter the working directory:

    git clone https://github.com/OllieUK/ServarrDock.git
    cd ServarrDock/docker

All commands below are assumed to be run from this `docker/` directory.

---

## Environment Configuration

Create and populate the shared environment file:

    cp .env.example .env

Edit `.env` to define:

* paths for persistent configuration and media storage
* network parameters (subnet, gateway, interface name)
* domain names and API tokens (where applicable)

This `.env` file is shared by **all** stacks.

---

## Traefik TLS Prerequisites (if using HTTPS)

Create the ACME storage file for Traefik and secure it:


    mkdir -p ./config/traefik
    touch ./config/traefik/acme.json
    chmod 600 ./config/traefik/acme.json

Ensure any dynamic Traefik configuration files referenced in the compose files exist.

---

## Step 1 — Deploy Core DNS & Network Infrastructure

This stack:

* brings up Pi-hole
* creates the shared Docker networks used by all other stacks

Run:

    docker compose up -d

Verify networks were created:

    docker network ls | egrep 'home-macvlan|my-network'

Nothing else should be started before this step.

---

## Step 2 — Deploy Edge Services (Ingress & Authentication)

This stack provides external access and authentication.

    docker compose -p edge -f compose.edge.yml up -d

At this point, Traefik and Authelia should be reachable via their configured domains.

---

## Step 3 — Deploy Operations Services

This stack runs background operational tooling.

    docker compose -p ops -f compose.ops.yml up -d

By default, Watchtower will update all containers unless explicitly excluded via labels.

---

## Step 4 — Deploy Media Frontend Services

This stack contains user-facing services such as media servers and dashboards.

    docker compose -p frontend -f compose.frontend.yml up -d

Verify that Plex/Jellyfin and dashboards are reachable through Traefik.

---

## Step 5 — Deploy Media Acquisition & Automation Pipeline

This stack handles downloading, indexing, and automation.

    docker compose -p pipeline -f compose.pipeline.yml up -d

This stack is designed for frequent updates and restarts without impacting user-facing services.

---

## Verification

Check that all containers are running and attached to the expected networks:

    docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Networks}}"

Services should resolve each other by **service name** on `my-network`.

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