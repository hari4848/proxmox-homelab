# ARR Stack

Complete media management suite that automates the process of discovering, downloading, organizing, and maintaining your movie and TV show collections. This stack integrates multiple services to create a seamless media consumption experience with automatic subtitle management and user request capabilities.

## Services Overview

### Radarr (Port 7878)
**Purpose:** Movie collection management and automation
- Automatically discovers new movie releases
- Downloads movies when they become available in desired quality
- Renames and organizes movie files according to your naming scheme
- Manages movie metadata and artwork
- Integrates with download clients like qBittorrent

**GitHub:** https://github.com/Radarr/Radarr

### Sonarr (Port 8989)
**Purpose:** TV show collection management and automation
- Monitors your favorite TV shows for new episodes
- Downloads episodes automatically when they air
- Handles season packs and individual episodes
- Manages episode naming and organization
- Tracks watched/unwatched status

**GitHub:** https://github.com/Sonarr/Sonarr

### Bazarr (Port 6767)
**Purpose:** Automatic subtitle management
- Downloads subtitles for movies and TV shows in multiple languages
- Integrates directly with Radarr and Sonarr
- Supports various subtitle providers (OpenSubtitles, Subsource, etc.)
- Automatic subtitle synchronization and quality selection
- Customizable subtitle preferences per show/movie

**GitHub:** https://github.com/morpheus65535/bazarr

### qBittorrent (Port 8080)
**Purpose:** Torrent download client
- Feature-rich torrent client with web interface
- Supports sequential downloading for media streaming
- Built-in search functionality
- RSS feed support for automatic torrent discovery
- Advanced bandwidth management and scheduling
- Supports magnet links and torrent files

**GitHub:** https://github.com/qbittorrent/qBittorrent

### Jellyseerr (Port 5055)
**Purpose:** Media request and discovery system
- User-friendly interface for requesting movies and TV shows
- Integrates with Jellyfin/Plex for media library access
- Approval workflow for admin-controlled requests
- Notification system for request status updates
- Rich metadata and discovery features
- Mobile-responsive design

**GitHub:** https://github.com/Fallenbagel/jellyseerr

## Prerequisites

- Docker Engine and Docker Compose v2+
- Host directories for configuration persistence
- `/Main` volume mounted with sufficient storage for media
- Proper PUID/PGID set for file permissions (default: 1000:1000)
- Network connectivity for subtitle providers and torrent trackers

## Initial Setup

### 1. Directory Structure
Create the required configuration directories:
```bash
mkdir -p radarr/config sonarr/config bazarr/config qbittorrent/config jellyseerr/config
```

### 2. Start the Stack
```bash
docker compose up -d
```

### 3. Service Configuration

**Radarr Setup:**
1. Access `http://<host>:7878`
2. Complete initial wizard setup
3. Add root path: `/Main/media/movies`
4. Configure quality profiles (HD-1080p, 4K, etc.)
5. Add download client: qBittorrent at `qbittorrent:8080`
6. Set up indexers for movie discovery

**Sonarr Setup:**
1. Access `http://<host>:8989`
2. Complete initial wizard setup
3. Add root path: `/Main/media/tv`
4. Configure quality profiles and language preferences
5. Add download client: qBittorrent at `qbittorrent:8080`
6. Set up TV show indexers

**Bazarr Setup:**
1. Access `http://<host>:6767`
2. Configure Radarr and Sonarr connections
3. Add subtitle providers (OpenSubtitles account recommended)
4. Set preferred languages and subtitle quality
5. Configure automatic download rules

**qBittorrent Setup:**
1. Access `http://<host>:8080`
2. Set default download location to `/Main/downloads`
3. Configure speed limits and connection settings
4. Enable WebUI authentication
5. Set up RSS feeds for automatic torrent discovery

**Jellyseerr Setup:**
1. Access `http://<host>:5055`
2. Connect to your Jellyfin instance
3. Configure user authentication (local or OAuth)
4. Set up notification preferences
5. Configure request approval workflow
6. Link Radarr/Sonarr for automatic request fulfillment

## Usage Workflow

1. **Discovery:** Users browse and request content through Jellyseerr
2. **Approval:** Admins approve requests in Jellyseerr
3. **Processing:** Radarr/Sonarr automatically adds approved items to download queue
4. **Downloading:** qBittorrent handles the actual torrent downloads
5. **Organization:** Radarr/Sonarr rename and move files to proper directories
6. **Subtitles:** Bazarr automatically downloads matching subtitles
7. **Availability:** Content appears in Jellyfin/Plex for streaming

## Integration Benefits

- **Automation:** Minimal manual intervention required after initial setup
- **Quality Control:** Consistent quality profiles across all content
- **Organization:** Standardized naming and directory structure
- **Accessibility:** Multi-language subtitle support
- **User Experience:** Simple request interface for non-technical users
- **Scalability:** Handles large media libraries efficiently

## Troubleshooting

- Ensure all containers can access `/Main` volume
- Check PUID/PGID permissions match host user
- Verify network connectivity between services
- Monitor qBittorrent download speeds and tracker status
- Regularly update indexers and subtitle providers

## Security Notes

- Configure reverse proxy for external access
- Use VPN or proxy for torrent privacy
- Enable authentication on all web interfaces
- Regularly update containers for security patches
- Monitor disk space usage on `/Main` volume