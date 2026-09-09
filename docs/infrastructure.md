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
