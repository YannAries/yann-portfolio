+++
title = "Deploying apps on VPS with Coolify and Cloudflare Tunnel"
date = "2025-01-08"
description = "Self-hosting with Coolify on Hetzner and Cloudflare Tunnel"
tags = ["cloud", "docker", "ssl", "cloudflare", "hetzner", "coolify"]
categories = ["infrastructure", "tutorials"]
featured = true
+++

Setting up my VPS on Hetzner Cloud to deploy my web app and API took time, especially with SSL certificates and reverse proxy.

## The stack

- **VPS**: Hetzner Cloud (2 vCPU, 4GB RAM, €4.90/month)
- **Platform**: Coolify (self-hosted PaaS)
- **Reverse proxy**: Traefik (via Coolify)
- **SSL/CDN**: Cloudflare Tunnel
- **Apps**: FastAPI backend + Dash/Plotly frontend

## Initial setup

### 1. VPS configuration

After creating a Hetzner Cloud server with Ubuntu 24.04 (provided by Hetzner):

```bash
# Create non-root user
adduser myusername
usermod -aG sudo myusername

# Configure SSH (port 2222)
nano /etc/ssh/sshd_config
# Set: Port 2222, PermitRootLogin no, PasswordAuthentication no
systemctl restart sshd

# Basic firewall
ufw allow 2222/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw --force enable
```

### 2. Installing Coolify

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

Coolify provides:
- Docker-based deployments
- Traefik reverse proxy
- Built-in CI/CD from Git
- Web interface on port 8000

Access via SSH tunnel (safer than exposing port 8000):
```bash
ssh -L 9000:localhost:8000 -p 2222 user@your-server-ip
# Visit: http://localhost:9000
```

### 3. Deploying applications

In Coolify:
1. Create new application from Git repository
2. Configure build settings and environment variables
3. Set domains (initially tried with `https://` but caused issues)
4. Deploy

## The SSL challenge

### Problem: HTTP→HTTPS redirect loop with Traefik

Coolify's Traefik is configured to redirect HTTP to HTTPS and request Let's Encrypt certificates. However:

- Let's Encrypt's HTTP-01 challenge requires HTTP access to `/.well-known/acme-challenge/`
- Traefik immediately redirects to HTTPS with self-signed certificate
- Challenge validation fails → timeout → no SSL

**Failed solutions:**
- Delete `acme.json` and regenerate
- Switch to ZeroSSL
- Manually configure Traefik middlewares
- Disable HTTP redirect (Coolify regenerates it on deploy)

### Solution: Cloudflare Tunnel

Cloudflare Tunnel bypasses the need for origin SSL by creating an encrypted tunnel from server to Cloudflare edge, which then handles SSL for visitors.

**Installation:**

```bash
# Install Cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Authenticate
cloudflared tunnel login
```

**Configuration via Cloudflare dashboard:**

1. Zero Trust → Tunnels → Create tunnel
2. Install connector (already done above)
3. Add public hostnames:
   - `api.mmameetsdata.com` → `http://127.0.0.1:80` (Host header: `api.mmameetsdata.com`)
   - `mmameetsdata.com` → `http://127.0.0.1:80` (Host header: `mmameetsdata.com`)

**Update DNS:**

Replace A records with Cloudflare-managed DNS (nameservers changed to Cloudflare's).

**Update Coolify apps to use HTTP:**

```bash
sudo docker exec coolify-db psql -U coolify -d coolify -c "UPDATE applications SET fqdn = 'http://api.mmameetsdata.com' WHERE name = 'API';"
```

Redeploy apps. Cloudflare Tunnel now handles SSL, Traefik serves HTTP internally.

**Start tunnel service:**

```bash
sudo systemctl enable --now cloudflared
```

## Key lessons

### 1. Origin protocol matters

When using CDN/tunnel solutions:
- Origin must serve HTTP (Cloudflare handles SSL)
- Avoid redirect loops between CDN and origin
- Use `127.0.0.1` instead of `localhost` (IPv6 issues)

### 2. Coolify permissions

After Coolify updates, fix permissions:

```bash
sudo chown -R 9999:9999 /data/coolify
sudo chmod -R 755 /data/coolify
```

Create update script:

```bash
#!/bin/bash
# Backup database
sudo docker exec coolify-db pg_dump -U coolify coolify > ~/coolify-backup-$(date +%Y%m%d).sql

# Fix permissions
sudo chown -R 9999:9999 /data/coolify
sudo chmod -R 755 /data/coolify

# Update
cd /data/coolify/source
sudo docker compose pull
sudo docker compose up -d

# Restart services
sudo docker restart coolify coolify-proxy
sudo systemctl restart cloudflared
```

### 3. Security hardening

**Firewall (Hetzner Cloud console):**
- Inbound: SSH (2222), HTTP (80), HTTPS (443)
- Block: 8000, 8080, 9000 (admin panels)

**Access internal services via SSH tunnel:**
```bash
ssh -L 9000:localhost:8000 -L 9001:localhost:3000 -p 2222 user@server-ip
```

**Disable unnecessary services:**
```bash
sudo systemctl disable --now multipathd open-vm-tools iscsid packagekit
```

**Container resource limits:**
```yaml
API:
  CPUs: 1
  Memory: 512MB (soft: 256MB)
  Swap: 0

Frontend:
  CPUs: 0.8
  Memory: 1GB (soft: 512MB)
  Swap: 0
```

### 4. Prevent cold starts

Use external monitoring to keep apps active:
- **UptimeRobot** (free, checks every 5 min)
- Add health check endpoints
- Configure gunicorn with `--preload` flag

## Troubleshooting tips

**502 errors after deployment:**
- Check container logs: `sudo docker logs <container-id>`
- Check port mappings: `sudo docker ps`
- Test origin directly: `curl -H "Host: domain.com" http://localhost:80`

**522 Cloudflare errors:**
- Tunnel can't reach origin
- Check cloudflared logs: `sudo journalctl -u cloudflared -n 50`
- Verify service is listening: `sudo netstat -tlnp | grep :80`
- Use `127.0.0.1` not `localhost` in tunnel config

**Permission denied on deploy:**
- Run permission fix script
- Restart Coolify: `sudo docker restart coolify`
- Redeploy application

## Final architecture

```
Internet
   ↓
Cloudflare Edge (SSL termination)
   ↓
Cloudflare Tunnel (encrypted)
   ↓
VPS (Hetzner) - Port 80/443
   ↓
Traefik (HTTP routing)
   ↓
Docker Containers (API + Frontend)
```

## Costs

- **VPS**: €4.90/month (Hetzner CPX21)
- **Cloudflare**: Free tier
- **Domain**: ~€10/year
- **Total**: ~€6/month

## Resources

- [Coolify documentation](https://coolify.io/docs)
- [Cloudflare Tunnel guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
- [Hetzner Cloud](https://www.hetzner.com/cloud)

## Conclusion

While self-hosting adds complexity compared to managed platforms, it brings:
- Full control over infrastructure
- Reduced costs at scale
- Learning opportunities
- Data sovereignty

Cloudflare Tunnel solved our SSL challenges elegantly, avoiding the need to fight with Let's Encrypt validation through multiple reverse proxy layers.

The final setup is production-ready, secure, and costs less than a cup of coffee per month.

---

**Live:**
- [mmameetsdata.com](https://mmameetsdata.com) - MMA fighter data tool
- [API Documentation](https://api.mmameetsdata.com/api/docs) - FastAPI backend