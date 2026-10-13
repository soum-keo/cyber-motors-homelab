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

## Linux ACLs: Group Access and File-Level Exceptions

### Goal

Configure a shared business directory where Sales, Finance, and Service
employees can access the common workspace, while restricting one
specific file to Finance and Service.

### 1. Use Groups for Normal Shared Access

The employees were added to the supplementary `share` group. This is
easier to maintain than adding a separate named-user ACL entry to the
shared directory for every employee.

Department groups such as `sales`, `service`, and `finance` remain
useful for department-specific directories. The `share` group represents
cross-department access.

### 2. Directory Access Does Not Automatically Grant File Access

Linux checks permissions along the path to a file. A user generally
needs execute (`x`) permission on each parent directory to traverse the
path. The file's own ACL then determines whether the user can read or
modify its contents.

Membership in `share` therefore does not automatically mean every file
inside the shared directory must be readable or writable.

### 3. Use a File ACL for a Specific Exception

The test file is owned by `root:share`. Its owning-group ACL entry was
changed to `---`, and named-user ACL entries granted read-only access to
`finance01` and `service01`.

The intended ACL was:

``` text
user::rw-
user:finance01:r--
user:service01:r--
group::---
mask::r--
other::---
```

This allows the selected users to read the file without granting access
through the general `share` group. It also prevents `sales01` from
reading the file through group membership.

### 4. Understand the ACL Mask

The ACL mask sets the maximum effective permissions for:

-   Named users, such as `user:service01`
-   Named groups, such as `group:service-readers`
-   The owning group, `group::`

The mask does not limit the file owner's `user::` permissions or the
`other::` permissions.

The effective permission is the intersection of an applicable ACL entry
and the mask. For example, a named-user entry of `rwx` with a mask of
`r--` gives effective read-only access.

The mask does not grant permissions on its own. The relevant ACL entry
must grant the permission too.

### 5. Verify Actual Access, Not Just Configuration

The following tests were performed:

-   `finance01` successfully read the file with `cat`.
-   `service01` successfully read the file with `cat`.
-   `sales01` received `Permission denied` when attempting to read the
    file.
-   `service01` was denied when `touch` attempted to update the existing
    file's timestamps.

Because the file contained text, the successful `cat` commands also
confirmed that its contents were readable. The failed `touch` confirmed
that read-only access did not allow that timestamp update.

### Key Takeaways

-   Use groups for common access and ACLs for targeted exceptions.
-   Directory traversal and file-content permissions are separate
    checks.
-   Group membership does not override a restrictive file ACL.
-   The ACL mask limits effective permissions for named users, named
    groups, and the owning group.
-   Verify access using the actual user account, then document the
    tested outcome.

