## Dockery

A small collection of Docker Compose stacks to run a self‑hosted media setup: VPN‑routed downloaders, the Servarr apps, Jellyfin with stats, and a simple homepage dashboard.

### Stacks

- **downloader-compose.yaml**: `gluetun` (VPN) + `qbittorrent` + `nzbget` (downloaders route traffic through the VPN container)
- **servarr-compose.yaml**: `jellyseerr`, `prowlarr`, `sonarr`, `radarr`
- **jellyfin-compose.yaml**: `jellyfin` media server + `jellystat` with a `postgres` DB
- **hompage-compose.yaml**: `homepage` dashboard

### Prerequisites

- **Docker** and **Docker Compose** installed
- Create a `.env` with your VPN credentials (used by `gluetun`) and adjust paths/timezone as needed
- Optional: **Portainer** for easier compose management

Example `.env` (edit values):

```
OPENVPN_USER=your_nordvpn_username
OPENVPN_PASSWORD=your_nordvpn_password
```

### Quick start

1. Review and adjust bind‑mount paths in the compose files (e.g. `/docker/...`, `/mnt/media/...`). Create missing directories on the host.
2. Start any stack you need (or by using Portainer):

```
docker compose -f downloader-compose.yaml up -d
docker compose -f servarr-compose.yaml up -d
docker compose -f jellyfin-compose.yaml up -d
docker compose -f hompage-compose.yaml up -d
```

### Default ports (host)

- **Homepage**: 3000
- **Jellyfin**: 8096 (HTTP), 8920 (HTTPS, optional), 7359/UDP, 1900/UDP
- **Jellystat**: 3001
- **Jellyseerr**: 5055
- **Prowlarr**: 9696
- **Sonarr**: 8989
- **Radarr**: 7878
- **NZBGet** (via gluetun): 6789
- **qBittorrent** (via gluetun): 8081 (Web UI), 6881 TCP/UDP

### Notes

- In the downloader stack, only `gluetun` exposes ports; `qbittorrent` and `nzbget` share its network namespace so all traffic goes through the VPN.
- The `gluetun` config here is set up for NordVPN + OpenVPN by default. You can switch to WireGuard by providing WG keys and updating the env vars.
- Update mount paths and timezone to match your host. Set strong secrets (e.g., `JWT_SECRET` for `jellystat`).
