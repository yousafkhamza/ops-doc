# Linux Administration Interview Questions and Answers

## System Boot Process
1. **Q: Explain the Linux boot process sequence**
   A: The sequence is:
   - BIOS/UEFI POST
   - Boot loader (GRUB)
   - Kernel initialization
   - systemd/init process
   - Run level services

2. **Q: What are the different runlevels in Linux?**
   A: Common runlevels:
   - 0: Shutdown
   - 1: Single user mode
   - 2: Multiuser mode without networking
   - 3: Multiuser mode with networking
   - 5: Graphical mode with networking
   - 6: Reboot

## File System & Permissions

1. **Q: Explain the Linux permission structure**
   A: Permissions are represented as:
   - r (4): Read
   - w (2): Write
   - x (1): Execute
   For each: user/owner, group, others

2. **Q: What's the difference between soft and hard links?**
   A: 
   - Soft link (symbolic): Points to file name, can cross file systems, breaks if source is deleted
   - Hard link: Points to inode, must be on same filesystem, persists even if original is deleted

3. **Q: Explain special permissions (SUID, SGID, Sticky Bit)**
   A:
   - SUID (4000): Execute as file owner
   - SGID (2000): Execute as group owner
   - Sticky Bit (1000): Only owner can delete files in directory

## Networking

1. **Q: How do you calculate a subnet mask?**
   A: Example for /24:
   - Binary: 11111111.11111111.11111111.00000000
   - Decimal: 255.255.255.0
   - Hosts available: 254 (2^8 - 2)

2. **Q: Common networking commands?**
   A:
   - `ip addr`: Show IP addresses
   - `netstat -tulpn`: Show active connections
   - `ss`: Socket statistics
   - `ping`: Test connectivity
   - `traceroute`: Show packet path

## Common Ports & Services

1. **Q: List important service ports**
   A:
   - SSH: 22
   - FTP: 20 (data), 21 (control)
   - HTTP: 80
   - HTTPS: 443
   - DNS: 53
   - NFS: 2049
   - SMTP: 25
   - DHCP: 67/68
   - PostgreSQL: 5432
   - MySQL: 3306
   - Named/BIND: 53

## Configuration Paths

1. **Q: Where are important config files located?**
   A:
   - Apache: `/etc/httpd/` (RHEL), `/etc/apache2/` (Debian)
   - Nginx: `/etc/nginx/`
   - SSH: `/etc/ssh/sshd_config`
   - Network: `/etc/sysconfig/network-scripts/` (RHEL)
   - DNS: `/etc/named.conf`, `/var/named/`
   - System logs: `/var/log/`
   - User accounts: `/etc/passwd`, `/etc/shadow`

## Web Servers

1. **Q: How to configure Apache virtual hosts?**
   A: In `/etc/httpd/conf.d/vhost.conf`:
   ```apache
   <VirtualHost *:80>
       ServerName example.com
       ServerAlias www.example.com
       DocumentRoot /var/www/example.com
       ErrorLog logs/example.com-error.log
       CustomLog logs/example.com-access.log combined
   </VirtualHost>
   ```

2. **Q: Nginx vs Apache differences?**
   A:
   - Nginx: Event-driven, better for static content, reverse proxy
   - Apache: Process/thread per connection, better for dynamic content, .htaccess support

## Package Management

1. **Q: RPM vs YUM commands?**
   A:
   - RPM: Low-level package manager
     - `rpm -i package.rpm`: Install
     - `rpm -q package`: Query
   - YUM: High-level with dependency resolution
     - `yum install package`
     - `yum update package`

2. **Q: How to add a new repository?**
   A:
   - Create .repo file in `/etc/yum.repos.d/`
   - Run `yum clean all && yum repolist`

## Text Processing Commands

1. **Q: Compare grep vs awk**
   A:
   - grep: Pattern matching and searching
     ```bash
     grep "pattern" file
     ```
   - awk: Pattern scanning and text processing language
     ```bash
     awk '{print $1}' file
     ```

2. **Q: Compare sed vs awk**
   A:
   - sed: Stream editor for basic text transformation
   - awk: Better for structured data processing and calculations

## System Monitoring

1. **Q: How to troubleshoot high CPU/memory usage?**
   A: Use commands:
   - `top`/`htop`: Process monitoring
   - `vmstat`: Virtual memory stats
   - `iostat`: IO statistics
   - `sar`: System activity report

2. **Q: How to check system logs?**
   A:
   - `journalctl`: For systemd logs
   - `tail -f /var/log/messages`
   - `dmesg`: Kernel messages

## Security

1. **Q: Basic security measures?**
   A:
   - Disable root login
   - Use SSH keys instead of passwords
   - Configure firewall (iptables/firewalld)
   - Regular security updates
   - Monitor logs for suspicious activity

2. **Q: How to set up SSH key authentication?**
   A:
   ```bash
   ssh-keygen -t rsa
   ssh-copy-id user@host
   ```