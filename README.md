# nginx-unit Installation Guide

nginx-unit is a free and open-source application server. NGINX Unit provides dynamic application server

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2+ cores
  - RAM: 1GB minimum
  - Storage: 1GB for apps
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default nginx-unit port)
  - Control on 8081
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install nginx-unit
sudo dnf install -y nginx_unit

# Enable and start service
sudo systemctl enable --now nginx-unit

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
nginx-unit --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install nginx-unit
sudo apt install -y nginx_unit

# Enable and start service
sudo systemctl enable --now nginx-unit

# Configure firewall
sudo ufw allow 8080

# Verify installation
nginx-unit --version
```

### Arch Linux

```bash
# Install nginx-unit
sudo pacman -S nginx_unit

# Enable and start service
sudo systemctl enable --now nginx-unit

# Verify installation
nginx-unit --version
```

### Alpine Linux

```bash
# Install nginx-unit
apk add --no-cache nginx_unit

# Enable and start service
rc-update add nginx-unit default
rc-service nginx-unit start

# Verify installation
nginx-unit --version
```

### openSUSE/SLES

```bash
# Install nginx-unit
sudo zypper install -y nginx_unit

# Enable and start service
sudo systemctl enable --now nginx-unit

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
nginx-unit --version
```

### macOS

```bash
# Using Homebrew
brew install nginx_unit

# Start service
brew services start nginx_unit

# Verify installation
nginx-unit --version
```

### FreeBSD

```bash
# Using pkg
pkg install nginx_unit

# Enable in rc.conf
echo 'nginx-unit_enable="YES"' >> /etc/rc.conf

# Start service
service nginx-unit start

# Verify installation
nginx-unit --version
```

### Windows

```bash
# Using Chocolatey
choco install nginx_unit

# Or using Scoop
scoop install nginx_unit

# Verify installation
nginx-unit --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/nginx_unit

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
nginx-unit --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable nginx-unit

# Start service
sudo systemctl start nginx-unit

# Stop service
sudo systemctl stop nginx-unit

# Restart service
sudo systemctl restart nginx-unit

# Check status
sudo systemctl status nginx-unit

# View logs
sudo journalctl -u nginx-unit -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add nginx-unit default

# Start service
rc-service nginx-unit start

# Stop service
rc-service nginx-unit stop

# Restart service
rc-service nginx-unit restart

# Check status
rc-service nginx-unit status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'nginx-unit_enable="YES"' >> /etc/rc.conf

# Start service
service nginx-unit start

# Stop service
service nginx-unit stop

# Restart service
service nginx-unit restart

# Check status
service nginx-unit status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start nginx_unit
brew services stop nginx_unit
brew services restart nginx_unit

# Check status
brew services list | grep nginx_unit
```

### Windows Service Manager

```powershell
# Start service
net start nginx-unit

# Stop service
net stop nginx-unit

# Using PowerShell
Start-Service nginx-unit
Stop-Service nginx-unit
Restart-Service nginx-unit

# Check status
Get-Service nginx-unit
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream nginx_unit_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name nginx_unit.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name nginx_unit.example.com;

    ssl_certificate /etc/ssl/certs/nginx_unit.example.com.crt;
    ssl_certificate_key /etc/ssl/private/nginx_unit.example.com.key;

    location / {
        proxy_pass http://nginx_unit_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName nginx_unit.example.com
    Redirect permanent / https://nginx_unit.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName nginx_unit.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/nginx_unit.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/nginx_unit.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend nginx_unit_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/nginx_unit.pem
    redirect scheme https if !{ ssl_fc }
    default_backend nginx_unit_backend

backend nginx_unit_backend
    balance roundrobin
    server nginx_unit1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R nginx_unit:nginx_unit /etc/nginx_unit
sudo chmod 750 /etc/nginx_unit

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status nginx-unit

# View logs
sudo journalctl -u nginx-unit -f

# Monitor resource usage
top -p $(pgrep nginx_unit)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/nginx_unit"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/nginx_unit-backup-$DATE.tar.gz" /etc/nginx_unit /var/lib/nginx_unit

echo "Backup completed: $BACKUP_DIR/nginx_unit-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop nginx-unit

# Restore from backup
tar -xzf /backup/nginx_unit/nginx_unit-backup-*.tar.gz -C /

# Start service
sudo systemctl start nginx-unit
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u nginx-unit -n 100
sudo tail -f /var/log/nginx_unit/nginx_unit.log

# Check configuration
nginx-unit --version

# Check permissions
ls -la /etc/nginx_unit
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep nginx_unit)

# Check disk I/O
iotop -p $(pgrep nginx_unit)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  nginx_unit:
    image: nginx_unit:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/nginx_unit
      - ./data:/var/lib/nginx_unit
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update nginx_unit

# Debian/Ubuntu
sudo apt update && sudo apt upgrade nginx_unit

# Arch Linux
sudo pacman -Syu nginx_unit

# Alpine Linux
apk update && apk upgrade nginx_unit

# openSUSE
sudo zypper update nginx_unit

# FreeBSD
pkg update && pkg upgrade nginx_unit

# Always backup before updates
tar -czf /backup/nginx_unit-pre-update-$(date +%Y%m%d).tar.gz /etc/nginx_unit

# Restart after updates
sudo systemctl restart nginx-unit
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/nginx_unit

# Clean old logs
find /var/log/nginx_unit -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/nginx_unit
```

## Additional Resources

- Official Documentation: https://docs.nginx_unit.org/
- GitHub Repository: https://github.com/nginx_unit/nginx_unit
- Community Forum: https://forum.nginx_unit.org/
- Best Practices Guide: https://docs.nginx_unit.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
