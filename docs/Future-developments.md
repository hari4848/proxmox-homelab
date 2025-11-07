### 1. Upgrade VM Size (If Needed)
**Current**: B2ats v2 (2 vCPU, 1GB RAM)

**If experiencing issues**:
- B2s: 2 vCPU, 4GB RAM
- B2ms: 2 vCPU, 8GB RAM

**Cost Consideration**: Balance Azure credits with actual needs

**Alternative**: Move more services to home if Azure credits run out

### 2. Storage Expansion
**Current**: Standard storage

**Consider**: Managed disk for better performance

**Alternative**: Use home server storage via Tailscale (slower but free)

### 3. Azure VM Alternatives
**If student credits expire**:
- **Oracle Cloud Free Tier**: Generous free resources
- **AWS Free Tier**: 12 months free
- **Self-hosted VPS**: Cheaper providers like Hetzner, DigitalOcean
- **Cloudflare Tunnel**: Free alternative to reverse proxy

## Service Migration Paths

### Moving Services Between LXCs

**Scenario**: If arr-stack grows or needs more resources

**Option 1**: Increase arr-stack resources
```bash
pct set 100 -cores 2 -memory 4096
```

**Option 2**: Create new specialized LXC
- Download LXC (arr stack)
- Media LXC (Jellyfin, Audiobookshelf)
- Automation LXC (Home Assistant, n8n)

**Benefits**: Better isolation, independent scaling

### Moving to VMs Instead of LXCs

**When to consider**:
- Need full OS features
- Running Windows applications
- Complex networking requirements
- Kernel module dependencies

**Trade-offs**:
- VMs use more resources
- Slower startup times
- Better isolation
- More compatible

## Long-term Architecture Evolution

### Phase 1: Current State ✅
- Basic homelab setup
- Core services running
- Remote access working
- CGNAT bypass functional

### Phase 2: Hardware Upgrades (Next 3-6 months)
- [ ] RAM upgrade to 32GB
- [ ] Storage expansion to 4TB
- [ ] GPU installation
- [ ] Main services: Nextcloud, Immich, Ollama

**Target Date**: Q1 2026

**Budget**: ₹15,000-20,000

### Phase 3: Advanced Services (6-12 months)
- [ ] Authentication layer (Authelia)
- [ ] Monitoring stack (Prometheus + Grafana)
- [ ] Automated backups with offsite copy
- [ ] Additional services based on needs

**Target Date**: Q2-Q3 2026

**Budget**: Minimal (mostly software)

### Phase 4: Infrastructure Maturity (12-24 months)
- [ ] Network segmentation (VLANs)
- [ ] High availability considerations
- [ ] Advanced home automation
- [ ] Development environment

**Target Date**: Q4 2026 - Q1 2027

**Budget**: ₹10,000-20,000 (networking equipment)

### Phase 5: Future Considerations (2+ years)
- [ ] Second server for redundancy
- [ ] Cluster setup (Proxmox cluster)
- [ ] Dedicated NAS
- [ ] Upgrade to newer hardware

**Target Date**: 2027+

**Budget**: TBD based on needs

## Prioritized Roadmap

### Immediate (Next 1-2 months)
1. **Setup automated backups** - Critical, no cost
2. **Implement monitoring** - Uptime Kuma at minimum
3. **Document current setup** - ✅ Completed with these docs
4. **Test disaster recovery** - Verify backups work

### Short-term (3-6 months)
1. **RAM upgrade** - Enables more services
2. **Storage expansion** - Prevents running out of space
3. **Install Nextcloud** - High-value service
4. **Setup authentication layer** - Security improvement

### Medium-term (6-12 months)
1. **GPU installation** - Unlocks Ollama, Immich, transcoding
2. **Deploy Immich** - After GPU
3. **Setup Ollama** - After GPU
4. **Monitoring stack** - Prometheus + Grafana
5. **Automated backup system** - With offsite copy

### Long-term (12+ months)
1. **Network segmentation** - Security enhancement
2. **Development environment** - If needed
3. **Consider second server** - Redundancy
4. **Explore clustering** - High availability

## Budget Planning

### Immediate Costs (₹0)
- Software setup only
- Use existing hardware
- Leverage Azure student credits

### Short-term Costs (₹8,000-10,000)
- RAM: ₹3,000-5,000
- Storage (4TB HDD): ₹5,000-8,000

### Medium-term Costs (₹5,000-10,000)
- GPU (used): ₹5,000-10,000
- PSU upgrade if needed: ₹2,000-4,000

### Optional/Future Costs
- UPS: ₹3,000-8,000
- Managed switch: ₹3,000-8,000
- Additional storage: ₹5,000-8,000
- Second server: ₹20,000-40,000

### Total Estimated Budget (Next 12 months)
**Minimum**: ₹15,000 (RAM + Storage + GPU)
**Comfortable**: ₹25,000 (Above + UPS + Network upgrades)
**Maximum**: ₹35,000 (All upgrades)

## Service Dependency Map

### After RAM Upgrade (32GB)
**Can Enable**:
- Nextcloud
- Monitoring stack
- Authelia
- Vaultwarden
- Paperless-ngx

### After Storage Upgrade (4TB)
**Can Enable**:
- Larger media library
- Nextcloud with generous space
- Local backups
- More Docker volumes

### After GPU Installation
**Can Enable**:
- Jellyfin hardware transcoding (multiple streams)
- Ollama (LLM with reasonable performance)
- Immich (with ML features)
- Frigate (if adding cameras)
- Stable Diffusion (optional)

### Services That Don't Need Upgrades
**Can deploy now**:
- Uptime Kuma (minimal resources)
- Vaultwarden (lightweight)
- Portainer (Docker management)
- Homepage/Dashboard (lightweight)

## Performance Optimization Targets

### Current Performance
- Proxmox: Runs smoothly
- LXCs: Adequate but tight on RAM
- Services: Functional, some slowness
- Remote access: Dependent on upload speed

### After RAM Upgrade
- Proxmox: Better caching
- LXCs: More headroom for peaks
- Services: Faster, more concurrent users
- Can run more services simultaneously

### After Storage Upgrade
- No more space anxiety
- Larger media library
- Room for high-quality content
- Space for backups and snapshots

### After GPU Addition
- Jellyfin: 3-5 simultaneous transcodes
- Ollama: 5-10 tokens/second (vs <1 on CPU)
- Immich: ML processing in minutes vs hours
- Overall: Better user experience

## Alternative Architectures Considered

### Option 1: All-in-Cloud
**Pros**: No hardware maintenance, scalable
**Cons**: Expensive, subscription costs, data privacy
**Verdict**: Not suitable for media server due to costs

### Option 2: VPS Instead of Azure
**Pros**: Often cheaper than Azure
**Cons**: Still costs money after student credits
**Verdict**: Consider if Azure credits expire

### Option 3: Cloudflare Tunnel
**Pros**: Free, no VM needed, DDoS protection
**Cons**: Terms of service restrictions on media
**Verdict**: Good for non-media services, against ToS for video streaming

### Option 4: DDNS with Port Forwarding
**Pros**: No cloud VM costs
**Cons**: Not possible with CGNAT
**Verdict**: Not feasible with current ISP

### Option 5: Multiple Specialized Servers
**Pros**: Better isolation, can upgrade independently
**Cons**: Higher power costs, more complexity
**Verdict**: Future consideration if expanding significantly

## Risk Mitigation

### Hardware Failure Risks

**Current Risk**: Single point of failure

**Mitigations**:
1. **Implement automated backups**
   - Proxmox VZdump for LXCs
   - Docker volume backups
   - Critical data to external drive

2. **Document everything**
   - ✅ Architecture docs
   - ✅ Configuration files backed up
   - Keep Docker compose files in git repo

3. **Test disaster recovery**
   - Practice restoring from backup
   - Document recovery procedures
   - Keep bootable Proxmox USB ready

**Future**: Consider RAID or second server for redundancy

### Data Loss Risks

**Current Risk**: Single HDD with no RAID

**Mitigations**:
1. **Implement 3-2-1 backup strategy**
   - 3 copies of important data
   - 2 different storage types
   - 1 offsite copy

2. **Critical data priorities**:
   - Personal photos/documents (highest)
   - Configuration files (high)
   - Media library (medium - can be reacquired)

3. **After storage upgrade**:
   - Consider SnapRAID for parity
   - Or RAID 1 for important data only
   - Regular backup verification

### Security Risks

**Current Mitigations**:
- No direct port forwarding (CGNAT helps)
- Tailscale for admin access
- NPM for public services only
- Container isolation

**Future Enhancements**:
1. **Add authentication layer** (Authelia)
2. **Implement fail2ban** on Azure VM
3. **Enable 2FA** on all services
4. **Regular security audits**
5. **Network segmentation** (VLANs)

### Service Availability Risks

**Current Risk**: Single server means downtime during issues

**Mitigations**:
1. **Keep critical services separate**
   - arr-stack can be turned off
   - main-apps stays running

2. **Quick recovery procedures**
   - Documented troubleshooting steps
   - Automated service restarts
   - Monitoring and alerts

3. **Minimize planned downtime**
   - Updates during off-hours
   - Test in staging when possible
   - Keep rollback plans ready

**Future**: Consider high availability if needed

## Technology Choices and Alternatives

### Container Platform: Docker vs Alternatives

**Current Choice**: Docker with Docker Compose

**Alternatives Considered**:
- **Podman**: Rootless, more secure, compatible with Docker
- **Kubernetes/K3s**: Overkill for current scale
- **LXD**: System containers, already using LXC

**Verdict**: Docker Compose is right choice for current scale

**Future Consideration**: If cluster grows, consider K3s

### Hypervisor: Proxmox vs Alternatives

**Current Choice**: Proxmox VE

**Alternatives**:
- **VMware ESXi**: More resource-intensive, enterprise-focused
- **XCP-ng**: Open source, similar to XenServer
- **Bare metal**: No virtualization overhead

**Verdict**: Proxmox is excellent for homelab

**Advantages**:
- Great web UI
- LXC support (lightweight)
- Active community
- Free and open source

### VPN: Tailscale vs Alternatives

**Current Choice**: Tailscale

**Alternatives**:
- **WireGuard**: Manual setup, more control
- **OpenVPN**: Older, slower, more complex
- **ZeroTier**: Similar to Tailscale

**Verdict**: Tailscale is perfect for current needs

**Advantages**:
- Zero configuration
- Mesh networking
- Subnet routing
- Free for personal use
- Mobile apps

**Future**: May add WireGuard as backup/alternative

### Reverse Proxy: NPM vs Alternatives

**Current Choice**: Nginx Proxy Manager

**Alternatives**:
- **Traefik**: More dynamic, Docker-native
- **Caddy**: Simpler, automatic HTTPS
- **HAProxy**: More powerful, complex
- **Nginx**: NPM but manual config

**Verdict**: NPM is good for current needs

**May Switch To**: Traefik if needing more automation

## Service Selection Criteria

### When Choosing New Services

**Must Have**:
1. Active development and community
2. Docker support (or easy containerization)
3. Reasonable resource requirements
4. Good documentation
5. Mobile apps if relevant

**Nice to Have**:
1. ARM support (future-proofing)
2. LDAP/OAuth integration
3. API for automation
4. Backup/export functionality
5. Dark mode UI

**Red Flags**:
1. Abandoned projects
2. Excessive resource usage
3. Poor security track record
4. Vendor lock-in
5. Requires paid license for basic features

### Service Evaluation Checklist

Before adding a new service:
- [ ] Does it solve a real need?
- [ ] Are there mature alternatives?
- [ ] Do I have enough resources?
- [ ] Can I back it up easily?
- [ ] Is the project actively maintained?
- [ ] Is there good documentation?
- [ ] Can I secure it properly?
- [ ] Will it integrate with existing setup?

## Learning and Skill Development

### Current Skills Demonstrated
- ✅ Linux system administration
- ✅ Proxmox/LXC management
- ✅ Docker and containerization
- ✅ Networking fundamentals
- ✅ VPN configuration (Tailscale)
- ✅ Reverse proxy setup
- ✅ Cloud infrastructure (Azure)

### Skills to Develop

**Infrastructure**:
- Advanced networking (VLANs, routing)
- Storage management (RAID, ZFS)
- Backup strategies
- Monitoring and alerting
- Security hardening

**DevOps**:
- CI/CD pipelines
- Infrastructure as Code (Terraform, Ansible)
- Git workflows
- Automated testing

**Programming** (Optional):
- Python for automation
- Bash scripting
- API integration
- Custom dashboards

### Learning Resources

**Free Resources**:
- r/selfhosted - Community and inspiration
- r/homelab - Hardware and infrastructure
- TechnoTim YouTube - Homelab tutorials
- NetworkChuck - Networking and security
- Official documentation for each service

**Hands-on Learning**:
- Break things and fix them
- Test new services in separate LXCs
- Document everything you learn
- Share knowledge with community

## Success Metrics

### Current State (Baseline)
- Services running: 9 (Jellyfin, Audiobookshelf, Home Assistant, n8n, Sonarr, Radarr, Prowlarr, qBittorrent, Samba)
- Uptime: Manual monitoring
- Resource usage: ~70% RAM, ~40% storage
- Remote access: Functional via Tailscale + Azure

### Short-term Goals (6 months)
- [ ] Services running: 12-15 (add Nextcloud, monitoring, auth)
- [ ] Uptime: 99%+ with monitoring in place
- [ ] Resource usage: <70% RAM after upgrade
- [ ] Automated backups: Daily for critical data
- [ ] Documentation: Complete (✅ architecture, network, troubleshooting)

### Medium-term Goals (12 months)
- [ ] Services running: 15-20 (add Immich, Ollama, additional tools)
- [ ] Uptime: 99.5%+ with alerting
- [ ] Hardware: RAM upgraded to 32GB, storage to 4TB, GPU installed
- [ ] Security: Authentication layer, 2FA, network segmentation
- [ ] Disaster recovery: Tested and documented

### Long-term Goals (24 months)
- [ ] Mature homelab infrastructure
- [ ] High availability for critical services
- [ ] Comprehensive monitoring and automation
- [ ] Development environment setup
- [ ] Possible second server or cluster

## Community Contribution

### Ways to Give Back

**Documentation**:
- Share setup guides
- Write tutorials for others
- Contribute to project documentation

**Open Source**:
- Report bugs
- Submit pull requests
- Help with translations

**Community Support**:
- Answer questions on Reddit/forums
- Share lessons learned
- Create content (blog, YouTube)

### Showcase Your Homelab

**Platforms**:
- r/selfhosted showcase threads
- r/homelab
- ServeTheHome forums
- Personal blog

**What to Share**:
- Architecture diagrams
- Lessons learned
- Cost breakdowns
- Performance metrics

## Contingency Plans

### If Azure Credits Run Out

**Options**:
1. **Oracle Cloud Free Tier**
   - 2 AMD VMs (1/8 OCPU, 1GB RAM each)
   - Or 4 ARM VMs (1 OCPU, 6GB RAM each)
   - 200GB storage
   - Generous bandwidth

2. **Cloudflare Tunnel**
   - Free alternative
   - No VM needed
   - But check ToS for media streaming

3. **Self-hosted VPS**
   - $5-10/month from various providers
   - More control than student packs

4. **Reduce public services**
   - Only expose critical services
   - Use Tailscale exclusively
   - Share access via Tailscale to family

### If Home Server Hardware Fails

**Preparation**:
- Keep recent backups
- Document configuration
- Have spare parts if possible
- Keep Proxmox installation USB ready

**Recovery Steps**:
1. Assess damage (PSU? Motherboard? Drive?)
2. Replace failed component
3. Reinstall Proxmox if needed
4. Restore LXCs from backup
5. Reconfigure networking
6. Restore Docker services

**Prevention**:
- Regular backups
- Monitor hardware health (SMART, temps)
- Plan for eventual replacement

### If ISP Changes CGNAT Policy

**If dynamic public IP becomes available**:
- Could eliminate Azure VM
- Direct port forwarding
- Still use Tailscale for security
- Save on cloud costs

**If CGNAT becomes stricter**:
- Current setup already handles this
- Tailscale + cloud VM works regardless
- No changes needed

## Maintenance Schedule

### Daily
- Check critical services are running
- Monitor Tailscale connectivity
- Review any alerts

### Weekly
- Review logs for errors
- Check disk space
- Verify backups completed
- Update Docker images for critical services

### Monthly
- Apply system updates
- Review resource usage trends
- Clean up Docker (prune)
- Test a backup restore
- Update documentation if changed

### Quarterly
- Review security posture
- Audit user access
- Plan upcoming upgrades
- Review and adjust resource allocations
- Full system backup
- Test disaster recovery

### Annually
- Hardware health check
- Replace aging components proactively
- Review architecture for improvements
- Update long-term roadmap
- Audit total cost of ownership

## Cost-Benefit Analysis

### Current Setup Costs

**One-time**:
- Server hardware: ₹0 (repurposed old PC)
- Domain: ₹0 (GitHub Student Pack)
- Azure VM: ₹0 (Student credits)

**Ongoing**:
- Electricity: ~₹300-500/month (~50W server)
- Internet: Already paying for Jio Fiber
- **Total: ~₹400/month**

### Comparable Cloud Costs

**Streaming equivalent (Netflix + Spotify + Storage)**:
- Netflix: ₹649/month
- YouTube Premium: ₹129/month (for ad-free)
- Spotify/music: ₹119/month
- Cloud storage (100GB): ₹130/month
- **Total: ~₹1,000/month**

### Value Proposition

**Homelab Advantages**:
- ✅ Unlimited storage (within hardware limits)
- ✅ Complete privacy and control
- ✅ Learning and skill development
- ✅ Customization and flexibility
- ✅ No subscription costs
- ✅ Can host unlimited users
- ✅ Not dependent on third-party services

**Homelab Disadvantages**:
- ❌ Requires technical knowledge
- ❌ Maintenance responsibility
- ❌ Power consumption
- ❌ Hardware failures your problem
- ❌ Not as convenient as cloud services

**Verdict**: Worth it for hobbyist/enthusiast who values control and learning

## Final Thoughts

### Key Principles

1. **Start Small, Scale Gradually**
   - Current setup is solid foundation
   - Add services as needed, not because you can

2. **Prioritize Stability Over Features**
   - Running services > newest services
   - Backups > new services
   - Documentation > complexity

3. **Automate What You Can**
   - Backups should be automatic
   - Updates can be automated
   - Monitoring and alerts are key

4. **Learn Continuously**
   - Each service teaches something new
   - Break things in test environment
   - Document what you learn

5. **Enjoy the Journey**
   - It's a hobby, not a job
   - Don't stress about perfection
   - Share with the community

### Next Steps

**Immediate Actions**:
1. ✅ Complete documentation (done with these guides)
2. Setup Uptime Kuma for monitoring
3. Configure automated Proxmox backups
4. Create restore procedures document
5. Test backup restoration

**This Month**:
1. Research RAM upgrade options
2. Price check storage options
3. Setup monitoring dashboard
4. Optimize current services

**Next Quarter**:
1. Purchase and install RAM
2. Purchase and install storage
3. Deploy Nextcloud
4. Setup authentication layer

**Happy Homelabbing! 🚀**
   # Future Development Plan

## Overview

This document outlines planned upgrades, new services, and improvements for the homelab infrastructure. Plans are organized by priority and complexity.

## Hardware Upgrades

### High Priority - Already Planned

#### 1. RAM Upgrade (12GB → 32GB+)
**Current Limitation**: 12GB is tight with two LXCs and multiple services

**Target**: 32GB DDR3 (or maximum supported by motherboard)

**Benefits**:
- More headroom for existing services
- Ability to run more concurrent services
- Better performance during peak usage
- Enable more aggressive caching

**Estimated Cost**: ₹3,000-5,000 (depending on DDR3 availability)

**Implementation Steps**:
1. Check motherboard specs for max RAM and speed
2. Source compatible DDR3 modules
3. Schedule downtime for installation
4. Test stability after installation
5. Adjust LXC resource allocations

**Impact**:
- main-apps LXC: Increase to 16GB RAM
- arr-stack LXC: Keep at 3GB or increase to 4GB
- Enable more services (Nextcloud, Immich)

#### 2. Storage Expansion (1TB → 4TB+)
**Current Limitation**: 1TB fills quickly with high-quality media

**Target**: 4TB or 6TB HDD (or multiple drives)

**Benefits**:
- More media storage
- Room for backups
- Space for new services (Nextcloud, Immich)

**Estimated Cost**: ₹5,000-8,000 (4TB HDD)

**Implementation Steps**:
1. Purchase new HDD
2. Install and format new drive
3. Setup RAID if multiple drives (consider mergerfs + SnapRAID)
4. Migrate data from old drive
5. Update mount points in LXCs

**Storage Strategy Options**:
- **Single drive**: Simplest, no redundancy
- **RAID 1 (Mirror)**: Redundancy, half capacity
- **mergerfs + SnapRAID**: Flexibility, parity protection
- **ZFS**: Advanced features, requires more RAM

#### 3. Graphics Card (Planned)
**Current Limitation**: No GPU for transcoding or AI workloads

**Target**: Old/used GPU (GTX 1050/1650, or similar)

**Benefits**:
- Hardware transcoding in Jellyfin (multiple streams)
- Run Ollama for local LLM
- Immich machine learning features
- Potential for other AI services

**Estimated Cost**: ₹5,000-10,000 (used market)

**Compatibility Checks**:
- PCIe slot availability
- Power supply wattage (may need upgrade)
- Case dimensions
- Proxmox GPU passthrough support

**Implementation Steps**:
1. Verify motherboard has PCIe x16 slot
2. Check PSU wattage (300W+ recommended)
3. Purchase compatible GPU
4. Install GPU hardware
5. Configure Proxmox GPU passthrough
6. Pass GPU to main-apps LXC
7. Configure Jellyfin hardware transcoding
8. Setup Ollama

**Services Enabled by GPU**:
- Jellyfin: Hardware transcoding (multiple streams)
- Ollama: Local LLM (Llama 2, Mistral, etc.)
- Immich: Face recognition, object detection
- Frigate (Home Assistant): Object detection for cameras
- Stable Diffusion: Image generation (optional)

### Medium Priority

#### 4. SSD Upgrade (256GB → 512GB)
**Current Status**: 256GB is adequate but could be better

**Benefits**:
- More space for LXC root filesystems
- Room for Docker images
- Can store critical data on SSD

**Estimated Cost**: ₹3,000-4,000

**Consideration**: Not urgent unless running out of space

#### 5. Network Upgrade
**Options**:
- **Managed Switch**: VLANs for network segmentation
- **Separate NIC**: Dedicated network for services
- **Upgrade to WiFi 6 Router**: Better wireless performance

**Benefits**:
- Network segmentation for security
- Better performance and QoS
- Separate management network

**Priority**: Low unless experiencing network issues

### Low Priority

#### 6. UPS (Uninterruptible Power Supply)
**Benefits**:
- Protect against power outages
- Graceful shutdown on extended outage
- Prevent data corruption

**Estimated Cost**: ₹3,000-8,000 (600VA-1000VA)

**Implementation**:
- Install NUT (Network UPS Tools) on Proxmox
- Configure automatic shutdown scripts

#### 7. Dedicated Backup Drive
**Current**: No dedicated backup solution

**Target**: External HDD or second internal drive

**Benefits**:
- Automated backups
- Protection against drive failure
- Version history

**Implementation**: Setup Proxmox backup jobs + rsync scripts

## Planned Services

### High Priority - Planned

#### 1. Nextcloud
**Purpose**: Self-hosted cloud storage and collaboration

**Resource Requirements**:
- CPU: 1-2 cores
- RAM: 2-4GB
- Storage: Depends on usage (50GB+ recommended)

**Features**:
- File sync and share
- Calendar and contacts
- Notes
- Office suite integration
- Mobile apps

**Implementation**:
```yaml
# Docker compose setup in main-apps LXC
services:
  nextcloud:
    image: nextcloud:latest
    ports:
      - 8080:80
    volumes:
      - /mnt/storage/nextcloud:/var/www/html
    environment:
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
      - POSTGRES_PASSWORD=secure_password
```

**Integration**:
- Expose via NPM: nextcloud.domain.com
- Integrate with Jellyfin for media access
- Use for document collaboration

**Prerequisites**:
- RAM upgrade recommended
- Consider database optimization

#### 2. Immich
**Purpose**: Self-hosted Google Photos alternative

**Resource Requirements**:
- CPU: 2+ cores (more with ML)
- RAM: 4GB minimum (8GB+ with ML)
- Storage: Depends on photo library size
- GPU: Required for machine learning features

**Features**:
- Photo and video backup from mobile
- Face recognition
- Object detection
- Map view
- Mobile apps (iOS/Android)

**Implementation**:
```yaml
# Requires GPU passthrough for ML features
# Full docker-compose available from Immich docs
services:
  immich-server:
    image: ghcr.io/immich-app/immich-server
    # ... configuration
```

**Prerequisites**:
- **GPU installation required** for ML features
- RAM upgrade recommended
- Storage expansion for photo library

**Timeline**: After GPU installation

#### 3. Ollama (Local LLM)
**Purpose**: Run large language models locally

**Resource Requirements**:
- CPU: 4+ cores
- RAM: 8GB minimum (16GB+ for larger models)
- GPU: **Required** for reasonable performance
- Storage: 10-50GB per model

**Capabilities**:
- Run models like Llama 2, Mistral, Code Llama
- Chat interface
- API for integration with other services
- Offline AI assistant

**Implementation**:
```bash
# Install in main-apps LXC with GPU passthrough
curl https://ollama.ai/install.sh | sh

# Pull models
ollama pull llama2
ollama pull codellama
```

**Use Cases**:
- Personal AI assistant
- Code generation and help
- Document summarization
- Integration with n8n workflows
- Home Assistant voice assistant

**Prerequisites**:
- **GPU installation is mandatory**
- RAM upgrade highly recommended

**Timeline**: After GPU and RAM upgrades

### Medium Priority

#### 4. Monitoring Stack
**Purpose**: Comprehensive monitoring and alerting

**Components**:
- **Prometheus**: Metrics collection
- **Grafana**: Visualization and dashboards
- **Node Exporter**: System metrics
- **cAdvisor**: Container metrics
- **Uptime Kuma**: Service uptime monitoring

**Resource Requirements**:
- CPU: 1 core
- RAM: 2-3GB
- Storage: 10-20GB for metrics retention

**Benefits**:
- Real-time monitoring of all services
- Historical data and trends
- Alerting for issues
- Beautiful dashboards

**Implementation Priority**: Medium - nice to have but not critical

#### 5. VPN Server (WireGuard)
**Purpose**: Alternative remote access (in addition to Tailscale)

**Use Cases**:
- Share access with family/friends
- Access from restricted networks
- Redundancy if Tailscale has issues

**Note**: Tailscale already provides this functionality, so this is low priority

#### 6. Backup Solution
**Options**:
- **Duplicati**: Web-based backup
- **Restic**: CLI backup with encryption
- **Borg**: Deduplicated backups
- **Proxmox Backup Server**: Enterprise-grade

**Implementation**: Setup automated backups to external drive or cloud

#### 7. Vaultwarden
**Purpose**: Self-hosted Bitwarden password manager

**Resource Requirements**:
- CPU: <0.5 core
- RAM: 512MB
- Storage: <1GB

**Benefits**:
- Password management
- Secure notes
- 2FA support
- Mobile and browser apps

**Priority**: Medium - security improvement

#### 8. Paperless-ngx
**Purpose**: Document management system

**Features**:
- OCR for scanned documents
- Tag and organize documents
- Full-text search
- Mobile app

**Use Case**: Digitize and manage important documents

### Low Priority - Future Considerations

#### 9. Frigate (with GPU)
**Purpose**: NVR (Network Video Recorder) with AI object detection

**Requirements**:
- GPU required for object detection
- IP cameras or webcams
- Storage for recordings

**Timeline**: After GPU installation and if adding cameras

#### 10. Home Assistant Addons
**Additional HA Services**:
- **Zigbee2MQTT**: Zigbee device integration
- **ESPHome**: ESP32/ESP8266 device management
- **Node-RED**: Alternative to n8n
- **AdGuard Home**: Network-wide ad blocking

**Priority**: Based on smart home needs

#### 11. Game Servers
**If interested in hosting game servers**:
- Minecraft
- Terraria
- Valheim
- Other multiplayer games

**Requirements**: Depends on game, typically 2-4GB RAM per server

#### 12. Development Environment
**Purpose**: Development and testing

**Services**:
- **Gitea**: Git server
- **Drone**: CI/CD
- **Code-server**: VS Code in browser
- **Portainer**: Docker management UI

**Use Case**: Software development workflow

## Network and Infrastructure Improvements

### 1. Authentication Layer (High Priority)
**Purpose**: Centralized authentication for all services

**Options**:
- **Authelia**: Lightweight, easy to setup
- **Authentik**: More features, heavier
- **Keycloak**: Enterprise-grade, complex

**Benefits**:
- Single sign-on (SSO) for all services
- 2FA/MFA for security
- Centralized user management
- Better security posture

**Recommended**: Authelia (good balance of features and simplicity)

**Implementation**:
```yaml
# Add to Docker compose
authelia:
  image: authelia/authelia
  # ... configuration
  # Integrate with NPM for all services
```

### 2. Separate VLANs (Medium Priority)
**Purpose**: Network segmentation for security

**VLANs**:
- Management (Proxmox, switches)
- Services (LXCs, services)
- IoT (Home Assistant devices)
- Guest (untrusted devices)

**Requirements**:
- Managed switch
- VLAN-aware router or pfSense/OPNsense

**Benefits**:
- Isolation of IoT devices
- Better security
- QoS and traffic shaping

### 3. Reverse Proxy Improvements
**Enhancements**:
- **Cloudflare Tunnel**: Alternative to Tailscale + Azure
- **Traefik**: More dynamic than NPM
- **Caddy**: Automatic HTTPS, simpler config

**Current Setup**: NPM on Azure VM works well

**Consideration**: Only if current setup has issues

### 4. Backup Strategy (High Priority)
**3-2-1 Backup Rule**:
- 3 copies of data
- 2 different media
- 1 offsite copy

**Implementation**:
1. **Local**: Proxmox backups to second HDD
2. **Local**: Important data synced to external drive
3. **Offsite**: Critical data to cloud (B2, AWS S3 Glacier)

**Automated Schedule**:
- Daily: Critical VMs/LXCs
- Weekly: Full system backup
- Monthly: Verify and test restores

### 5. Monitoring and Alerting (Medium Priority)
**Setup comprehensive monitoring**:
- Service uptime (Uptime Kuma)
- System metrics (Prometheus + Grafana)
- Log aggregation (Loki or ELK)
- Alerts (email, Telegram, Discord)

**Metrics to Track**:
- Service availability
- Resource usage trends
- Network performance
- Disk space growth
- Error rates

## Azure VM Optimization

### 1. Upgrade VM Size (If Needed)
**Current**: B2ats v2 (2 vCPU, 1GB RAM)

**If experiencing issues**:
- B2s: 2 vCPU, 4GB RAM
- B2ms: 2 vCPU, 8GB RAM

**Cost Consideration**: Azure credit from student pack

###
