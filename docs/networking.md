# Networking

## Network Overview

The Cyber Motors Dealership homelab currently operates on a local network using the `192.168.1.0/24` address space.

The Proxmox host and virtual machines use the existing home network to provide connectivity between the lab environment and the internet.

## Current Network

| Component         | Address          |
| ----------------- | ---------------- |
| Network           | `192.168.1.0/24` |
| Default Gateway   | `192.168.1.254`  |
| Proxmox Host      | `192.168.1.20`   |
| Proxmox Interface | `eno1`           |

## Current State

The lab is currently using a relatively simple network design. Network segmentation, VLANs, and additional infrastructure services are planned as the environment develops.

## Planned Improvements

* Introduce VLAN segmentation
* Separate infrastructure and client networks
* Implement dedicated DHCP/DNS services
* Document firewall and routing policies
* Test network access controls
