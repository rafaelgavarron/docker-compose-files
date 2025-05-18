# Docker Compose Stack

This repository contains a Docker Compose configuration to run multiple services in a containerized environment.

## Included Services

- **qBittorrent**: Torrent client
- **Obsidian LiveSync**: Synchronization for Obsidian using CouchDB
- **Jellyfin**: Media server
- **Pi-hole**: Network-level ad blocker
- **Minecraft Bedrock Server**: Minecraft Bedrock Edition server
- **Minecraft Backup**: Backup service for the Minecraft server
- **Watchtower**: Automatic container updater

## Prerequisites

- Docker Engine
- Docker Compose
- Linux system (the configuration uses Linux-specific paths)

## Installation and Configuration

### 1. Clone this repository

```bash
git clone [REPOSITORY_URL]
cd [REPOSITORY_NAME]
```

### 2. Directory Structure

Create the necessary directories for volumes:

```bash
# For qBittorrent
mkdir -p /home/rafaelgavarron/qbittorrent/appdata
mkdir -p /home/rafaelgavarron/qbittorrent/downloads

# For Obsidian LiveSync
mkdir -p /mnt/user/appdata/couchdb-obsidian-livesync/data
mkdir -p /mnt/user/appdata/couchdb-obsidian-livesync/etc/local.d

# For Jellyfin
mkdir -p /home/rafaelgavarron/jellyfin/config
mkdir -p /home/rafaelgavarron/jellyfin/cache

# For Pi-hole
mkdir -p /home/rafaelgavarron/etc-pihole

# For Minecraft Bedrock Server
mkdir -p /home/rafaelgavarron/data

# For Minecraft Backup
mkdir -p /minecraft/backup-config
mkdir -p /minecraft/backups
```

### 3. Start services

```bash
docker-compose up -d
```

## Service Details

### qBittorrent

Torrent client with web interface.

- **Web Interface**: http://your-ip:8080
- **Default Credentials**:
  - Username: `admin`
  - Password: `adminadmin`
- **Ports**:
  - 6881 (TCP/UDP): For torrent traffic
  - 8080 (TCP): Web interface
- **Volumes**:
  - `/home/rafaelgavarron/qbittorrent/appdata:/config`: Configuration
  - `/home/rafaelgavarron/qbittorrent/downloads:/downloads`: Downloads

### Obsidian LiveSync (CouchDB)

CouchDB server for Obsidian notes synchronization.

- **Web Interface**: http://your-ip:5984/_utils
- **Credentials**:
  - Username: `obsidian_user`
  - Password: `password`
- **Port**: 5984 (TCP)
- **Volumes**:
  - `/mnt/user/appdata/couchdb-obsidian-livesync/data:/opt/couchdb/data`: Data
  - `/mnt/user/appdata/couchdb-obsidian-livesync/etc/local.d:/opt/couchdb/etc/local.d`: Configuration

### Jellyfin

Media server for your personal collection.

- **Web Interface**: http://your-ip:8096
- **Ports**:
  - 8096 (TCP): Main web interface
  - 8920 (TCP): HTTPS access
- **Volumes**:
  - `/home/rafaelgavarron/jellyfin/config:/config`: Configuration
  - `/home/rafaelgavarron/jellyfin/cache:/cache`: Cache
  - `/home/rafaelgavarron/qbittorrent/downloads:/media`: Media library

### Pi-hole

Network-level ad blocker.

- **Web Interface**: http://your-ip:70/admin
- **API Password**: `gavarron`
- **Ports**:
  - 53 (UDP): DNS
  - 70 (TCP): Web interface (HTTP)
  - 4043 (TCP): Web interface (HTTPS)
- **Volume**:
  - `/home/rafaelgavarron/etc-pihole:/etc/pihole`: Configuration and data

### Minecraft Bedrock Server

Server for Minecraft Bedrock Edition.

- **Port**: 19132 (UDP)
- **Volume**:
  - `/home/rafaelgavarron/data:/data`: Server data
- **Settings**:
  - EULA: Automatically accepted
  - SSH: Enabled
  - Internal port exposure: 2222

### Minecraft Backup

Automated backup service for the Minecraft server.

- **Dependency**: Requires the `Bedrock_Server` service to be running
- **Volumes**:
  - `/minecraft/backup-config:/config`: Backup configuration
  - `/minecraft/backups:/data`: Backup storage
  - `/home/rafaelgavarron/data:/public`: Server data for backup

### Watchtower

Automatically updates Docker containers when new versions are available.

- **Volume**:
  - `/var/run/docker.sock:/var/run/docker.sock`: Docker socket for management

## Service Management

### Start all services
```bash
docker-compose up -d
```

### Stop all services
```bash
docker-compose down
```

### Check logs
```bash
docker-compose logs [service]
```

### Restart a service
```bash
docker-compose restart [service]
```

## Security Considerations

1. **Obsidian LiveSync**: Change the default `password` to something more secure.
2. **Pi-hole**: Consider changing the API password `gavarron` to a more secure password.
3. **Exposed Ports**: Make sure your firewall is properly configured to expose only the necessary ports.

## Backups

- Minecraft data is automatically saved by the backup service
- For other services, consider regular backup of volume directories

## Troubleshooting

### Issue: Container doesn't start
Check the logs to understand the problem:
```bash
docker logs [container_name]
```

### Issue: Service is inaccessible
Check if ports are correctly mapped and there are no conflicts:
```bash
docker-compose ps
```

## Additional Notes

- Watchtower will automatically update all containers when new images are available
- The timezone system is configured to UTC for qBittorrent and America/Sao_Paulo for other services
