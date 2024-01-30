# ServarrDock Installation Guide

This guide will walk you through the installation and configuration process for setting up ServarrDock, a comprehensive media management solution.

## Prerequisites

- Docker and Docker Compose installed on your system
- A domain configured with Cloudflare for SSL certificate generation and management
- An understanding of the services included in ServarrDock and their dependencies

## Installation Steps

1. Clone the [ServarrDock repository](https://github.com/OllieUK/ServarrDock) from GitHub.
2. Copy the `.env.example` file to a new file named `.env`.
3. Update the `.env` file with your personal settings, API keys, and credentials.
4. Create a file named `acme.json` in the `./config/traefik` directory to store your SSL certificates, and set its permissions to 600: `chmod 600 acme.json`.
5. Run `docker-compose up -d` to start the services.
6. Set up Plex so it can communicate with the outside world. This is called 'port forwarding' and you might also need to add extra port mappings (see the section below).
7. **Configure and Set Up Each Service**:
   Configure each service according to the order specified below. For services that are accessed through Traefik with a URL prefix, such as `/radarr` for Radarr or `/sabnzbd` for SABnzbd, you must set the URL Base within each application's settings to match this prefix. This configuration is typically found in the application's web interface under settings. In some cases, it may be necessary to directly modify the application's configuration files, which are located in the container's config location as specified in the `.env` file.

   - **Media Managers (Sonarr, Radarr, Lidarr):**
     Ensure that the URL Base in each media manager is set to match the URL Prefix used in Traefik (e.g., `/sonarr`, `/radarr`, `/lidarr`).
   - **Downloaders (SABnzbd, qBittorrent):**
     Set the URL Base in SABnzbd and qBittorrent to correspond with the Traefik URL Prefix (e.g., `/sabnzbd` for SABnzbd).
   - **Gluetun, Prowlarr, Plex:**
     Follow standard setup procedures as these services typically do not require a URL Base setting.
   - **Ombi:**
     Configure Ombi after setting up media managers and Plex. Ensure Ombi is aware of the URL Base or prefixes if accessed through Traefik.
   - **Heimdall:**
     Set up Heimdall as your dashboard to access all services from a central location.
8. Once all services are up and running, configure the inter-service connections using their API keys and endpoints.

## Plex Port Forwarding and Extra Port Mappings

By default, Plex attempts to use UPnP to automatically configure port forwarding on your router. However, this may not work in all scenarios, particularly when using Docker with Bridge networking. In such cases, you'll need to configure port forwarding manually.

### Extra Port Mappings

To ensure that Plex can be accessed remotely, you may need to add extra port mappings to your `docker-compose.yml` file for the Plex service. Add the following lines under the ports section for the Plex container:

```yaml
- "32400:32400"
- "32400:32400/udp"
- "32469:32469"
- "32469:32469/udp"
- "5353:5353/udp"
- "1900:1900/udp"
```

This will ensure that the necessary ports for Plex are mapped to the host system.

### Static Port Forwarding on the Internet Router

If UPnP is not working for you, you'll need to set up static port forwarding on your Internet router. This process varies depending on your router model, so refer to your router's documentation for instructions on how to set up port forwarding.

Forward the following ports from your router to the IP address of the device running the Plex container:

- TCP: 32400 (required for remote access)
- UDP: 32400 (required for remote access)

Once you've set up port forwarding on your router, return to the Plex web interface and navigate to Settings > Remote Access. Update the settings to reflect the manual port forwarding configuration, enter the port number (32400 by default), and click on "Retry" or "Enable Remote Access."

## Dependencies and Configuration Order

It's essential to set up and configure the services in the following order due to their dependencies:

- Media managers (Sonarr, Radarr, Lidarr) and Plex: These services should be set up and running before configuring Ombi, as it relies on their API endpoints.
- Ombi: After the media managers and Plex are running, you can set up Ombi to handle requests and user management.

For detailed instructions on how to configure each service, refer to their respective documentation.

## Volumes and Folder Structure

When setting up the services, ensure that the appropriate volumes and folder structures are in place. The `./media` volume should contain a folder for downloads, with subfolders for torrent and nzb downloads, and a folder for media, with subfolders for different media types. The structure of the media subfolders is up to the user. Both will need to be set up in the configurations of the downloaders and the media managers.