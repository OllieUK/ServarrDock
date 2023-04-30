# Installation Instructions for ServarrDock

## Prerequisites

- Docker and Docker Compose installed on your system
- A domain name properly configured with DNS records
- Your preferred VPN provider's configuration files and credentials

## Installation

1. Clone the ServarrDock repository:

`git clone https://github.com/OllieUK/ServarrDock.git`

2. Change to the ServarrDock directory:

`cd ServarrDock`


3. Copy `.env.example` to `.env`:

`cp .env.example .env`

4. Edit the `.env` file and provide values for all required variables.

5. Start the containers using Docker Compose:

`docker-compose up -d`


## Post-Installation Configuration

### SABnzbd Host Configuration

After completing the installation, you need to configure the SABnzbd service. Add the FQDN (Fully Qualified Domain Name) of your SABnzbd service to the `sabnzbd.ini` file at `./config/sabnzbd.ini`. You need to find the `host_whitelist` setting and add the name of your container, e.g.,

`host_whitelist = sabnzbd.example.com`

### Automatic Updates with Watchtower

ServarrDock uses Watchtower to automatically monitor and update running Docker containers when a new image is available. Watchtower is pre-configured to remove old images after updating and check for updates every 24 hours. No additional configuration is needed for Watchtower, and your services will be updated automatically.