# Nginx Reverse Proxy Setup for pgAdmin

This guide explains how to set up Nginx as a reverse proxy for pgAdmin with SSL/TLS support.

## Prerequisites

- Nginx installed on your system
- pgAdmin running via Docker Compose
- Domain name (optional, for production)
- SSL certificate (Let's Encrypt recommended for production)

## Installation

### Windows

Download and install Nginx from [nginx.org](https://nginx.org/en/download.html) or use Chocolatey:

```powershell
choco install nginx
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install nginx
```

### Linux (CentOS/RHEL)

```bash
sudo yum install nginx
```

## Configuration

### Basic Configuration (HTTP Only)

Create a configuration file for pgAdmin:

**Linux**: `/etc/nginx/sites-available/pgadmin.yourdomain.com.conf`  
**Windows**: `C:\nginx\conf\conf.d\pgadmin.yourdomain.com.conf`

```nginx
server {
    listen 80;
    server_name pgadmin.yourdomain.com;  # Change to your domain

    location / {
        proxy_pass http://127.0.0.1:8101;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Timeouts
        proxy_connect_timeout 300s;
        proxy_send_timeout 300s;
        proxy_read_timeout 300s;
    }
}
```

### SSL/TLS Configuration (HTTPS)

For production, always use HTTPS. Here's a configuration with SSL:

```nginx
# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name pgadmin.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name pgadmin.yourdomain.com;

    # SSL Certificate Configuration
    ssl_certificate /path/to/fullchain.pem;
    ssl_certificate_key /path/to/privkey.pem;

    # SSL Security Settings
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';
    ssl_prefer_server_ciphers off;

    # HSTS (Optional but recommended)
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Additional Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    location / {
        proxy_pass http://127.0.0.1:8101;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Host $host;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Timeouts
        proxy_connect_timeout 300s;
        proxy_send_timeout 300s;
        proxy_read_timeout 300s;

        # Buffer settings
        proxy_buffering off;
        proxy_request_buffering off;
    }
}
```

### Update Docker Compose Configuration

When using Nginx as a reverse proxy with SSL, update `docker-compose.yml`:

```yaml
environment:
  PGADMIN_CONFIG_SESSION_COOKIE_SECURE: "True" # Enable secure cookies
  PGADMIN_CONFIG_OVERWRITE_REDIRECT_URI: "True" # Handle redirects properly
```

## Enable Configuration

### Linux

```bash
# Create symbolic link to enable site
sudo ln -s /etc/nginx/sites-available/pgadmin.yourdomain.com.conf /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Windows

```powershell
# Test configuration
nginx -t

# Reload Nginx
nginx -s reload
```

## SSL Certificate Setup

### Using Let's Encrypt (Certbot)

#### Linux

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d pgadmin.yourdomain.com

# Auto-renewal is configured automatically
# Test renewal
sudo certbot renew --dry-run
```

#### Windows

Use [win-acme](https://www.win-acme.com/) or manually obtain certificates from Let's Encrypt.

### Using Self-Signed Certificate (Development Only)

```bash
# Generate self-signed certificate
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/pgadmin-selfsigned.key \
  -out /etc/ssl/certs/pgadmin-selfsigned.crt

# Update nginx configuration with these paths
ssl_certificate /etc/ssl/certs/pgadmin-selfsigned.crt;
ssl_certificate_key /etc/ssl/private/pgadmin-selfsigned.key;
```

## Local Development Setup

For local development without a domain, add to hosts file:

**Windows**: `C:\Windows\System32\drivers\etc\hosts`  
**Linux**: `/etc/hosts`

```
127.0.0.1 pgadmin.yourdomain.com
```

Then access pgAdmin at `http://pgadmin.yourdomain.com`

## Firewall Configuration

### Linux (UFW)

```bash
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

### Linux (firewalld)

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

### Windows Firewall

```powershell
# Allow HTTP
New-NetFirewallRule -DisplayName "Nginx HTTP" -Direction Inbound -Protocol TCP -LocalPort 80 -Action Allow

# Allow HTTPS
New-NetFirewallRule -DisplayName "Nginx HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow
```

## Troubleshooting

### Check Nginx Status

**Linux:**

```bash
sudo systemctl status nginx
```

**Windows:**

```powershell
# Check if Nginx is running
tasklist | findstr nginx
```

### View Nginx Logs

**Linux:**

```bash
# Error log
sudo tail -f /var/log/nginx/error.log

# Access log
sudo tail -f /var/log/nginx/access.log
```

**Windows:**

```powershell
# Logs are typically in C:\nginx\logs\
Get-Content C:\nginx\logs\error.log -Tail 50 -Wait
```

### Common Issues

#### 502 Bad Gateway

- Ensure pgAdmin container is running: `docker compose ps`
- Check if port 8101 is accessible: `netstat -an | findstr :8101`
- Verify proxy_pass URL in Nginx config

#### Connection Refused

- Check if Nginx is running
- Verify firewall rules
- Check SELinux settings (Linux): `sudo setsebool -P httpd_can_network_connect 1`

#### SSL Certificate Errors

- Verify certificate paths in Nginx config
- Check certificate permissions
- Ensure certificate is valid: `openssl x509 -in /path/to/cert.pem -text -noout`

## Performance Optimization

Add these directives to the `http` block in `nginx.conf`:

```nginx
# Gzip compression
gzip on;
gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_types text/plain text/css text/xml text/javascript application/json application/javascript application/xml+rss;

# Connection optimization
keepalive_timeout 65;
keepalive_requests 100;

# Buffer sizes
client_body_buffer_size 128k;
client_max_body_size 100M;  # For large SQL file uploads
proxy_buffers 32 4k;
proxy_buffer_size 8k;
```

## Security Best Practices

1. **Restrict Access by IP** (if applicable):

   ```nginx
   location / {
       allow 192.168.1.0/24;
       deny all;
       proxy_pass http://127.0.0.1:8101;
       # ... rest of proxy settings
   }
   ```

2. **Rate Limiting**:

   ```nginx
   # In http block
   limit_req_zone $binary_remote_addr zone=pgadmin_limit:10m rate=10r/s;

   # In location block
   limit_req zone=pgadmin_limit burst=20 nodelay;
   ```

3. **Hide Nginx Version**:

   ```nginx
   # In http block
   server_tokens off;
   ```

4. **Use Strong SSL Configuration**:
   - Use Mozilla SSL Configuration Generator: https://ssl-config.mozilla.org/

## References

- [Nginx Documentation](https://nginx.org/en/docs/)
- [pgAdmin 4 Documentation](https://www.pgadmin.org/docs/)
- [Let's Encrypt](https://letsencrypt.org/)
- [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org/)
