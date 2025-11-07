# Network Configuration Documentation

## Network Overview

This document details the complete network configuration, including IP addressing, routing, and connectivity between all components in the homelab.

## Network Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                        INTERNET                              │
└────────────────┬────────────────────────────────────────────┘
                 │
        ┌────────┴────────┐
        │  Public Domain  │
        │  DNS Records    │
        │  *.domain.com   │
        └────────┬────────┘
                 │
                 ▼
┌────────────────────────────────────────────────────────────┐
│           Azure VM (India Region)                          │
│  Public IP: [Azure Assigned]                               │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Nginx Proxy Manager (Docker)                        │  │
│  │  - Port 80/443 exposed publicly                      │  │
│  │  - Wildcard SSL certificates                         │  │
│  │  - Subdomain routing                                 │  │
│  └──────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Tailscale Client                                    │  │
│  │  - Connected to mesh network                         │  │
│  │  - Accepts subnet routes: 10.42.42.0/24              │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
                 │
                 │ Tailscale VPN Tunnel
                 │ (Encrypted WireGuard)
                 │
                 ▼
┌────────────────────────────────────────────────────────────┐
│           HOME NETWORK (10.42.42.0/24)                     │
│           Behind CGNAT (Jio Fiber)                         │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐ │
│  │  Proxmox Host (10.42.42.1)                            │ │
│  │  Bridge: vmbr0                                        │ │
│  │                                                        │ │
│  │  ┌─────────────────────────────────────────────────┐  │ │
│  │  │  LXC 1: arr-stack                               │  │ │
│  │  │  IP: 10.42.42.10 (static)                       │  │ │
│  │  │  Gateway: 10.42.42.1                            │  │ │
│  │  │                                                  │  │ │
│  │  │  Docker Services:                               │  │ │
│  │  │  - Sonarr (8989)                                │  │ │
│  │  │  - Radarr (7878)                                │  │ │
│  │  │  - Prowlarr (9696)                              │  │ │
│  │  │  - qBittorrent (8080, 6881)                     │  │ │
│  │  └─────────────────────────────────────────────────┘  │ │
│  │                                                        │ │
│  │  ┌─────────────────────────────────────────────────┐  │ │
│  │  │  LXC 2: main-apps                               │  │ │
│  │  │  IP: 10.42.42.20 (static)                       │  │ │
│  │  │  Gateway: 10.42.42.1                            │  │ │
│  │  │                                                  │  │ │
│  │  │  Docker Services:                               │  │ │
│  │  │  - Jellyfin (8096, 8920)                        │  │ │
│  │  │  - Audiobookshelf (13378)                       │  │ │
│  │  │  - Home Assistant (8123)                        │  │ │
│  │  │  - n8n (5678)                                   │  │ │
│  │  │                                                  │  │ │
│  │  │  System Services:                               │  │ │
│  │  │  - Tailscale (subnet router)                    │  │ │
│  │  │  - Samba (445, 139)                             │  │ │
│  │  └─────────────────────────────────────────────────┘  │ │
│  │                                                        │ │
│  │  Storage: 1TB HDD mounted to both LXCs                │ │
│  └───────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

## IP Address Allocation

### Home Network (10.42.42.0/24)

| Device/Service | IP Address | Type | Purpose |
|----------------|------------|------|---------|
| Proxmox Host (vmbr0) | 10.42.42.1 | Static | Hypervisor management & gateway |
| Reserved | 10.42.42.2-9 | Static | Future infrastructure services |
| arr-stack LXC | 10.42.42.10 | Static | Media acquisition services |
| main-apps LXC | 10.42.42.20 | Static | Media servers and automation |
| Reserved | 10.42.42.21-99 | Static | Future LXC/VM allocation |
| DHCP Pool | 10.42.42.100-200 | Dynamic | Other home devices |
| Reserved | 10.42.42.201-254 | Static | Future expansion |

### Tailscale Network (100.64.0.0/10 CGNAT Range)

| Device | Tailscale IP | Role | Subnet Routes |
|--------|--------------|------|---------------|
| Azure VM | 100.x.x.x | Exit node, Proxy | Accepts: 10.42.42.0/24 |
| main-apps LXC | 100.x.x.x | Subnet router | Advertises: 10.42.42.0/24 |
| Admin devices | 100.x.x.x | Management | None |

*Note: Tailscale IPs are dynamically assigned by Tailscale coordination server*

## Port Mappings

### LXC 1: arr-stack (10.42.42.10)

| Service | Container Port | Protocol | Access Level |
|---------|---------------|----------|--------------|
| Sonarr Web UI | 8989 | HTTP | Internal + Tailscale + Public (via NPM) |
| Radarr Web UI | 7878 | HTTP | Internal + Tailscale + Public (via NPM) |
| Prowlarr Web UI | 9696 | HTTP | Internal + Tailscale + Public (via NPM) |
| qBittorrent Web UI | 8080 | HTTP | Internal + Tailscale + Public (via NPM) |
| qBittorrent Incoming | 6881 | TCP/UDP | Internal (NAT forwarded if possible) |

### LXC 2: main-apps (10.42.42.20)

| Service | Container Port | Protocol | Access Level |
|---------|---------------|----------|--------------|
| Jellyfin Web UI | 8096 | HTTP | Internal + Tailscale + Public (via NPM) |
| Jellyfin HTTPS | 8920 | HTTPS | Internal + Tailscale |
| Jellyfin Service Discovery | 7359 | UDP | Internal only |
| Jellyfin DLNA | 1900 | UDP | Internal only |
| Audiobookshelf | 13378 | HTTP | Internal + Tailscale + Public (via NPM) |
| Home Assistant | 8123 | HTTP/HTTPS | Internal + Tailscale + Public (via NPM) |
| n8n | 5678 | HTTP | Internal + Tailscale (NOT public) |
| Samba/SMB | 445 | SMB | Internal only |
| Samba/NetBIOS | 139 | NetBIOS | Internal only |
| Samba/NetBIOS Name | 137 | UDP | Internal only |
| Samba/NetBIOS Datagram | 138 | UDP | Internal only |

### Azure VM

| Service | Port | Protocol | Purpose | Access |
|---------|------|----------|---------|--------|
| NPM Admin UI | 81 | HTTP | Management interface | Tailscale only |
| HTTP | 80 | HTTP | Redirect to HTTPS | Public |
| HTTPS | 443 | HTTPS | Public service access | Public |
| SSH | 22 | SSH | Server management | Tailscale only |

## DNS Configuration

### Public Domain Configuration

Your domain from GitHub Student Pack should be configured with these records:

| Record Type | Name | Value | TTL | Purpose |
|-------------|------|-------|-----|---------|
| A | @ | [Azure VM Public IP] | 300 | Root domain |
| A | * | [Azure VM Public IP] | 300 | Wildcard for all subdomains |
| CNAME | www | @ | 300 | WWW redirect (optional) |

### Subdomain to Service Mapping (NPM Configuration)

Configure these proxy hosts in Nginx Proxy Manager:

| Subdomain | Target Service | Forward Hostname/IP | Forward Port | SSL |
|-----------|---------------|---------------------|--------------|-----|
| jellyfin.domain.com | Jellyfin | 10.42.42.20 | 8096 | Yes |
| audiobooks.domain.com | Audiobookshelf | 10.42.42.20 | 13378 | Yes |
| home.domain.com | Home Assistant | 10.42.42.20 | 8123 | Yes |
| sonarr.domain.com | Sonarr | 10.42.42.10 | 8989 | Yes |
| radarr.domain.com | Radarr | 10.42.42.10 | 7878 | Yes |
| prowlarr.domain.com | Prowlarr | 10.42.42.10 | 9696 | Yes |
| downloads.domain.com | qBittorrent | 10.42.42.10 | 8080 | Yes |
| n8n.domain.com | n8n | 10.42.42.20 | 5678 | Yes |
| proxy.domain.com | NPM Admin | 127.0.0.1 | 81 | Yes |

**Important NPM Settings:**
- Enable "Block Common Exploits"
- Enable "Websockets Support" (for Home Assistant, n8n)
- Request Let's Encrypt SSL certificate with DNS challenge or HTTP challenge
- Enable "Force SSL" and "HTTP/2 Support"

## Network Routing

### Tailscale Subnet Routing Configuration

**On main-apps LXC (10.42.42.20):**
```bash
# Enable IP forwarding
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Advertise home subnet
sudo tailscale up --advertise-routes=10.42.42.0/24 --accept-routes
```

**On Azure VM:**
```bash
# Accept subnet routes from home
sudo tailscale up --accept-routes
```

**In Tailscale Admin Console:**
- Go to Machines → main-apps LXC
- Approve the subnet route: 10.42.42.0/24
- Disable key expiry for server nodes

### Traffic Flow Examples

#### Public Service Access (Jellyfin)
```
User's Browser
    ↓ HTTPS (jellyfin.domain.com:443)
Azure VM Public IP
    ↓ (NPM receives request)
NPM SSL Termination
    ↓ (proxies via Tailscale)
Tailscale Tunnel (encrypted)
    ↓
main-apps LXC (100.x.x.x via Tailscale)
    ↓ HTTP (10.42.42.20:8096)
Jellyfin Container
    ↓ (response flows back)
User receives content
```

#### Remote Administration via Tailscale
```
Admin Device (Tailscale connected)
    ↓ Direct connection via Tailscale IP
main-apps LXC (100.x.x.x)
    OR
    ↓ Via advertised subnet route
10.42.42.20:8096 (direct to service)
```

#### Local Network Access
```
Home Device (10.42.42.x)
    ↓ Direct LAN connection
10.42.42.20:8096
    ↓
Jellyfin Container
    (No internet routing, fastest performance)
```


### Azure VM Security Group (Network Security Group)

**Inbound Security Rules:**
| Priority | Name | Port | Protocol | Source | Action |
|----------|------|------|----------|--------|--------|
| 100 | Allow-HTTPS | 443 | TCP | Internet | Allow |
| 110 | Allow-HTTP | 80 | TCP | Internet | Allow |
| 200 | Allow-SSH-Tailscale | 22 | TCP | Service Tag: Internet | Allow* |
| 300 | Deny-All | * | Any | Any | Deny |

*Note: SSH should ideally be restricted to Tailscale IPs, but since Tailscale uses dynamic IPs, either use Tailscale ACLs or disable password auth and use SSH keys only.

**Outbound Security Rules:**
```
Allow all outbound (default) - needed for:
- Let's Encrypt certificate validation
- Tailscale coordination server
- Package updates
```

## Tailscale Configuration

### Setup and Configuration

**Install Tailscale on main-apps LXC:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-routes=10.42.42.0/24 --accept-routes
```

**Install Tailscale on Azure VM:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --accept-routes
```

### Tailscale Admin Console Settings

1. **Approve Subnet Routes:**
   - Navigate to Machines
   - Find main-apps LXC
   - Approve the advertised route: 10.42.42.0/24

2. **Disable Key Expiry** (for servers):
   - Machines → [Device] → … → Disable key expiry

3. **Enable MagicDNS** (optional but recommended):
   - DNS → Enable MagicDNS
   - Allows using machine names instead of IPs

4. **Configure Access Controls** (ACL):
```json
{
  "tagOwners": {
    "tag:server": ["your-email@example.com"],
    "tag:admin": ["your-email@example.com"]
  },
  "acls": [
    {
      "action": "accept",
      "src": ["tag:admin"],
      "dst": ["*:*"]
    },
    {
      "action": "accept",
      "src": ["tag:server"],
      "dst": ["tag:server:*"]
    }
  ]
}
```

5. **Tag your devices:**
   - Azure VM: `tag:server`
   - main-apps LXC: `tag:server`

## Network Performance

### Expected Latency
- **Local network access**: <1ms (LAN speed)
- **Tailscale (home to Azure)**: 20-50ms (depends on ISP routing to Azure India region)
- **Public access via NPM**: 25-60ms (adds overhead of reverse proxy)

### Bandwidth Considerations
- **Jio Fiber Upload**: Primary bottleneck for remote streaming (typically 10-50 Mbps)
- **Azure VM Network**: Up to 1Gbps capable
- **Internal home network**: Gigabit (1000 Mbps)

### Optimization Tips
1. **For remote Jellyfin streaming**: Enable transcoding to reduce bandwidth
2. **For large downloads**: Access qBittorrent locally when possible
3. **For backups**: Schedule during off-peak hours
4. **For Home Assistant**: Keep automations local, minimize cloud dependencies

## Network Security Best Practices

### Currently Implemented
✅ No direct port forwarding from ISP router
✅ All public traffic through hardened Azure VM
✅ VPN encryption for admin access (Tailscale)
✅ SSL/TLS for all public services
✅ Isolated Docker networks per service
✅ LXC-level separation of services
✅ Firewall rules at multiple layers

### Recommended Additions
⬜ Implement fail2ban on Azure VM (protect SSH and NPM)
⬜ Add authentication layer (Authelia/Authentik) for services
⬜ Enable Tailscale MFA for admin accounts
⬜ Set up log monitoring and alerting
⬜ Implement rate limiting on NPM
⬜ Regular security updates (automated with unattended-upgrades)
⬜ Enable 2FA on critical services (Jellyfin, Home Assistant)
⬜ Implement backup strategy with encryption

## Common Network Issues and Solutions

### Cannot Access Services via Subdomain

**Symptoms**: Subdomain returns error or times out

**Diagnostic Steps**:
```bash
# 1. Check DNS resolution
nslookup jellyfin.yourdomain.com

# 2. Check if Azure VM is reachable
ping [azure-vm-public-ip]

# 3. Check Tailscale connectivity
tailscale status
tailscale ping main-apps

# 4. Check NPM logs on Azure VM
docker logs nginx-proxy-manager
```

**Common Solutions**:
- DNS not propagated: Wait 5-60 minutes
- Azure VM stopped: Start from Azure portal
- Tailscale disconnected: `sudo systemctl restart tailscaled`
- NPM misconfigured: Check proxy host settings

### Slow Remote Streaming

**Symptoms**: Jellyfin buffers frequently when accessed remotely

**Diagnostic Steps**:
```bash
# 1. Check upload speed from home
speedtest-cli

# 2. Check Tailscale connection quality
tailscale ping azure-vm

# 3. Check CPU usage during transcoding
docker stats
```

**Solutions**:
- Enable Jellyfin transcoding to lower bitrate
- Use Jellyfin mobile apps with download feature
- Access during off-peak ISP hours
- Consider upgrading Jio Fiber plan for better upload

### Local Network Access Not Working

**Symptoms**: Cannot access services from home devices

**Diagnostic Steps**:
```bash
# 1. Check if LXC is running
pct status 100  # arr-stack
pct status 101  # main-apps

# 2. Check network connectivity
ping 10.42.42.10
ping 10.42.42.20

# 3. Check if service is listening
nmap -p 8096 10.42.42.20

# 4. Check Docker containers
docker ps
```

**Solutions**:
- LXC stopped: `pct start [id]`
- Service crashed: `docker-compose up -d`
- Firewall blocking: Check ufw rules
- Network misconfiguration: Verify static IP settings

## Network Monitoring Tools

### Recommended Monitoring Stack

1. **Uptime Kuma** - Service availability monitoring
   - Deploy in main-apps LXC
   - Monitor all services via HTTP checks
   - Set up notifications (email, Telegram, Discord)

2. **Netdata** - Real-time system monitoring
   - Deploy on Proxmox host and each LXC
   - Monitor CPU, RAM, network, disk I/O
   - Access via Tailscale

3. **Grafana + Prometheus** - Metrics and alerting
   - Collect metrics from all services
   - Create dashboards for network performance
   - Set up alerts for anomalies

4. **Tailscale Admin Console** - Network topology
   - Monitor connection status
   - Check latency between nodes
   - Review subnet routing status

### Key Metrics to Monitor

- **Tailscale**: Connection status, latency, packet loss
- **Azure VM**: CPU usage, network bandwidth, disk usage
- **NPM**: Request rate, response time, error rate
- **Services**: Uptime, response time, resource usage
- **Network**: Bandwidth utilization, packet loss

## Network Maintenance Tasks

### Daily
- Check Tailscale connection status
- Monitor service uptime

### Weekly
- Review NPM access logs for unusual activity
- Check Azure VM resource usage
- Verify backup completion

### Monthly
- Update all systems (`apt update && apt upgrade`)
- Review and renew SSL certificates (auto-renewal check)
- Check disk space usage
- Review firewall logs

### Quarterly
- Review and update Tailscale ACLs
- Audit user access and permissions
- Test disaster recovery procedures
- Update documentation
