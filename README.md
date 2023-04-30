# ServarrDock: Self-Hosted Media Management and Automation System

ServarrDock is a comprehensive Docker-based solution for self-hosted media management. It includes a wide range of media-related services such as Sonarr, Radarr, Lidarr, downloaders, Plex, Ombi, and more. These services are integrated and managed using Traefik as a reverse proxy, with built-in support for Cloudflare and Let's Encrypt SSL certificates.

## Services Included

- Sonarr, Radarr, Lidarr: Media managers for TV shows, movies, and music respectively
- SABnzbd and qBittorrent: Downloaders for Usenet and torrent content
- Gluetun: VPN service for secure and private downloading
- Prowlarr: Meta-indexer and search aggregator for Usenet and torrent indexers
- Plex: Media server for organizing and streaming your media library
- Ombi: Request management and user management tool, integrated with Plex, Sonarr, Radarr, and Lidarr
- Heimdall: Web-based front-end dashboard for organizing and accessing all your self-hosted services
- Watchtower: Automatic Docker container updates

## Folder Structure

ServarrDock keeps downloads and media in the same volume to avoid the need for copying files across volumes when moving from downloaders to the media library.

The folder structure within the `media` volume is as follows:

- `downloads`: Contains sub-folders for torrent and nzb downloads.
  - `torrent`: Stores torrent downloads.
  - `nzb`: Stores nzb downloads.
- `media`: Contains sub-folders for various media types. The structure of these sub-folders is up to the user.

Both folders will need to be set up in the configurations of the downloaders and the media managers.

## Getting Started

Follow the instructions in the `INSTALL.md` file to set up and configure the system to suit your needs. After setup, you can easily access and manage your services through the domain you've configured and the Heimdall dashboard.