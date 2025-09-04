# 🐳 Self-Hosted Docker Services – Setup Guide

This guide explains how to deploy various self-hosted services using Docker Compose. All containers are organized with separate configuration folders and shared storage mounts for persistence and modularity.

## 📋 Prerequisites

- Docker & Docker Compose installed
- Linux-based host (tested on Ubuntu LXC in Proxmox)
- Required ports open locally or reverse-proxied
- Proper folder structure for configs and media
- Optional: Tailscale, VPN, or reverse proxy for remote access

---

## 🗂 Directory Structure

- docker-compose/ 
- │ 
- ├── jellyfin/ 
- │ └── config/, media/ 
- ├── rustdesk/   
- │ └── hbbs/, hbbr/  
- ├── code-server/ 
- │ └── config/ 
- ├── gotify/ 
- │ └── config/ 
- ├── servarr/ 
- │ ├── sonarr/, radarr/, bazarr/, jellyseerr/, qbittorrent/ 
- ├── nextcloud/ 
- │ └── config/, data/ 
- ├── homarr/ 
- │ └── config/ 


Each folder contains:
- `docker-compose.yml`
- Named volumes or bind mounts

---

## 🚀 Setup Instructions

### Starting the containers

Create a directory for each services and put the compose file in it then use this command to run it

```bash
docker-compose up -d

```
then check whether the containers are running using this command

```bash
docker ps
```

### Accessing the web ui

Check the ip address of the local system then check the ports of each services 
now you can access the service by visiting to http://localip:port. the ip of your system and the port of the particular service which can be seen in the compose file.

This is just my setup. For more  details refer the corresponding service github page.







