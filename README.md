# Cloud Infrastructure - Local Monitoring Stack

A complete local monitoring and observability stack using Docker, Traefik, and modern DevOps tools.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Traefik Reverse Proxy                     │
│              (*.home.local → Internal Services)              │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐   ┌────────▼────────┐   ┌───────▼────────┐
│   Homepage     │   │   Prometheus    │   │    Grafana     │
│   Dashboard    │   │   (Metrics)     │   │ (Visualization)│
└────────────────┘   └─────────────────┘   └────────────────┘
                              │                     │
                     ┌────────┼────────┐           │
                     │                 │           │
            ┌────────▼───────┐  ┌──────▼──────┐   │
            │ Node Exporter  │  │  cAdvisor   │   │
            │ (Host Metrics) │  │ (Container) │   │
            └────────────────┘  └─────────────┘   │
                                                   │
┌──────────────────┐   ┌─────────────────┐       │
│      Loki        │◄──│    Promtail     │       │
│  (Log Storage)   │   │ (Log Collector) │       │
└────────┬─────────┘   └─────────────────┘       │
         └──────────────────────────────────────►┘

┌─────────────────────────────────────────────────────────────┐
│                     Uptime Kuma                              │
│              (Service Health Monitoring)                     │
└─────────────────────────────────────────────────────────────┘
```

## Services

| Service | URL | Purpose |
|---------|-----|---------|
| Traefik | http://traefik.home.local | Reverse proxy & routing |
| Homepage | http://homepage.home.local | Unified dashboard |
| Prometheus | http://prometheus.home.local | Metrics collection |
| Grafana | http://grafana.home.local | Metrics visualization |
| Loki | http://loki.home.local | Log aggregation |
| Uptime Kuma | http://uptime.home.local | Uptime monitoring |

## Directory Structure

```
.
├── traefik/           # Reverse proxy & routing
├── homepage/          # Dashboard & service directory
├── prometheus/        # Metrics collection
├── grafana/          # Visualization & dashboards
├── loki/             # Log aggregation
├── promtail/         # Log shipping
├── uptime-kuma/      # Uptime monitoring
├── docker-compose.yml # Shared network definition
├── .env.example      # Environment template
└── README.md         # This file
```

## Prerequisites

- Docker & Docker Compose
- Local DNS resolution (see DNS Setup below)

## DNS Setup

You need to configure local DNS resolution for `*.home.local` domains. Choose one option:

### Option A: /etc/hosts (Simple)

Edit `/etc/hosts` (macOS/Linux) or `C:\Windows\System32\drivers\etc\hosts` (Windows):

```
127.0.0.1 traefik.home.local
127.0.0.1 homepage.home.local
127.0.0.1 prometheus.home.local
127.0.0.1 grafana.home.local
127.0.0.1 loki.home.local
127.0.0.1 uptime.home.local
```

### Option B: dnsmasq (Wildcard DNS)

Automatically resolves all `*.home.local` to `127.0.0.1`. See `dnsmasq/README.md` for setup.

## Quick Start

1. **Clone and setup environment:**
   ```bash
   git clone <repo-url>
   cd cloud-infra
   cp .env.example .env
   ```

2. **Create shared Docker network:**
   ```bash
   docker-compose up -d
   ```

3. **Start services in order:**
   ```bash
   # Start reverse proxy first
   cd traefik && docker-compose up -d && cd ..

   # Start monitoring core
   cd prometheus && docker-compose up -d && cd ..
   cd loki && docker-compose up -d && cd ..
   cd promtail && docker-compose up -d && cd ..

   # Start visualization
   cd grafana && docker-compose up -d && cd ..

   # Start dashboard & uptime monitoring
   cd homepage && docker-compose up -d && cd ..
   cd uptime-kuma && docker-compose up -d && cd ..
   ```

4. **Access the dashboard:**
   ```bash
   open http://homepage.home.local
   ```

## Common Commands

```bash
# View all containers
docker ps

# View logs for a service
cd <service-dir>
docker-compose logs -f

# Restart a service
cd <service-dir>
docker-compose restart

# Stop all services
docker stop $(docker ps -q)

# Remove all services
docker-compose down
```

## Backup & Recovery

See `MAINTENANCE.md` for backup procedures.

## Security Notes

- This stack is designed for **local development only**
- Default credentials should be changed for any non-local deployment
- No TLS/HTTPS configured (add via Traefik for production)
- Docker socket mounted read-only where possible

## Monitoring Features

### Metrics Collection
- Host system metrics (CPU, memory, disk, network)
- Container metrics (per-container resource usage)
- Service health checks
- Custom application metrics

### Log Aggregation
- Centralized log collection from all containers
- Log parsing and labeling
- Full-text search in Grafana

### Visualization
- Pre-built Grafana dashboards
- Real-time metrics
- Historical data retention (configurable)

### Alerting
- Uptime Kuma for service availability
- Prometheus alerting rules (optional)
- Notification channels (optional)

## Contributing

See TODO.md for the implementation roadmap.

## License

MIT
