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
## Linux Service Accounts and Least Privilege

Nginx uses the `www-data` account for its worker processes rather than running all request handling as root. This follows the principle of least privilege by limiting the permissions available to the web server.

The `www-data` account on `ubuntu01` has UID 33, primary GID 33, and `/usr/sbin/nologin` as its configured shell. This account is intended for service operation rather than ordinary interactive login.

### Why Least Privilege Matters

Using overly permissive filesystem permissions, such as `chmod 777`, can allow unintended users to modify files and directories. If an attacker compromises a service, the permissions of that service's account help determine what resources the attacker can access.

Departmental data should remain inaccessible to unrelated service accounts unless access is explicitly required.

### Key Takeaways

- Human accounts, departmental accounts, and service accounts have different purposes.
- A service should run with only the privileges it needs.
- Directory ownership, group membership, filesystem permissions, and ACLs work together to control access.
- A service account's restricted login shell reduces interactive-login exposure but does not, by itself, restrict all filesystem access.
- Security configurations should be verified rather than assumed.
