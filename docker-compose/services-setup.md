# 🐳 Self-Hosted Docker Services – Enhanced Setup Guide

This guide covers deploying popular self-hosted services using Docker Compose. Each service runs in its own directory, with persistent storage for data and configuration. The structure makes management modular, organized, and scalable.

---

## 📋 Prerequisites

- **Docker & Docker Compose:** Installed and running.
- **Linux Host:** (Tested on Ubuntu LXC in Proxmox; other Linux distros work too.)
- **Networking:** Required ports open locally or via reverse proxy.
- **Directory Structure:** Folders for configs and media as shown below.
- **(Optional) Remote Access:** Set up Tailscale, VPN, or reverse proxy for secure remote connections.

---

## 🗂 Recommended Directory Structure

```
docker-compose/
  ├── jellyfin/
  │   ├── config/
  │   └── media/
  ├── rustdesk/
  │   ├── hbbs/
  │   └── hbbr/
  ├── code-server/
  │   └── config/
  ├── gotify/
  │   └── config/
  ├── servarr/
  │   ├── sonarr/
  │   ├── radarr/
  │   ├── bazarr/
  │   ├── jellyseerr/
  │   └── qbittorrent/
  ├── nextcloud/
  │   ├── config/
  │   └── data/
  ├── homarr/
  │   └── config/
```

**In each service folder:**
- Place a `docker-compose.yml` file.
- Use named volumes or bind mounts for persistent data.

---

## 🚀 Setup Instructions

### 1. Prepare Each Service Directory

1. Create a folder for each service.
2. Add the relevant `docker-compose.yml`.
3. Ensure subfolders for config, data, and media exist.

### 2. Launch Containers

From the directory containing your `docker-compose.yml`:

```bash
docker-compose up -d
```

_This starts all defined containers in detached mode._

### 3. Verify Containers

Check running containers:

```bash
docker ps
```

_Confirm that your services are up and healthy._

### 4. Access Web Interfaces

1. Find your host’s local IP address (e.g., using `ip a` or `hostname -I`).
2. Check the port mapping for each service in its compose file.
3. Open your browser and visit:  
   `http://<local-ip>:<service-port>`

_For example: `http://192.168.1.50:8096` for Jellyfin._

---

## 🛠️ Tips & Best Practices

- **Configuration:** Review and customize each compose file for your needs.
- **Backups:** Regularly back up config and data folders.
- **Updates:** Use `docker-compose pull && docker-compose up -d` to update images.
- **Security:** Only expose ports you need; use firewalls and reverse proxies.
- **Documentation:** Refer to each service’s official GitHub/docs for advanced options.

---

## 📚 Further Reading

- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Proxmox Containers](https://pve.proxmox.com/wiki/Linux_Container)
- Individual service GitHub pages (see each service’s README for details)

---

Let me know if you want to add more details for specific services, integration tips, or troubleshooting steps!