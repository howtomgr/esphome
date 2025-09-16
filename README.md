# esphome Installation Guide

esphome is a free and open-source ESP device system. ESPHome provides system to control ESP8266/ESP32

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
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for configs
  - Network: HTTP/OTA
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 6052 (default esphome port)
  - Dashboard on 6052
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

# Install esphome
sudo dnf install -y esphome

# Enable and start service
sudo systemctl enable --now esphome

# Configure firewall
sudo firewall-cmd --permanent --add-port=6052/tcp
sudo firewall-cmd --reload

# Verify installation
esphome --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install esphome
sudo apt install -y esphome

# Enable and start service
sudo systemctl enable --now esphome

# Configure firewall
sudo ufw allow 6052

# Verify installation
esphome --version
```

### Arch Linux

```bash
# Install esphome
sudo pacman -S esphome

# Enable and start service
sudo systemctl enable --now esphome

# Verify installation
esphome --version
```

### Alpine Linux

```bash
# Install esphome
apk add --no-cache esphome

# Enable and start service
rc-update add esphome default
rc-service esphome start

# Verify installation
esphome --version
```

### openSUSE/SLES

```bash
# Install esphome
sudo zypper install -y esphome

# Enable and start service
sudo systemctl enable --now esphome

# Configure firewall
sudo firewall-cmd --permanent --add-port=6052/tcp
sudo firewall-cmd --reload

# Verify installation
esphome --version
```

### macOS

```bash
# Using Homebrew
brew install esphome

# Start service
brew services start esphome

# Verify installation
esphome --version
```

### FreeBSD

```bash
# Using pkg
pkg install esphome

# Enable in rc.conf
echo 'esphome_enable="YES"' >> /etc/rc.conf

# Start service
service esphome start

# Verify installation
esphome --version
```

### Windows

```bash
# Using Chocolatey
choco install esphome

# Or using Scoop
scoop install esphome

# Verify installation
esphome --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/esphome

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
esphome --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable esphome

# Start service
sudo systemctl start esphome

# Stop service
sudo systemctl stop esphome

# Restart service
sudo systemctl restart esphome

# Check status
sudo systemctl status esphome

# View logs
sudo journalctl -u esphome -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add esphome default

# Start service
rc-service esphome start

# Stop service
rc-service esphome stop

# Restart service
rc-service esphome restart

# Check status
rc-service esphome status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'esphome_enable="YES"' >> /etc/rc.conf

# Start service
service esphome start

# Stop service
service esphome stop

# Restart service
service esphome restart

# Check status
service esphome status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start esphome
brew services stop esphome
brew services restart esphome

# Check status
brew services list | grep esphome
```

### Windows Service Manager

```powershell
# Start service
net start esphome

# Stop service
net stop esphome

# Using PowerShell
Start-Service esphome
Stop-Service esphome
Restart-Service esphome

# Check status
Get-Service esphome
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream esphome_backend {
    server 127.0.0.1:6052;
}

server {
    listen 80;
    server_name esphome.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name esphome.example.com;

    ssl_certificate /etc/ssl/certs/esphome.example.com.crt;
    ssl_certificate_key /etc/ssl/private/esphome.example.com.key;

    location / {
        proxy_pass http://esphome_backend;
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
    ServerName esphome.example.com
    Redirect permanent / https://esphome.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName esphome.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/esphome.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/esphome.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:6052/
    ProxyPassReverse / http://127.0.0.1:6052/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend esphome_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/esphome.pem
    redirect scheme https if !{ ssl_fc }
    default_backend esphome_backend

backend esphome_backend
    balance roundrobin
    server esphome1 127.0.0.1:6052 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R esphome:esphome /etc/esphome
sudo chmod 750 /etc/esphome

# Configure firewall
sudo firewall-cmd --permanent --add-port=6052/tcp
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
sudo systemctl status esphome

# View logs
sudo journalctl -u esphome -f

# Monitor resource usage
top -p $(pgrep esphome)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/esphome"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/esphome-backup-$DATE.tar.gz" /etc/esphome /var/lib/esphome

echo "Backup completed: $BACKUP_DIR/esphome-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop esphome

# Restore from backup
tar -xzf /backup/esphome/esphome-backup-*.tar.gz -C /

# Start service
sudo systemctl start esphome
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u esphome -n 100
sudo tail -f /var/log/esphome/esphome.log

# Check configuration
esphome --version

# Check permissions
ls -la /etc/esphome
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 6052

# Test connectivity
telnet localhost 6052

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep esphome)

# Check disk I/O
iotop -p $(pgrep esphome)

# Check connections
ss -an | grep 6052
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  esphome:
    image: esphome:latest
    ports:
      - "6052:6052"
    volumes:
      - ./config:/etc/esphome
      - ./data:/var/lib/esphome
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update esphome

# Debian/Ubuntu
sudo apt update && sudo apt upgrade esphome

# Arch Linux
sudo pacman -Syu esphome

# Alpine Linux
apk update && apk upgrade esphome

# openSUSE
sudo zypper update esphome

# FreeBSD
pkg update && pkg upgrade esphome

# Always backup before updates
tar -czf /backup/esphome-pre-update-$(date +%Y%m%d).tar.gz /etc/esphome

# Restart after updates
sudo systemctl restart esphome
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/esphome

# Clean old logs
find /var/log/esphome -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/esphome
```

## Additional Resources

- Official Documentation: https://docs.esphome.org/
- GitHub Repository: https://github.com/esphome/esphome
- Community Forum: https://forum.esphome.org/
- Best Practices Guide: https://docs.esphome.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
