# ServarrDock: Self-Hosted Media Management and Automation System

ServarrDock is a comprehensive Docker-based solution for self-hosted media management. It integrates a wide range of media-related services, all managed using Docker. Services are segregated into two distinct configurations to enhance manageability and security: one for core network infrastructure and one for media services.

## System Architecture

ServarrDock is structured into three main parts:

### Infrastructure Services
Managed by the `docker-compose-infra.yml` file, this includes essential services for network management and security:
- **Traefik**: Serves as a reverse proxy and load balancer with built-in support for Cloudflare and Let's Encrypt SSL certificates.
- **Authelia**: Provides authentication and authorization to protect your services.
- **Watchtower**: Automatically updates running Docker containers.
- **PiHole**: Acts as a network-wide ad blocker and DNS server.

### Media Services
Managed by the `docker-compose-media.yml` file, this setup handles all media processing tasks:
- **Downloaders**: SABnzbd and qBittorrent, secured through Gluetun VPN service.
- **Media Managers**: Sonarr, Radarr, Lidarr, and Bazarr for managing TV shows, movies, and music.
- **Indexer**: Prowlarr for managing indexers and integrating them with media managers.

### UI Services
Managed by the `docker-compose-ui.yml` file, this includes user interfaces for media access and management:
- **Plex**: Manages and streams your digital media.
- **Tautulli**: Monitors activity and provides statistics about your Plex server.
- **Heimdall**: Application dashboard for accessing and managing all your services.
- **Ombi**: Allows users to request media for Plex, managing user requests.


## Services Included

### Media Managers

1. [Sonarr](https://sonarr.tv/): A PVR for Usenet and BitTorrent users.
2. [Radarr](https://radarr.video/): A movie collection manager for Usenet and BitTorrent users.
3. [Lidarr](https://lidarr.audio/): A music collection manager for Usenet and BitTorrent users.
4. [Bazarr](https://www.bazarr.media/): A companion program to Sonarr and Radarr that manages and downloads subtitles.
5. [Prowlarr](https://wiki.servarr.com/Prowlarr): A meta-indexer and search aggregator for Usenet and torrent indexers.

### Download Clients

6. [SABnzbd](https://sabnzbd.org/): A Usenet binary newsreader with a web-based user interface.
7. [qBittorrent](https://www.qbittorrent.org/): A cross-platform free and open-source BitTorrent client.

### Media Players

8. [Plex](https://www.plex.tv/): A platform for managing and streaming digital media.

### Web Frontends

9. [Heimdall](https://heimdall.site/): An application dashboard and launcher.
10. [Ombi](https://ombi.io/): A self-hosted web application that automatically gives your shared Plex or Emby users the ability to request content.

## Folder Structure

ServarrDock organizes downloads and media in the same volume:
- `downloads/torrent`: For torrent downloads.
- `downloads/nzb`: For nzb downloads.
- `media`: Organized into sub-folders for movies, TV shows, and music.

## Getting Started

To set up and configure ServarrDock, follow the detailed instructions in the [INSTALL.md](./INSTALL.md) file. Setup involves configuring the `docker-compose-infra.yml`, `docker-compose-media.yml`, and `docker-compose-ui.yml` files according to your network and security preferences.

After setup, manage your services via the Heimdall dashboard and the domain you have configured.

## Contributions and Support

Contributions are welcome. Please fork the repository and submit a pull request with your enhancements.

For issues and support, please file tickets on the project's GitHub issue tracker.