# ServarrDock Installation Guide

This guide will walk you through the installation and configuration process for setting up ServarrDock, a comprehensive media management solution.

## Prerequisites

- Docker and Docker Compose installed on your system
- A domain configured with Cloudflare for SSL certificate generation and management
- An understanding of the services included in ServarrDock and their dependencies

## Installation Steps

1. Clone the ServarrDock repository from [GitHub](https://github.com/OllieUK/ServarrDock).
2. Copy the `.env.example` file to a new file named `.env`.
3. Update the `.env` file with your personal settings, API keys, and credentials.
4. Run `docker-compose up -d` to start the services.
5. Configure and set up each service according to the order specified below:
   - Media managers (Sonarr, Radarr, Lidarr)
   - Downloaders (SABnzbd, qBittorrent)
   - Gluetun
   - Prowlarr
   - Plex
   - Ombi (requires media managers and Plex to be running)
   - Heimdall
6. Once all services are up and running, configure the inter-service connections using their API keys and endpoints.

## Dependencies and Configuration Order

It's essential to set up and configure the services in the following order due to their dependencies:

1. Media managers (Sonarr, Radarr, Lidarr) and Plex: These services should be set up and running before configuring Ombi, as it relies on their API endpoints.
2. Ombi: After the media managers and Plex are running, you can set up Ombi to handle requests and user management.

For detailed instructions on how to configure each service, refer to their respective documentation.

## Volumes and Folder Structure

When setting up the services, ensure that the appropriate volumes and folder structures are in place. The `./media` volume should contain a folder for downloads, with subfolders for torrent and nzb downloads, and a folder for media, with subfolders for different media types. The structure of the media subfolders is up to the user. Both will need to be set up in the configurations of the downloaders and the media managers.