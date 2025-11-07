# Homelab Troubleshooting Steps

## Quick Diagnostic Workflow

When something goes wrong, follow this systematic approach:

1. **Identify the scope**: Is it one service, all services, local only, or remote only?
2. **Check the basics**: Is it powered on? Is it connected to network?
3. **Verify connectivity**: Can you ping/reach the service?
4. **Check logs**: What do the service logs say?
5. **Verify dependencies**: Are required services running?
6. **Recent changes**: Did you change anything recently?

## Service Access Issues

### Cannot Access ANY Services Remotely

**Symptoms**: All public subdomains return errors, timeout, or show connection refused

**Priority**: CRITICAL - Complete remote access down

**Diagnostic Steps**:
```bash
# 1. Check if Azure VM is running
# From Azure portal or CLI
az vm get-instance-view --name <vm-name> --resource-group <rg-name>

# 2. Check Tailscale connectivity from any Tailscale device
tailscale status
tailscale ping azure-vm
tailscale ping main-apps

# 3. SSH to Azure VM (if Tailscale works)
ssh <user>@azure-vm

# 4. Check NPM container status
docker ps | grep nginx-proxy-manager
docker logs nginx-proxy-manager

# 5. Check if ports are listening
sudo netstat -tulpn | grep -E ':(80|443)'
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| Azure VM stopped/crashed | Portal shows "Stopped" | Start VM from Azure portal |
| Azure VM out of resources | High CPU/RAM in Azure metrics | Restart VM, check resource usage |
| Tailscale disconnected | `tailscale status` shows offline | `sudo systemctl restart tailscaled` on both ends |
| NPM container crashed | `docker ps` doesn't show NPM | `cd /path/to/npm && docker-compose up -d` |
| NPM misconfigured | NPM logs show errors | Review and fix proxy host configs |
| DNS issues | `nslookup domain.com` fails or wrong IP | Update DNS records to correct Azure VM IP |
| SSL certificates expired | Browser shows SSL error | Renew certificates in NPM UI |
| Azure VM IP changed | Old IP in DNS records | Update DNS A records with new IP |

**Quick Fixes**:
```bash
# Restart NPM container
docker restart nginx-proxy-manager

# Restart Tailscale
sudo systemctl restart tailscaled

# Restart Azure VM (from Azure portal or CLI)
az vm restart --name <vm-name> --resource-group <rg-name>
```

### Cannot Access Specific Service Remotely

**Symptoms**: One subdomain fails (e.g., jellyfin.domain.com) but others work

**Priority**: MEDIUM - Single service affected

**Diagnostic Steps**:
```bash
# 1. Check if the service container is running
ssh main-apps  # or arr-stack
docker ps | grep jellyfin  # or specific service
docker logs jellyfin

# 2. Test direct access via Tailscale (bypass NPM)
curl http://10.42.42.20:8096  # Example for Jellyfin

# 3. Check NPM proxy host configuration
# Access NPM UI via proxy.domain.com
# Verify proxy host for the failing service

# 4. Check service health from inside LXC
curl localhost:8096  # From within the LXC
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| Docker container stopped | `docker ps` doesn't show it | `docker start <container>` or `docker-compose up -d` |
| Service crashed | Container restarts repeatedly | Check logs: `docker logs <container>`, fix config/resources |
| Wrong port in NPM | NPM logs show connection refused | Update NPM proxy host with correct port |
| Service bound to localhost only | Works from LXC, not remotely | Update Docker config to bind to 0.0.0.0 |
| Firewall blocking | Direct IP works via Tailscale but not via NPM | Check `ufw status` and add rule |
| Service requires WebSocket | Connection drops or fails | Enable WebSocket support in NPM proxy host |
| Resource exhaustion | Container OOMKilled | Increase LXC RAM or reduce service memory limit |

**Quick Fixes**:
```bash
# Restart specific service
docker restart <service-name>

# Restart all services in an LXC
docker-compose down && docker-compose up -d

# Check why container stopped
docker inspect <container> | grep -A 10 State
```

### Cannot Access Services on Local Network

**Symptoms**: Services don't work from devices on home network (10.42.42.0/24)

**Priority**: MEDIUM - Local access affected

**Diagnostic Steps**:
```bash
# 1. Check if LXC is running
pct status 100  # arr-stack
pct status 101  # main-apps

# 2. Check network connectivity
ping 10.42.42.10
ping 10.42.42.20

# 3. Check if ports are open
nmap -p 8096 10.42.42.20

# 4. Check from Proxmox host
curl http://10.42.42.20:8096

# 5. Check LXC network configuration
pct enter 101
ip addr show
cat /etc/network/interfaces
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| LXC stopped | `pct status` shows "stopped" | `pct start <lxc-id>` |
| Static IP changed/lost | `ip addr` shows different IP | Reconfigure `/etc/network/interfaces` and restart |
| Docker networking issue | Can ping LXC but not services | `systemctl restart docker` |
| Proxmox firewall blocking | Can't ping LXC from network | Check and adjust Proxmox firewall rules |
| LXC firewall blocking | `ufw status` shows deny rules | `ufw allow from 10.42.42.0/24` |
| vmbr0 bridge issue | No network on any LXC | Check Proxmox network configuration |

**Quick Fixes**:
```bash
# Start LXC
pct start 101

# Restart LXC networking
pct enter 101
systemctl restart networking

# Disable LXC firewall temporarily for testing
ufw disable

# Restart Docker networking
systemctl restart docker
```

## Proxmox Host Issues

### Proxmox Host Unresponsive

**Symptoms**: Cannot access Proxmox web UI, SSH fails, services completely down

**Priority**: CRITICAL - Entire homelab offline

**Diagnostic Steps**:
1. Physical check: Is the server powered on? LEDs lit?
2. Network check: Can you ping 10.42.42.1?
3. Monitor check: Connect monitor and keyboard directly
4. Check router: Is the server showing as connected?

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| Power outage | No lights, no fans | Check power supply, UPS if available |
| Kernel panic | Console shows panic message | Hard reboot, check logs after restart |
| Network cable unplugged | Can't ping, no link lights | Reconnect network cable |
| Overheating/hardware failure | System crashes under load | Check temps, clean dust, check hardware |
| Out of memory | System becomes extremely slow | Force reboot, investigate memory leaks |
| Disk full | Proxmox won't start properly | Boot to recovery, clean up disk space |

**Emergency Actions**:
```bash
# Force reboot from console (if SSH accessible)
reboot -f

# If completely frozen, hard power cycle
# Press and hold power button for 5-10 seconds
# Wait 30 seconds, then power on

# After reboot, check system logs
journalctl -xb -p err
dmesg | grep -i error
```

### LXC Won't Start

**Symptoms**: `pct start <id>` fails or LXC crashes immediately after starting

**Priority**: HIGH - Service unavailable

**Diagnostic Steps**:
```bash
# 1. Check current status
pct status 101

# 2. Try to start with detailed output
pct start 101

# 3. Check container logs
pct logs 101

# 4. Check Proxmox system logs
journalctl -xe | grep "lxc\[101\]"

# 5. Check container configuration
cat /etc/pve/lxc/101.conf

# 6. Check Proxmox storage
pvesm status
df -h
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| Insufficient RAM | Error mentions memory | Stop other LXCs, free up RAM, or add more |
| Storage not mounted | Error about mount points | Check storage status: `pvesm status` |
| Corrupted filesystem | Errors about read/write | Run fsck on LXC storage |
| Configuration error | Syntax error in config | Review and fix `/etc/pve/lxc/<id>.conf` |
| Conflicting IP address | Network startup fails | Change IP in LXC config or other device |
| Missing kernel modules | Module loading errors | `modprobe <module>` on Proxmox host |
| Resource limits exceeded | Cgroup errors | Adjust CPU/RAM limits in LXC config |

**Quick Fixes**:
```bash
# Free up memory by stopping non-critical LXC
pct stop 100  # Stop arr-stack temporarily

# Restore LXC from backup
pct restore 101 /path/to/backup.tar.gz

# Start in recovery mode (if available)
# Edit /etc/pve/lxc/101.conf, comment out startup scripts

# Check and repair filesystem (LXC must be stopped)
pct fsck 101
```

### Cannot Mount Shared 1TB Storage

**Symptoms**: LXCs can't access the 1TB HDD, media files unavailable

**Priority**: HIGH - Media services affected

**Diagnostic Steps**:
```bash
# 1. Check if HDD is recognized
lsblk
fdisk -l

# 2. Check if mounted on Proxmox host
mount | grep sdb  # or your HDD device
df -h

# 3. Check mount point
ls -la /mnt/storage  # or your mount path

# 4. Check LXC mount point configuration
cat /etc/pve/lxc/101.conf | grep mp

# 5. Check for errors
dmesg | grep -i "sdb\|error"
journalctl -xe | grep mount
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| HDD not mounted on host | `mount` doesn't show it | `mount /dev/sdb1 /mnt/storage` |
| Wrong device name | lsblk shows different name | Update mount command with correct device |
| Filesystem errors | dmesg shows I/O errors | Run `fsck /dev/sdb1` (unmount first) |
| Permission issues | ls shows wrong ownership | `chown -R 100000:100000 /mnt/storage` |
| Mount point not in LXC config | LXC can't see it | Add mount point in Proxmox LXC options |
| HDD hardware failure | SMART errors, clicking sounds | Replace HDD, restore from backup |
| USB HDD disconnected | Drive disappears | Reconnect USB cable, check power |

**Quick Fixes**:
```bash
# Mount HDD manually
mkdir -p /mnt/storage
mount /dev/sdb1 /mnt/storage

# Make mount persistent (add to /etc/fstab)
echo '/dev/sdb1 /mnt/storage ext4 defaults 0 2' >> /etc/fstab

# Fix permissions for unprivileged LXCs
chown -R 100000:100000 /mnt/storage
chmod -R 755 /mnt/storage

# Check HDD health
smartctl -a /dev/sdb

# Bind mount to LXC (if not in config)
pct set 101 -mp0 /mnt/storage,mp=/mnt/storage
```

## Docker Issues

### All Docker Containers Stopped

**Symptoms**: `docker ps` shows no running containers in an LXC

**Priority**: HIGH - Multiple services down

**Diagnostic Steps**:
```bash
# 1. Check Docker service
systemctl status docker

# 2. Check if containers exist
docker ps -a

# 3. Check Docker logs
journalctl -u docker -n 100

# 4. Check system resources
free -h
df -h

# 5. Try starting Docker
systemctl start docker
```

**Common Causes & Solutions**:

| Cause | How to Identify | Solution |
|-------|----------------|----------|
| Docker daemon crashed | `systemctl status docker` shows inactive | `systemctl start docker` |
| LXC restarted | Containers show "Exited" status | `docker-compose up -d` in each directory |
| Out of memory | OOMKilled in container status | Free RAM, reduce container limits |
| Disk full | df shows 100% | Clean up: `docker system prune -a` |
| Corrupted Docker state | Docker won't start | `rm -rf /var/lib/docker` and restart (last resort) |
| Network driver issue | Errors about network | `systemctl restart docker` |

**Quick Fixes**:
```bash
# Restart Docker service
systemctl restart docker

# Start all compose projects
cd /opt/jellyfin && docker-compose up -d
cd /opt/audiobookshelf && docker-compose up -d
# Repeat for each service

# Clean up Docker resources
docker system prune -f

# Remove stopped containers
docker container prune -f

# Remove unused images
docker image prune -a -f
```

###
