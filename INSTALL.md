# ServarrDock Installation Guide

This guide provides detailed instructions on setting up ServarrDock, a comprehensive media management solution organized into separate Docker services for enhanced manageability and security.

## Prerequisites

- Docker and Docker Compose installed on your host system.
- A domain configured with Cloudflare for SSL certificate generation and management.
- Basic knowledge of Docker networking and volume management.

## Installation Steps

### Step 1: Clone the Repository

Clone the ServarrDock repository to your local machine:

```bash
git clone https://github.com/OllieUK/ServarrDock.git
cd ServarrDock
```

### Step 2: Environment Configuration

Copy the `.env.example` file to create your environment settings:

```bash
cp .env.example .env
```

Edit the `.env` file to update it with your specific configurations such as domain names, API keys, and system paths.

### Step 3: SSL Certificates Setup

Create an `acme.json` file in the `./config/traefik` directory for SSL certificates and set its permissions:

```bash
touch ./config/traefik/acme.json
chmod 600 ./config/traefik/acme.json
```

### Step 4: Deploy Infrastructure Services

Start the core infrastructure services:

```bash
docker-compose -f docker-compose-infra.yml up -d
```

This command launches essential services such as Traefik, PiHole, Watchtower, and Authelia.

### Step 5: Deploy Media Services

Once the infrastructure is running, deploy the media services:

```bash
docker-compose -f docker-compose-media.yml up -d
```

This command starts media-related services including download clients, media managers, and indexing services, all secured through the VPN service.

### Step 6: Deploy UI Services

After setting up the infrastructure and media services, deploy the UI services:

```bash
docker-compose -f docker-compose-ui.yml up -d
```

This command initiates user interface services such as Plex, Tautulli, Heimdall, and Ombi, enabling easy access and management of your media.

### Step 7: Verify the Setup

Ensure that all services are operational by accessing them through the Heimdall dashboard or directly via their configured domains. Check the functionality of each service to confirm proper interconnection and security settings.

## Configuration Tips

- **Network Settings**: Ensure that all services are communicating over the correct networks. Services requiring external access should be on `home-macvlan` with appropriate firewall settings configured.

- **Service Dependencies**: Make sure services that depend on others, like Tautulli depending on Plex, are configured to start after their dependencies are fully operational.

- **Security Practices**: Regularly update your `.env` file and Docker configurations to use the latest images and security patches.

## Troubleshooting

If you encounter issues during setup, check the Docker logs for each container to identify configuration errors or missing dependencies. Additionally, verify that all paths and port configurations in the `.env` and Docker Compose files are correct.

For more detailed troubleshooting steps, refer to the documentation for each specific service used within ServarrDock.

## Conclusion

By following these steps, you should have a fully operational ServarrDock environment, organized into manageable components for ease of maintenance and scalability. For further customization and support, participate in the community forums or contribute to the ongoing development of ServarrDock.