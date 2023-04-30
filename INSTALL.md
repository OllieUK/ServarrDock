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

## Folder Setup

To set up the folder structure for your downloads and media, follow these steps:

1. Create the `downloads` and `media` folders within the `./media` volume, if they don't exist already.
2. Inside the `downloads` folder, create two sub-folders named `torrent` and `nzb`.
3. Inside the `media` folder, create sub-folders for each media type you wish to store, such as `movies`, `tv`, `music`, etc.

After setting up the folder structure, you need to configure the downloaders and media managers to use these folders:

1. Configure the downloaders (SABnzbd and qBittorrent) to save downloaded files to the respective `torrent` or `nzb` sub-folders within the `downloads` folder.
2. Configure the media managers (Radarr, Sonarr, Lidarr) to scan and organize media files within the `media` folder and its sub-folders.

By following these steps, you will have a seamless integration between downloaders and media managers, without the need to copy files between volumes.
