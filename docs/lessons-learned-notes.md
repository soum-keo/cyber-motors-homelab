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
