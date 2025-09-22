# Setting Up a Tailscale Tunnel to Bypass CGNAT

## Why Do You Need This?

If your WiFi is under CGNAT (Carrier-Grade NAT), you cannot directly expose your local devices or services to the public internet. CGNAT masks your device's real IP with a shared one used by many users, making port forwarding and direct remote access impossible.

**Tailscale Tunnel** is a simple solution: it creates a secure, peer-to-peer VPN using WireGuard, letting you access your devices from anywhere—even if they're behind CGNAT. To expose a service to the internet from a CGNAT-ed network, you can:

- Set up a Tailscale node (like a Raspberry Pi or a cloud VM) on a network that is **not** under CGNAT and use it as a relay (subnet router and/or exit node).
- Route traffic through this node, effectively bypassing CGNAT.

---

## Alternatives to Bypass CGNAT

- **Get a Static IP:** Contact your ISP for a public/static IP (may incur extra cost).
- **Cloudflare Tunnel:** Free for small use, but limited (see [Cloudflare Tunnel Docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)).

---

## Step-by-Step Guide

### 1. Install Tailscale on Both Local and Cloud (or Remote) Devices

**On Raspberry Pi / Linux:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
- Log in using the provided URL.

**On Cloud VM (Ubuntu/Debian):**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-exit-node --advertise-routes=192.168.1.0/24
```
- `--advertise-exit-node`: Lets you route all traffic through this node.
- `--advertise-routes=<subnet>`: Shares local network services via Tailscale.

> **Note:** Replace `192.168.1.0/24` with your local subnet as needed.

---

### 2. Approve Routes and Exit Nodes in Tailscale Admin Panel

- Go to [https://login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines)
- Find your cloud node, approve subnet routes and exit node status.

---

### 3. Use Tailscale to Access Services

- From your local machine, connect to the Tailscale IP of your remote device.
- To route all internet traffic via the remote node (as a VPN):
  ```bash
  sudo tailscale up --exit-node=<cloud-node-tailscale-ip>
  ```
- For subnet routing, access devices/services on the remote network.

---

### 4. Exposing Local Services via Tailscale

- Run your service (e.g., a web server) on the device.
- Use the Tailscale IP (shown by `tailscale ip -4`) to access it from other Tailscale nodes.

---

## Diagram / Screenshot

<!-- Leave space here for a diagram or screenshot. -->

![Tailscale Setup Diagram](path/to/screenshot-or-diagram.png)

---

## Link to Domain Setup Instructions

See [DOMAIN_SETUP.md](./DOMAIN_SETUP.md) for configuring a custom domain with your service.

---

## Other Questions

- **Do you need to expose multiple services via Tailscale?**
- **Are you looking for a way to automate this setup or manage multiple exit nodes?**
- **Do you need integration with DNS or HTTPS (e.g., Let's Encrypt)?**
- **Should I include a section on using Tailscale ACLs for security?**

If you have any specific requirements or questions, let me know!

---
