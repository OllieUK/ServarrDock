# ServarrDock
This is a (semi-)automated setup for a a home media server, using Servarr services for media management and SABnzbd as a download client.

While most of the configuration is done via the docker-compose.yml and .env files, there are are one or two manual steps that also need to be done for this to work:

** SABNZBD Host Configuration
Add the FQDN of your SABnzbd service to the sabnzbd.ini file at ./config/sabnzbd.ini
You need to find the 'host_whitelist' setting and add the name of your container, e.g.
host_whitelist = sabnzbd.example.com

## Services Overview

                                    +-----------------+
                                    |                 |
                                +--->    Traefik      |
                                |   | (Reverse Proxy) |
                                |   |                 |
                                |   +--------+--------+
                                |            |
+-------------------------------+------------+-----------------------------+
|                               |            |                             |
|  +--------+  +--------+  +----v----+  +----v----+  +----v----+  +--------+ |
|  |        |  |        |  |         |  |         |  |         |  |        | |
|  | Sonarr +--+ Radarr +--+ Lidarr +--+ Prowlarr +--+ Heimdall|  | Gluetun| |
|  |        |  |        |  |         |  |         |  |         |  |        | |
|  +----+---+  +----+---+  +----+----+  +----+----+  +----+----+  +----+---+ |
|       |           |           |           |           |           |       |
|       |           |           |           |           |           |       |
+-------+-----------+-----------+-----------+-----------+-----------+-------+
        |           |           |           |           |           |
        |           |           |           |           |           |
+-------v-----------v-----------v-----------v-----------v-----------v-------+
|                                                                          |
|  +--------+  +--------+               +------------------+               |
|  |        |  |        |               |                  |               |
|  | SABnzbd +--+ qBittorrent           |    Gluetun       |               |
|  |        |  |        |               |   (VPN Client)   |               |
|  +--------+  +--------+               +------------------+               |
|                                                                          |
+--------------------------------------------------------------------------+
