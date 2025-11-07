# Samba Server Setup for Proxmox Homelab

This directory describes the Samba configuration used in this Proxmox-based homelab environment. The primary use-case is for **Network Attached Storage (NAS)**, supporting general file sharing, media server management, and storage for Docker Compose workloads.

## Quick Install

On Ubuntu (default for most of the LXC containers here):

```bash
sudo apt update
sudo apt install samba
```

## Basic Setup Steps

1. **Install Samba**: See the command above.
2. **Edit Configuration**: Modify your `smb.conf` file as needed (see this repo for the running configuration).
3. **Restart Samba**:
    ```bash
    sudo systemctl restart smbd
    ```
4. **Add Samba Users (if needed)**:
    ```bash
    sudo smbpasswd -a <username>
    ```
5. **Access Your Shares**: Connect from Windows, Linux, or macOS using the server’s IP and configured share.

> All directory paths, permission settings, and share details are managed directly in the [`smb.conf`](./smb.conf) file. Refer to that file for exact configuration including user permissions and share locations.

## Tips & Recommendations

- **Dockerized Samba**: Running Samba in a Docker container can simplify deployment and updates, but may introduce networking and volume-mounting complexities.
- **Cockpit Integration**: Beginners may find [Cockpit](https://cockpit-project.org/) useful for managing Samba and other services through a web UI.
- **Official Samba Docs**: For advanced configuration, troubleshooting, and security best practices, see the [official Samba documentation](https://www.samba.org/samba/docs/) and [GitHub repo](https://github.com/samba-team/samba).

## Security

- User and permission management is handled in `smb.conf` and with Samba’s user tools.
- Restrict access as needed for your use-case; see the configuration file for specifics.

---

**Note:** This setup is tested mainly on Ubuntu-based LXC containers in a Proxmox environment but should be adaptable to other Linux distributions with minimal changes.
