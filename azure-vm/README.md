# Azure VM Setup for Homelab CGNAT Bypass

This guide details the setup of a low-cost Azure VM to act as a public-facing reverse proxy, enabling access to self-hosted services running on a homelab behind a CGNAT firewall.

## Overview

The primary challenge of self-hosting on an ISP that uses CGNAT is the inability to accept incoming connections from the internet. This setup solves that by using a cloud VM with a public IP as a middleman.

**How it works:**
1.  A Tailscale VPN tunnel securely connects the homelab server and the Azure VM.
2.  The Azure VM runs Nginx Proxy Manager (NPM) in a Docker container.
3.  NPM listens for public traffic and proxies it through the encrypted Tailscale tunnel to the appropriate service in the homelab, using its private Tailscale IP address.

## VM Specifications
- **Provider**: Microsoft Azure
- **Instance Type**: Standard B2ats v2
- **vCPUs**: 2
- **RAM**: 1GB
- **OS**: Ubuntu Server
- **Purpose**: Public endpoint and reverse proxy.

## Setup Steps

### 1. Provision the Azure VM
1.  Create a new Ubuntu Server VM in the Azure portal.
2.  During setup, ensure ports `80` (HTTP) and `443` (HTTPS) are open for inbound traffic in the Network Security Group settings.
3.  Connect to the VM via SSH.

### 2. Install Docker
Docker is required to run Nginx Proxy Manager.

```bash
# Update package lists
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add your user to the docker group to run docker commands without sudo
sudo usermod -aG docker $USER
```
After running the `usermod` command, you will need to log out and log back in for the changes to take effect.

### 3. Install Tailscale
Tailscale creates the secure tunnel between the Azure VM and your homelab.

```bash
# Install Tailscale using the official script
curl -fsSL https://tailscale.com/install.sh | sh

# Authenticate and connect your VM to your Tailscale network
sudo tailscale up --accept-routes
```
Follow the on-screen instructions to authenticate the new device in your Tailscale admin console.

### 4. Install Nginx Proxy Manager
NPM provides an easy-to-use web interface for managing reverse proxy configurations and SSL certificates.

For the most up-to-date installation instructions, please refer to the [official Nginx Proxy Manager documentation](https://nginxproxymanager.com/setup/).

## Configuration

Once NPM is running, you can configure it to expose your homelab services.

1.  **Access the NPM Admin UI**: Open a web browser and navigate to `http://<your-vm-public-ip>:81`.
2.  **Log in** with the default credentials and change your password.
3.  **Create a Proxy Host**:
    -   Go to `Hosts` > `Proxy Hosts` and click `Add Proxy Host`.
    -   **Domain Name**: Enter the public domain for your service (e.g., `jellyfin.yourdomain.com`).
    -   **Scheme**: `http`
    -   **Forward Hostname / IP**: Enter the Tailscale IP address of your homelab service (e.g., `100.X.X.X`).
    -   **Forward Port**: Enter the port the service is running on (e.g., `8096` for Jellyfin).
    -   **Enable `Block Common Exploits`**.
4.  **Enable SSL**:
    -   Click on the `SSL` tab.
    -   Under `SSL Certificate`, select `Request a new SSL Certificate`.
    -   Enable `Force SSL` and `HTTP/2 Support`.
    -   Agree to the Let's Encrypt ToS and click `Save`.

NPM will now handle SSL termination and forward traffic securely to your homelab service over the Tailscale tunnel.
