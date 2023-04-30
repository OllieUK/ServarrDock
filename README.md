# ServarrDock: Self-Hosted Media Management and Automation System

ServarrDock provides a comprehensive self-hosted media management and automation system that aims to simplify and streamline the process of downloading, organizing, and accessing your media content.

The system includes a reverse proxy (Traefik) that makes it easy to access all your services through a single domain with HTTPS. It is integrated with a variety of services for media library management, including TV shows, movies, and music. These services automate the process of searching, downloading, and organizing media files from Usenet and torrent sources. The system also features a VPN client (Gluetun) that secures the downloaders' network traffic, ensuring privacy and security.

Additionally, the system includes a meta-indexer and search aggregator (Prowlarr) that simplifies the integration between the media library managers and indexers. To make managing your services more accessible, a web-based front-end dashboard (Heimdall) is included, allowing you to organize, manage, and access all your self-hosted services in one place.

This project is designed to be easily extensible, allowing you to add or remove services as needed, according to your preferences.

## Components

- Traefik: Reverse proxy that manages and routes traffic to various services.
- Media Library Managers: Automate the process of searching, downloading, and organizing media files from Usenet and torrent sources.
- Gluetun: VPN client that secures the network traffic of the downloaders.
- Prowlarr: Meta-indexer and search aggregator that simplifies integration between media library managers and indexers.
- Heimdall: Web-based front-end dashboard for organizing, managing, and accessing your self-hosted services.

## Getting Started

Follow the instructions in the `INSTALL.md` file to set up and configure the system to suit your needs. After setup, you can easily access and manage your services through the domain you've configured and the Heimdall dashboard.