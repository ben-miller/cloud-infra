# Traefik - Reverse Proxy & Load Balancer

Traefik is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy. It automatically discovers services and routes traffic based on labels.

## Overview

- **Version**: Traefik v3.0
- **Dashboard URL**: http://traefik.home.local
- **Purpose**: Central reverse proxy for all monitoring services
- **Entry Point**: HTTP on port 80

## Architecture

```
Client Request (*.home.local)
        ↓
    Traefik :80
        ↓
   Docker Provider (auto-discovery)
        ↓
   Route to Container (based on labels)
```

## Features Enabled

- ✅ Docker provider with automatic service discovery
- ✅ Dashboard for monitoring routes and services
- ✅ Health check endpoint at `/ping`
- ✅ Dynamic configuration via file provider
- ✅ Shared monitoring network
- ✅ Access logs (optional)

## Configuration Files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Service definition |
| `traefik.yml` | Static configuration (requires restart to change) |
| `dynamic/` | Dynamic configuration files (auto-reload) |
| `acme/` | TLS certificate storage (for future HTTPS) |

## Quick Start

### 1. Start Traefik

```bash
# From the traefik directory
docker-compose up -d
```

### 2. Verify Status

```bash
# Check container is running
docker ps | grep traefik

# View logs
docker-compose logs -f

# Health check
curl http://localhost/ping
```

### 3. Access Dashboard

Open http://traefik.home.local in your browser (requires DNS setup).

## Adding Services to Traefik

Services are automatically discovered via Docker labels. Add these labels to any service's `docker-compose.yml`:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myservice.rule=Host(`myservice.home.local`)"
  - "traefik.http.routers.myservice.entrypoints=web"
  - "traefik.http.services.myservice.loadbalancer.server.port=3000"
```

**Label Breakdown:**
- `traefik.enable=true` - Expose this service to Traefik
- `rule=Host(...)` - Domain-based routing rule
- `entrypoints=web` - Use HTTP (port 80)
- `server.port=...` - Internal container port

## DNS Setup Required

Traefik uses domain-based routing. You need to configure local DNS to resolve `*.home.local` domains to `127.0.0.1`.

**Option A: /etc/hosts (Simple)**
```bash
sudo nano /etc/hosts
```

Add:
```
127.0.0.1 traefik.home.local
127.0.0.1 homepage.home.local
127.0.0.1 grafana.home.local
127.0.0.1 prometheus.home.local
127.0.0.1 loki.home.local
127.0.0.1 uptime.home.local
```

**Option B: dnsmasq (Recommended - Wildcard DNS)**
See root README.md for dnsmasq setup instructions.

## Troubleshooting

### Dashboard not accessible
```bash
# Check Traefik is running
docker ps | grep traefik

# Check logs for errors
docker-compose logs traefik

# Verify DNS resolves
ping traefik.home.local
```

### Service not appearing in dashboard
```bash
# Verify service has traefik.enable=true label
docker inspect <container> | grep traefik.enable

# Check service is on monitoring-network
docker inspect <container> | grep monitoring-network

# View Traefik logs for discovery events
docker-compose logs -f traefik
```

### Port 80 already in use
```bash
# Find what's using port 80
sudo lsof -i :80

# Stop conflicting service or change Traefik port in docker-compose.yml
```

## Security Notes (Local Environment)

⚠️ **Current Configuration is for LOCAL DEVELOPMENT ONLY**

- Dashboard is exposed without authentication
- HTTP only (no TLS)
- Insecure API access enabled

For production environments:
- Enable HTTPS with Let's Encrypt or custom certificates
- Add authentication middleware for dashboard
- Disable insecure API access
- Configure proper access controls

## Useful Commands

```bash
# Start Traefik
docker-compose up -d

# Stop Traefik
docker-compose down

# Restart after config changes
docker-compose restart

# View logs
docker-compose logs -f

# Check configuration is valid
docker-compose config
```

## Environment Variables

Configured via root `.env` file:

| Variable | Default | Description |
|----------|---------|-------------|
| `TRAEFIK_DASHBOARD_DOMAIN` | traefik.home.local | Dashboard access domain |
| `TZ` | UTC | Container timezone |

## Next Steps

After Traefik is running:
1. Set up DNS resolution (see DNS Setup above)
2. Configure other services with Traefik labels
3. Access services via their domain names instead of ports

## Resources

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Docker Provider Guide](https://doc.traefik.io/traefik/providers/docker/)
- [Routing Configuration](https://doc.traefik.io/traefik/routing/overview/)
