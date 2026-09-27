# Lessons Learned

## Linux Permissions

Linux permissions use three groups:

- Owner
- Group
- Others

Permission values:

- Read = 4
- Write = 2
- Execute = 1

Example:

644 = rw-r--r--

## Nginx

Nginx acts as the web server.

The master process runs as root while worker
processes run as www-data.

The web root is /var/www/html/.

## HTTP Status Codes

200 = successful request

304 = resource has not changed and the client
can use its cached copy.

## UFW / Host Firewalls

UFW provides a simplified interface for managing the Linux
firewall.

Key concepts:

- A service can be running without being reachable from the network.
- Listening ports can be inspected with `ss`.
- Firewall rules determine which network connections are permitted.
- A default-deny inbound policy allows only explicitly permitted services.
- SSH should be allowed before enabling a restrictive firewall when
  administering the server remotely.
- UFW can manage both IPv4 and IPv6 rules.

### Commands Practiced

```bash
sudo ss -tulpn
sudo ufw status verbose
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow http
```

---
## Windows Server VM Deployment

### Virtual Hardware and Guest Drivers

Windows Server does not necessarily include drivers for every type of virtual hardware presented by a hypervisor.

The `dc01` VM was configured with VirtIO storage and networking. Windows Setup initially could not see the virtual disk because the VirtIO SCSI storage driver was not available.

The VirtIO driver ISO was used to provide the required storage driver. After Windows was installed, the VirtIO network adapter also required its corresponding network driver.

This demonstrated the distinction between:

- Virtual hardware being presented by the hypervisor
- The guest operating system recognizing that hardware
- The guest operating system having a functional driver for that hardware

### Static IP Addressing

DHCP was initially used to establish network connectivity. Once connectivity was confirmed, `dc01` was assigned a static IP address.

Servers benefit from predictable addressing because other systems and services need a reliable way to locate them.

For the Cyber Motors environment:

```text
pve01       192.168.1.20
ubuntu01    192.168.1.30
dc01        192.168.1.40
```
