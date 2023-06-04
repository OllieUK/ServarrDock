# ServarrDock: Self-Hosted Media Management and Automation System

ServarrDock is a comprehensive Docker-based solution for self-hosted media management. It includes a wide range of media-related services, all integrated and managed using Traefik as a reverse proxy, with built-in support for Cloudflare and Let's Encrypt SSL certificates.

## Services Included

This project includes the following services:

### Media Managers

1. [Sonarr](https://sonarr.tv/): A PVR for Usenet and BitTorrent users.
2. [Radarr](https://radarr.video/): A movie collection manager for Usenet and BitTorrent users.
3. [Lidarr](https://lidarr.audio/): A music collection manager for Usenet and BitTorrent users.
4. [Bazarr](https://www.bazarr.media/): A companion program to Sonarr and Radarr that manages and downloads subtitles.

### Download Clients

5. [SABnzbd](https://sabnzbd.org/): A Usenet binary newsreader with a web-based user interface.
6. [qBittorrent](https://www.qbittorrent.org/): A cross-platform free and open-source BitTorrent client.

### Media Players

7. [Plex](https://www.plex.tv/): A platform for managing and streaming digital media.

### Web Frontends

8. [Heimdall](https://heimdall.site/): An application dashboard and launcher.
9. [Ombi](https://ombi.io/): A self-hosted web application that automatically gives your shared Plex or Emby users the ability to request content.

### Other Services

10. [Watchtower](https://containrrr.dev/watchtower/): Automatically updates running Docker containers when a new image is available.
11. [Traefik](https://traefik.io/): A modern HTTP reverse proxy and load balancer.
12. [Gluetun](https://github.com/qdm12/gluetun): A VPN client container supporting multiple providers.
13. [Authelia](https://www.authelia.com/): An open-source authentication and authorization server.
14. [Prowlarr](https://wiki.servarr.com/Prowlarr): A meta-indexer and search aggregator for Usenet and torrent indexers.
15. [Tautulli](https://tautulli.com/): A Plex Media Server monitoring program.

Each service is contained within its own Docker container, allowing it to run independently from the other services. For more information on each service, follow the provided links.

## Folder Structure

ServarrDock keeps downloads and media in the same volume to avoid the need for copying files across volumes when moving from downloaders to the media library. Here's what the folder structure within the media volume looks like:

- `downloads`: This is the main folder where your downloaders will store the files they're currently downloading. It contains two sub-folders:
  - `torrent`: This sub-folder is where torrent downloads are stored.
  - `nzb`: This sub-folder is where nzb downloads are stored.

- `media`: This is the main folder where your media files will be stored. It contains sub-folders for various media types. The structure of these sub-folders is up to you, but you might have separate folders for movies, TV shows, and music, for example.

Both the `downloads` and `media` folders will need to be set up in the configurations of the downloaders and the media managers. This is because the downloaders need to know where to put the files they download, and the media managers need to know where to look for new media files to add to your library.

## Getting Started

To set up and configure ServarrDock to suit your needs, follow the instructions in the [INSTALL.md](./INSTALL.md) file. After setup, you can easily access and manage your services through the domain you've configured and the Heimdall dashboard.