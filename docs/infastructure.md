# Infrastructure

## Proxmox Host

The homelab is currently built from an mini-PC running Proxmox VE.

| Specification        | Details           |
| -------------------- | ----------------- |
| Hostname             | `pve01.home.arpa` |
| IP Address           | `192.168.1.20`    |
| Gateway              | `192.168.1.254`   |
| Hypervisor           | Proxmox VE 8.4.0  |
| Management Interface | `eno1`            |

The Proxmox host provides the virtualization platform for the 'Cyber Motors' environment.

## Virtual Machines

### ubuntu01

| Specification    | Details                                        |
| ---------------- | ---------------------------------------------- |
| Hostname         | `ubuntu01`                                     |
| Operating System | Ubuntu Server 26.04.1 LTS                      |
| Purpose          | Linux infrastructure and administration server |

Additional systems and services will be documented as they are added to the environment.
