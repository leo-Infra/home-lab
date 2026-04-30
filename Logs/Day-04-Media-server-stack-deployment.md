# Day 04 - Media Server Stack Deployment

## Objective
Deploy a complete automated media server stack using Docker Compose, including VPN routing, download management, and media streaming.

---

## What I did

### Stack deployment with Docker Compose
- Deployed a full arr-stack using a pre-configured `docker-compose.yml`
- Understood the difference between `docker run` and `docker-compose`
- Learned how to manage multi-container applications as a single unit

**Services deployed:**
- `Gluetun` — VPN container (Mullvad WireGuard)
- `qBittorrent` — torrent client routed through VPN
- `Prowlarr` — indexer manager
- `FlareSolverr` — Cloudflare bypass proxy
- `Radarr` — movie management
- `Sonarr` — TV show management
- `Jellyfin` — media server
- `Jellyseerr` — media request interface
- `Bazarr`, `Lidarr`, `Readarr` — subtitles, music, books

---

### VPN Configuration (Gluetun + Mullvad)

**Problem encountered:**
- Gluetun container stayed `unhealthy` and blocked all dependent services (qBittorrent, Prowlarr, etc.)

**Root causes identified and fixed:**
- `WIREGUARD_ADDRESSES` was set to placeholder value `10.14.0.2/16`
- Downloaded real WireGuard config from Mullvad dashboard to get correct IP (e.g. `10.74.255.255/32`)
- `DOT_PROVIDERS=cloudflare` was causing DNS resolution failures inside the tunnel
- Fixed by replacing with `DOT=off` to use Mullvad's native DNS

**UFW firewall was blocking WireGuard:**
```bash
sudo ufw allow 51820/udp
```

**Result:** Gluetun became `healthy`, all dependent containers started successfully

---

### Docker networking understanding
- Learned that containers sharing `network_mode: service:gluetun` communicate via `localhost`
- Containers in separate networks (`arrnetwork`) must communicate via Docker IP or host IP
- Used Docker IPs from readme (`172.39.0.x`) for inter-container communication
- Example: Prowlarr Server in Radarr = `http://172.39.0.2:9696`

---

## What I learned
- How VPN tunnel DNS resolution works inside containers
- Difference between DOT (DNS over TLS) and standard DNS
- How Docker network modes affect container communication
- How to read and interpret container health logs

---

---

# Day 06 - Stack Configuration and Media Automation

## Objective
Configure all deployed services to work together as a fully automated media pipeline.

---

## What I did

### Service interconnection
Connected all services together:
- Prowlarr → Radarr (Full Sync)
- Prowlarr → Sonarr (Full Sync)
- qBittorrent → Radarr
- qBittorrent → Sonarr
- FlareSolverr → Prowlarr (tag: `flaresolverr`)
- Jellyfin → Jellyseerr
- Sonarr → Jellyseerr

**Key lesson:** When connecting services from different Docker networks, use host IP (`192.168.1.76`) or Docker network IPs (`172.39.0.x`), not container names or `localhost`

---

### Indexer configuration in Prowlarr
Added multiple public indexers:
- `1337x` (with flaresolverr tag for Cloudflare bypass)
- `NorTorrent`, `Torrent9`, `World-torrent` (French public trackers)
- `YTS` (movies)
- `EZTV` (TV shows, with flaresolverr tag)

**Understood why some indexers get disabled:**
- Cloudflare protection triggers automatic disable
- FlareSolverr tag bypasses this protection

---

### UFW port management
Opened necessary ports for service access:
```bash
sudo ufw allow 51820/udp   # WireGuard
sudo ufw allow 7878/tcp    # Radarr
sudo ufw allow 9696/tcp    # Prowlarr
sudo ufw allow 8080/tcp    # qBittorrent
sudo ufw allow 8096/tcp    # Jellyfin
sudo ufw allow 8989/tcp    # Sonarr
```

---

### Jellyfin library configuration
- Fixed library path: `/data/media/movies`
- Understood that Jellyfin reads from `/data/media/` not from torrent download folder
- Radarr automatically moves completed downloads from `/data/torrents/` to `/data/media/`

---

### Quality and language profiles in Radarr
- Created custom format `français` with Language condition (score: `+100`)
- Created custom format `anglais` as fallback (score: `+44`)
- Set profile language to `Any` to accept all languages
- Understood that quality profile restrictions (Ultra-HD only) can block French releases available only in 720p/1080p

---

### Media automation test
Successfully tested full pipeline:
1. Requested movie via Jellyseerr
2. Radarr picked up request and searched indexers
3. qBittorrent downloaded torrent through Mullvad VPN
4. Radarr imported file to `/data/media/movies/`
5. Jellyfin detected new file automatically

---

## Issues encountered

**Jellyseerr volume not configured:**
- Warning: `/app/config` volume mount not configured properly
- Data will be lost on container restart
- To be fixed by updating `docker-compose.yml` volumes

**Transcoding performance:**
- Video playback causes freezes every 3-4 seconds
- Root cause: Hardware acceleration set to `None`
- Jellyfin transcodes everything in software (CPU)
- To be fixed by enabling VAAPI (Intel GPU acceleration)

---

## What I learned
- How an automated media pipeline works end to end
- Importance of Docker network architecture for service communication
- How indexers, download clients and media managers interact
- Language and quality preference management in Radarr/Sonarr
- Difference between direct play and transcoding in Jellyfin

---

## Next steps
- Fix Jellyseerr persistent volume
- Enable hardware acceleration (VAAPI) for Jellyfin
- Install Homepage dashboard for centralized access
- Add new HDD and migrate data
- Explore private French trackers for better French content
- Test Jellyfin access from TV (VIDAA OS browser or APK sideload)
