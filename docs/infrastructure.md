# Infrastructure

## Overview

Cyber Motors is a fictional automotive dealership environment
built to practice systems administration, networking,
security, virtualization, and cloud technologies.

The environment currently runs on a single Proxmox VE host
with an Ubuntu Server virtual machine.

---

## Proxmox Host

| Specification | Details |
|---|---|
| Hostname | `pve01.home.arpa` |
| IP Address | `192.168.1.20` |
| Hypervisor | Proxmox VE 8.4.0 |
| Management Interface | `eno1` |

The Proxmox host provides the virtualization platform for
the Cyber Motors environment.

---

## Virtual Machines

### ubuntu01

| Specification | Details |
|---|---|
| OS | Ubuntu Server 26.04.1 LTS |
| IP Address | `192.168.1.30/24` |
| VM ID | `100` |
| Purpose | Linux infrastructure and administration server |

### Current Services

- OpenSSH
- Nginx
- Internal employee portal

---

## Nginx Web Server

### Purpose

Nginx provides an internal employee portal for the Cyber Motors
environment.

### Configuration

- Web root: `/var/www/html/`
- Entry point: `/var/www/html/index.html`
- HTTP: Port 80
- Access: Internal network only

### Current Status

The initial employee portal is operational and accessible from
the internal network.

### Administration

Nginx is managed using `systemctl`.

Logs are reviewed using:

```bash
journalctl -u nginx
```
## Host Firewall - 9/13/2026

### Purpose

UFW (Uncomplicated Firewall) was configured on `ubuntu01` to
control inbound network access to the server.

The firewall follows a default-deny inbound policy, allowing
only services currently required by the server.

### Firewall Policy

| Traffic | Policy |
|---|---|
| Incoming | Deny by default |
| Outgoing | Allow by default |
| Routed | Disabled |

### Allowed Services

| Service | Protocol | Port | Purpose |
|---|---|---:|---|
| SSH | TCP | 22 | Remote administration |
| HTTP | TCP | 80 | Internal employee portal |

Rules were configured for both IPv4 and IPv6 traffic.

### Verification

Firewall status was verified using:

```bash
sudo ufw status verbose
```
---
## Linux Users, Groups, and Department Access

### Purpose

`ubuntu01` was configured to simulate departmental access controls
for the Cyber Motors business environment.

Linux groups were used to represent departments and control access
to department-specific directories.

### Department Groups

The following groups were created:

| Group | Purpose |
|---|---|
| `sales` | Sales department |
| `service` | Service department |
| `finance` | Finance department |
| `share` | Shared company resources |
| `service-readers` | Users who require read-only access to Service records |

The existing `it-admins` group remains available for IT administration.

### Test Users

A test Sales employee was created:

- `sales01` — member of the `sales` group

A test Finance employee was created:

- `finance01` — member of the `service-readers` group

Both users retain their automatically created private primary
groups.

### Department Directory Structure

Department directories were created under:

```text
/srv/cyber-motors/
├── sales/
├── service/
├── finance/
└── shared/
```
## Windows Server Infrastructure

### VM 200 — dc01

A Windows Server virtual machine was created as the foundation for the Windows infrastructure portion of the Cyber Motors environment.

| Property | Configuration |
|---|---|
| VM ID | 200 |
| Hostname | `dc01` |
| Operating System | Windows Server 2025 Standard Evaluation |
| Installation | Desktop Experience |
| CPU | 2 vCPU |
| Memory | 4 GB |
| Disk | 64 GB |
| Disk Controller | VirtIO SCSI |
| Network Adapter | VirtIO |
| Proxmox Bridge | `vmbr0` |
| IPv4 Address | `192.168.1.40/24` |
| Default Gateway | `192.168.1.254` |
| DNS | `192.168.1.254` |
| Role | Future Active Directory Domain Controller |

### Windows Server Installation

Windows Server 2025 was installed as a virtual machine on the Proxmox host `pve01`.

The VM uses UEFI firmware and a virtual EFI disk. Windows Server was installed using the Desktop Experience edition to provide a graphical administration environment while learning Windows Server administration.

### VirtIO Drivers

The VM was configured with VirtIO virtual hardware for storage and networking.

During installation, Windows Setup initially could not detect the 64 GB virtual disk because the required VirtIO SCSI driver was not included in the Windows installation environment.

The `virtio-win` driver ISO was attached to the VM and the appropriate VirtIO SCSI driver was loaded during Windows Setup. After the driver was loaded, the 64 GB virtual disk appeared and Windows installation proceeded normally.

After Windows was installed, the VirtIO network adapter initially lacked a driver as well. The VirtIO network driver (`NetKVM`) was installed from the same driver ISO, allowing Windows to recognize the virtual Ethernet adapter and obtain network connectivity.

### Network Configuration

The server initially used DHCP to verify network connectivity after installation.

After confirming connectivity, the server was assigned a static address:

```text
IP Address:      192.168.1.40
Subnet:          /24 (255.255.255.0)
Gateway:         192.168.1.254
DNS:             192.168.1.254
```
### Windows Local Users and Groups

Created local Windows users and department groups on `dc01` to establish the initial business identity structure.

| Department | Local Group | User |
|---|---|---|
| Sales | `Sales Department` | `sales01` |
| Service | `Service Department` | `service01` |
| Finance | `Finance Department` | `finance01` |

Windows local groups will be used to manage access to department-specific resources through NTFS permissions.

> Note: The group names use the `Department` suffix because names such as `Service` conflicted with existing Windows naming/usage on the system.
