# 🖥️ Proxmox Homelab Setup

Welcome to my personal **Proxmox-based homelab** documentation! This setup is designed for flexibility, learning, and easy management of self-hosted services. Below you'll find an overview of the architecture, key components, and storage configuration.

---

## 🏗️ Architecture Overview

**Proxmox OS** is installed on an SSD for fast and reliable performance. The homelab consists of:

- **Two Ubuntu Linux Containers (LXC):**
  - **Servarr LXC:** Runs Docker Compose-managed containers for all major *Arr* services:
    - Sonarr, Radarr, Bazarr, qBittorrent, Jellyseerr, Prowlarr
  - **Apps LXC:** Hosts other Dockerized apps, including:
    - Jellyfin, Nextcloud, Audiobookshelf, Homarr, and more
  - Using separate LXCs for *Arr* and other apps means you can easily shut down the *Arr* stack without impacting other services.

- **Kali VM:**  
  A dedicated virtual machine running Kali Linux for basic cybersecurity learning and testing.

---

## 📦 Storage Setup

- **Main Storage:**  
  A 1TB HDD formatted with **ext4** is used for bulk storage.  
  - The HDD is mounted directly (bind mount) into the Servarr LXC for media management.
  - Files are also copied/shared with the Apps LXC, allowing Jellyfin and other apps access to the same media library.
   - **SMB sharing** is also set up in the Apps LXC, but for details see the [samba share](../Services/Samba/) directory.

> **Why ext4 & direct mounts?**  
> NFS is avoided due to performance concerns. Direct ext4 bind mounts ensure fast and reliable access for Docker containers inside the LXCs.

---

## ⚡ Management

- **Docker Compose** is used for managing all containerized services in both LXCs.
- For detailed Docker setups and compose files, refer to the [docker](../Services/) directory.

---

## 🖼️ Screenshots

![Proxmox Dashboard](../screenshots/proxmox dashboard.jpg)

---
