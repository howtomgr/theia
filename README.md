# theia Installation Guide

theia is a free and open-source cloud IDE. Theia provides extensible cloud IDE framework

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
  - RAM: 2GB minimum
  - Storage: 10GB for workspace
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3000 (default theia port)
  - None
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

# Install theia
sudo dnf install -y theia

# Enable and start service
sudo systemctl enable --now theia

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
theia --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install theia
sudo apt install -y theia

# Enable and start service
sudo systemctl enable --now theia

# Configure firewall
sudo ufw allow 3000

# Verify installation
theia --version
```

### Arch Linux

```bash
# Install theia
sudo pacman -S theia

# Enable and start service
sudo systemctl enable --now theia

# Verify installation
theia --version
```

### Alpine Linux

```bash
# Install theia
apk add --no-cache theia

# Enable and start service
rc-update add theia default
rc-service theia start

# Verify installation
theia --version
```

### openSUSE/SLES

```bash
# Install theia
sudo zypper install -y theia

# Enable and start service
sudo systemctl enable --now theia

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
theia --version
```

### macOS

```bash
# Using Homebrew
brew install theia

# Start service
brew services start theia

# Verify installation
theia --version
```

### FreeBSD

```bash
# Using pkg
pkg install theia

# Enable in rc.conf
echo 'theia_enable="YES"' >> /etc/rc.conf

# Start service
service theia start

# Verify installation
theia --version
```

### Windows

```bash
# Using Chocolatey
choco install theia

# Or using Scoop
scoop install theia

# Verify installation
theia --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/theia

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
theia --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable theia

# Start service
sudo systemctl start theia

# Stop service
sudo systemctl stop theia

# Restart service
sudo systemctl restart theia

# Check status
sudo systemctl status theia

# View logs
sudo journalctl -u theia -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add theia default

# Start service
rc-service theia start

# Stop service
rc-service theia stop

# Restart service
rc-service theia restart

# Check status
rc-service theia status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'theia_enable="YES"' >> /etc/rc.conf

# Start service
service theia start

# Stop service
service theia stop

# Restart service
service theia restart

# Check status
service theia status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start theia
brew services stop theia
brew services restart theia

# Check status
brew services list | grep theia
```

### Windows Service Manager

```powershell
# Start service
net start theia

# Stop service
net stop theia

# Using PowerShell
Start-Service theia
Stop-Service theia
Restart-Service theia

# Check status
Get-Service theia
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream theia_backend {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name theia.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name theia.example.com;

    ssl_certificate /etc/ssl/certs/theia.example.com.crt;
    ssl_certificate_key /etc/ssl/private/theia.example.com.key;

    location / {
        proxy_pass http://theia_backend;
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
    ServerName theia.example.com
    Redirect permanent / https://theia.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName theia.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/theia.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/theia.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend theia_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/theia.pem
    redirect scheme https if !{ ssl_fc }
    default_backend theia_backend

backend theia_backend
    balance roundrobin
    server theia1 127.0.0.1:3000 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R theia:theia /etc/theia
sudo chmod 750 /etc/theia

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
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
sudo systemctl status theia

# View logs
sudo journalctl -u theia -f

# Monitor resource usage
top -p $(pgrep theia)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/theia"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/theia-backup-$DATE.tar.gz" /etc/theia /var/lib/theia

echo "Backup completed: $BACKUP_DIR/theia-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop theia

# Restore from backup
tar -xzf /backup/theia/theia-backup-*.tar.gz -C /

# Start service
sudo systemctl start theia
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u theia -n 100
sudo tail -f /var/log/theia/theia.log

# Check configuration
theia --version

# Check permissions
ls -la /etc/theia
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3000

# Test connectivity
telnet localhost 3000

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep theia)

# Check disk I/O
iotop -p $(pgrep theia)

# Check connections
ss -an | grep 3000
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  theia:
    image: theia:latest
    ports:
      - "3000:3000"
    volumes:
      - ./config:/etc/theia
      - ./data:/var/lib/theia
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update theia

# Debian/Ubuntu
sudo apt update && sudo apt upgrade theia

# Arch Linux
sudo pacman -Syu theia

# Alpine Linux
apk update && apk upgrade theia

# openSUSE
sudo zypper update theia

# FreeBSD
pkg update && pkg upgrade theia

# Always backup before updates
tar -czf /backup/theia-pre-update-$(date +%Y%m%d).tar.gz /etc/theia

# Restart after updates
sudo systemctl restart theia
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/theia

# Clean old logs
find /var/log/theia -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/theia
```

## Additional Resources

- Official Documentation: https://docs.theia.org/
- GitHub Repository: https://github.com/theia/theia
- Community Forum: https://forum.theia.org/
- Best Practices Guide: https://docs.theia.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
