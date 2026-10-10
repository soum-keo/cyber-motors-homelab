# Networking

## Network Overview

The Cyber Motors homelab currently operates on the existing home network using the `192.168.1.0/24` IPv4 address space.

The Proxmox host and virtual machines connect to the home LAN through the Proxmox Linux bridge `vmbr0`. This allows the virtual machines to communicate with the local network and access the internet through the existing router.

The current environment uses a flat network design. Dedicated VLANs, network segmentation, and lab-managed DHCP/DNS services have not yet been implemented.

## Current Network

| Component | Address | Interface / Role |
|---|---|---|
| Network | `192.168.1.0/24` | Local IPv4 network |
| Default Gateway | `192.168.1.254` | Home router |
| DNS Server | `192.168.1.254` | Home router |
| Proxmox Host (`pve01`) | `192.168.1.20/24` | `eno1`, bridged through `vmbr0` |
| Ubuntu Server (`ubuntu01`) | `192.168.1.30/24` | `ens18` |
| Windows Server (`dc01`) | `192.168.1.40/24` | `Ethernet` |

## Address Allocation

The lab uses manually configured static IPv4 addresses for its infrastructure systems.

| Address | Intended Device |
|---|---|
| `192.168.1.20` | Proxmox host |
| `192.168.1.30` | Ubuntu Server |
| `192.168.1.40` | Windows Server |
| `192.168.1.50` | Reserved for a future security/lab VM |

The home router currently uses `192.168.1.254` as the default gateway. Its DHCP pool is configured separately from the manually assigned lab addresses.

## Proxmox Virtual Networking

Proxmox uses the physical network interface `eno1` and the Linux bridge `vmbr0` to provide network connectivity to virtual machines.

The Ubuntu and Windows Server VMs use VirtIO network adapters connected to `vmbr0`.

This bridge allows the VMs to participate directly in the existing home LAN rather than operating behind a separate, dedicated lab router.

## Ubuntu Server Networking

### VM 100 — `ubuntu01`

Ubuntu Server uses a static IPv4 configuration managed through Netplan.

| Setting | Value |
|---|---|
| Hostname | `ubuntu01` |
| Interface | `ens18` |
| IPv4 Address | `192.168.1.30/24` |
| Default Gateway | `192.168.1.254` |
| DNS Server | `192.168.1.254` |
| Address Assignment | Static |
| Network Adapter | VirtIO |

DHCP is disabled for the active Netplan configuration. The routing table includes a default route through `192.168.1.254` and a directly connected route for `192.168.1.0/24`.

### Connectivity and Services

The Ubuntu VM hosts an internal Cyber Motors dealership webpage using Nginx.

- Nginx listens on TCP port `80`.
- UFW is enabled, with inbound TCP ports `22` (SSH) and `80` (HTTP) allowed.
- Outbound traffic is allowed by the current UFW policy.
- The active network configuration and routing table were inspected using Linux networking utilities.

## Windows Server Networking

### VM 200 — `dc01`

Windows Server uses a manually configured static IPv4 address.

| Setting | Value |
|---|---|
| Hostname | `dc01` |
| Interface | `Ethernet` |
| IPv4 Address | `192.168.1.40/24` |
| Default Gateway | `192.168.1.254` |
| DNS Server | `192.168.1.254` |
| Address Assignment | Static |
| Network Adapter | VirtIO |

The VirtIO network driver was installed to enable connectivity from the Windows Server guest.

The static IP configuration was applied through PowerShell and verified using `Get-NetIPConfiguration`. Connectivity to the default gateway and Ubuntu Server was also tested.

`dc01` is currently a standalone Windows Server. Active Directory Domain Services and lab-managed DNS have not yet been configured.

## Current State

The homelab currently uses a flat LAN design with static IP addresses assigned to its infrastructure systems.

Connectivity between the Proxmox host, Ubuntu Server, and Windows Server relies on the existing home router and the Proxmox bridge.

The Ubuntu and Windows guests use the home router for DNS resolution. This configuration will change if Windows Server is promoted to a domain controller and begins providing DNS for the lab domain.

## Planned Improvements

- Introduce VLAN segmentation.
- Separate infrastructure, server, and client networks.
- Implement dedicated DHCP/DNS services where appropriate.
- Configure Active Directory-integrated DNS on Windows Server.
- Document firewall and routing policies.
- Test network access controls between systems and departments.
- Explore dedicated routing and firewall services for the lab.
