# Gitea Docker Setup

This repository contains a Docker Compose configuration for running Gitea with PostgreSQL.

## Prerequisites

- Docker and Docker Compose installed
- Nginx installed on host (for reverse proxy)
- Domain name configured to point to your server

## Environment Variables

Create a `.env` file in the project root with the following variables:

```env
POSTGRES_DB=gitea
POSTGRES_USER=gitea
POSTGRES_PASSWORD=your_secure_password
GITEA_DOMAIN=git.example.com
```

## Quick Start

1. Clone this repository and navigate to the directory
2. Create the `.env` file with your configuration
3. Start the services:

```bash
docker-compose up -d
```

Gitea will be accessible at `http://127.0.0.1:3000` and SSH at port `222`.

## Nginx Configuration

The repository includes example Nginx configurations in the `nginx.example/conf.d/` directory:

- `gitea.httponly.conf` - HTTP-only configuration (use this first)
- `gitea.conf` - HTTPS configuration with SSL (use after obtaining certificates)

### Initial Setup (HTTP Only)

1. Copy the HTTP-only configuration to your Nginx config directory:

```bash
sudo cp nginx.example/conf.d/gitea.httponly.conf /etc/nginx/conf.d/gitea.conf
```

2. Update the configuration file with your domain name
3. Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Setting Up HTTPS with Let's Encrypt

1. Install certbot:

```bash
sudo dnf install certbot python3-certbot-nginx
```

2. Create webroot directory:

```bash
sudo mkdir -p /var/www/certbot
```

3. Obtain SSL certificate:

```bash
sudo certbot certonly --webroot \
    -w /var/www/certbot \
    -d git.example.com \
    --email your-email@example.com \
    --agree-tos \
    --no-eff-email
```

4. Switch to HTTPS configuration:

```bash
sudo cp nginx.example/conf.d/gitea.conf /etc/nginx/conf.d/gitea.conf
```

5. Update the configuration with your domain and certificate paths
6. Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Services

### Gitea Server

- **Image**: `docker.gitea.com/gitea:1.25.4`
- **HTTP Port**: 3000 (localhost only)
- **SSH Port**: 222 (accessible externally)
- **Data Volume**: `./gitea:/data`

### PostgreSQL Database

- **Image**: `postgres:14`
- **Data Volume**: `./postgres:/var/lib/postgresql/data`

## Data Persistence

Data is persisted in the following local directories:

- `./gitea` - Gitea application data
- `./postgres` - PostgreSQL database data

Make sure to back up these directories regularly.

## Accessing Gitea

- **Web Interface**: Access through your configured domain (e.g., https://git.example.com)
- **SSH**: Use port 222 for Git operations over SSH

```bash
git clone ssh://git@git.example.com:222/username/repository.git
```

## Updating

To update Gitea to a newer version:

1. Edit `docker-compose.yaml` and change the image version
2. Pull the new image and recreate the container:

```bash
docker-compose pull
docker-compose up -d
```

## Troubleshooting

Check container logs:

```bash
docker-compose logs -f server
docker-compose logs -f db
```

## Security Notes

- The Gitea HTTP port (3000) is bound to 127.0.0.1 only, accessible via Nginx reverse proxy
- SSH is accessible on port 222 (non-standard port for added security)
- Change default database credentials in the `.env` file
