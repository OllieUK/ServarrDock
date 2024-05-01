# ServarrDock Installation Guide

This guide will walk you through the installation and configuration process for setting up ServarrDock, a comprehensive Docker-based media management solution.

## Prerequisites

- Docker and Docker Compose installed on your system.
- A domain configured with Cloudflare for SSL certificate generation and management.
- An understanding of Docker networking and volume management.

## Installation Steps

### Step 1: Clone the Repository
Clone the ServarrDock repository from GitHub:

```bash
git clone https://github.com/OllieUK/ServarrDock.git
cd ServarrDock
```

### Step 2: Prepare Environment Files
Copy the `.env.example` file to a new file named `.env` and update it with your personal settings, API keys, and credentials:

```bash
cp .env.example .env
```

Edit the `.env` file to ensure all necessary configurations, such as domain names, API keys, and personal settings, are correctly set.

### Step 3: SSL Certificate Storage
Create a file named `acme.json` in the `./config/traefik` directory to store your SSL certificates, and set its permissions for security:

```bash
touch ./config/traefik/acme.json
chmod 600 ./config/traefik/acme.json
```

### Step 4: Deploy Infrastructure Services
Deploy the core network services using the infrastructure Docker compose file:

```bash
docker-compose -f docker-compose-infra.yml up -d
```

This starts the necessary network services including Traefik, PiHole, Watchtower, and Authelia.

### Step 5: Deploy Media Services
Once the infrastructure is running, deploy the media services:

```bash
docker-compose -f docker-compose-media.yml up -d
```

This will start all media-related services like Sonarr, Radarr, Plex, and download clients.

### Step 6: Configure Services
Configure each service to ensure they are properly interconnected and secure. Specific attention should be paid to the following settings:

#### URL Base Configuration
For services accessed through Traefik with a URL prefix (e.g., `/radarr` for Radarr):
- Ensure that the URL Base in each media manager matches the URL Prefix used in Traefik. This setting is typically found in the application's web interface under settings.

#### Download Clients
Configure SABnzbd and qBittorrent to use the VPN service for secure downloads and set their URL Bases to align with Traefik routing:

```yaml
network_mode: "service:gluetun"
```

#### Plex Configuration
Ensure Plex is accessible from outside your network by setting up port forwarding, if necessary. Refer to the 'Plex Port Forwarding and Extra Port Mappings' section below for details.

### Step 7: Verify Operation
After configuration, ensure that all services are operational by accessing them via your configured domain and Heimdall dashboard. Check connectivity between services using their respective APIs.

## Plex Port Forwarding and Extra Port Mappings

### Manual Port Forwarding
If UPnP is not sufficient, manually configure port forwarding on your router. Forward TCP and UDP port 32400 to the host running Plex.

### Extra Port Mappings for Plex
Add the following port mappings in the `docker-compose-media.yml` file under the Plex service if needed:

```yaml
- "32400:32400"
- "32400:32400/udp"
- "32469:32469"
- "32469:32469/udp"
- "5353:5353/udp"
- "1900:1900/udp"
```

## Final Steps
After setting up port forwarding and ensuring that all services are communicating correctly, navigate to the Plex web interface and configure 'Remote Access' with the manual port forwarding settings.

For detailed configuration guides for each service, refer to their official documentation linked in the README.md.