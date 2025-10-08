# Monitoring & Observability Setup - Todo List

## Phase 1: Repository Structure & Foundation

### 1.1 Initialize Main Repository
- [x] Create root `.gitignore` (ignore secrets, logs, data volumes)
- [x] Create root `README.md` with architecture overview
- [x] Create `.env.example` for shared environment variables
- [x] Create `docker-compose.yml` in root for network definition
- [x] Define shared Docker network (e.g., `monitoring-network`)
- [x] Add local domain suffix to `.env` (e.g., `DOMAIN=local`)

### 1.2 Create Subdirectories for Each Service
- [x] Create service directories:
```
├── traefik/
├── homepage/
├── prometheus/
├── grafana/
├── loki/
├── promtail/
├── uptime-kuma/
└── immich/
```

---

## Phase 2: Traefik Setup (Reverse Proxy & DNS)

### 2.1 Traefik Core Configuration
- [x] Create `traefik/` directory
- [x] Create `traefik/docker-compose.yml`
- [x] Create `traefik/traefik.yml` (static configuration)
- [x] Create `traefik/dynamic/` directory for dynamic configs
- [x] Create `traefik/acme/` directory for certificates (if using HTTPS)
- [x] Create `traefik/README.md`
- [x] Enable Docker provider
- [x] Enable dashboard on `traefik.home.local`
- [x] Configure entry points (http on :80)
- [x] Set up access logs (optional - config included)

### 2.2 Local DNS Setup (Choose One Method)

**Option A: dnsmasq (Recommended - Automatic wildcard DNS)**
- [ ] Create `dnsmasq/` directory
- [ ] Create `dnsmasq/docker-compose.yml`
- [ ] Create `dnsmasq/dnsmasq.conf` with:
  ```
  address=/home.local/127.0.0.1
  ```
- [ ] Configure system DNS to use 127.0.0.1 as primary DNS
- [ ] Configure dnsmasq to forward other queries to 8.8.8.8
- [ ] Test: `dig homepage.home.local` should resolve to 127.0.0.1
- [ ] Update all service configs to use `.home.local` TLD
- [ ] Create `dnsmasq/README.md` with OS-specific DNS config instructions

**Option B: Manual /etc/hosts (Simple but requires editing per service)**
- [ ] Add entries to `/etc/hosts` (macOS/Linux) or `C:\Windows\System32\drivers\etc\hosts` (Windows):
  ```
  127.0.0.1 traefik.home.local
  127.0.0.1 homepage.home.local
  127.0.0.1 grafana.home.local
  127.0.0.1 prometheus.home.local
  127.0.0.1 loki.home.local
  127.0.0.1 uptime.home.local
  127.0.0.1 photos.home.local
  ```
- [ ] Document hosts file changes in root README

**Option C: Pi-hole (Full-featured DNS + Ad-blocking)**
- [ ] Create `pihole/` directory  
- [ ] Create `pihole/docker-compose.yml`
- [ ] Set up Pi-hole web interface
- [ ] Add local DNS records for each service in Pi-hole UI
- [ ] Configure system DNS to use 127.0.0.1
- [ ] Access Pi-hole at `http://pihole.home.local/admin`

### 2.3 Traefik Testing
- [ ] Start Traefik: `cd traefik && docker-compose up -d`
- [ ] Verify Traefik dashboard at `http://traefik.local`
- [ ] Check Docker provider is detecting containers
- [ ] Verify Traefik is on the monitoring network

---

## Phase 3: Homepage Setup

### 2.1 Homepage Configuration
- [ ] Create `homepage/` directory
- [ ] Create `homepage/docker-compose.yml`
- [ ] Add Traefik labels to docker-compose for `homepage.local`
- [ ] Create `homepage/config/` directory
- [ ] Create `homepage/config/services.yaml` (service definitions)
- [ ] Create `homepage/config/settings.yaml` (general settings)
- [ ] Create `homepage/config/bookmarks.yaml` (useful links)
- [ ] Create `homepage/config/widgets.yaml` (dashboard widgets)
- [ ] Create `homepage/README.md` with setup instructions
- [ ] Configure homepage to show service status
- [ ] Set up Docker socket access (read-only) for container stats
- [ ] Update service URLs to use `.local` domains
- [ ] Test homepage loads at `http://homepage.local`

---

## Phase 4: Prometheus Setup

### 3.1 Prometheus Core
- [ ] Create `prometheus/` directory
- [ ] Create `prometheus/docker-compose.yml`
- [ ] Add Traefik labels for `prometheus.local`
- [ ] Create `prometheus/prometheus.yml` (main config)
- [ ] Create `prometheus/rules/` directory for alerting rules
- [ ] Create `prometheus/data/` directory (add to .gitignore)
- [ ] Configure data retention policy
- [ ] Configure scrape intervals (15s default)
- [ ] Create `prometheus/README.md`

### 3.2 Prometheus Targets
- [ ] Add self-scraping job (Prometheus metrics)
- [ ] Add node-exporter job (system metrics)
- [ ] Add cadvisor job (container metrics)
- [ ] Add blackbox-exporter job (endpoint monitoring)
- [ ] Add Traefik metrics scraping
- [ ] Configure service discovery if needed
- [ ] Test Prometheus UI at `http://prometheus.local`

### 3.3 Prometheus Exporters
- [ ] Set up node-exporter in `prometheus/docker-compose.yml`
- [ ] Set up cadvisor for Docker metrics
- [ ] Set up blackbox-exporter for HTTP probes
- [ ] Document each exporter's purpose

---

## Phase 5: Grafana Setup

### 4.1 Grafana Core
- [ ] Create `grafana/` directory
- [ ] Create `grafana/docker-compose.yml`
- [ ] Add Traefik labels for `grafana.local`
- [ ] Create `grafana/provisioning/datasources/` directory
- [ ] Create `grafana/provisioning/dashboards/` directory
- [ ] Create `grafana/dashboards/` for JSON dashboard files
- [ ] Create `grafana/data/` directory (add to .gitignore)
- [ ] Create `grafana/grafana.ini` for custom config
- [ ] Configure root_url for `grafana.local`
- [ ] Create `grafana/README.md`

### 4.2 Grafana Configuration
- [ ] Provision Prometheus datasource (use `http://prometheus:9090`)
- [ ] Provision Loki datasource (use `http://loki:3100`)
- [ ] Set default admin password via environment variable
- [ ] Configure anonymous access (disable for production)
- [ ] Set up dashboard provisioning
- [ ] Test Grafana UI at `http://grafana.local`

### 4.3 Grafana Dashboards
- [ ] Import Node Exporter Full dashboard (ID: 1860)
- [ ] Import Docker monitoring dashboard (ID: 893)
- [ ] Import Prometheus Stats dashboard (ID: 2)
- [ ] Import Traefik dashboard (ID: 4475 or 11462)
- [ ] Create custom overview dashboard
- [ ] Document dashboard IDs in README

---

## Phase 6: Loki & Promtail Setup

### 5.1 Loki Configuration
- [ ] Create `loki/` directory
- [ ] Create `loki/docker-compose.yml`
- [ ] Add Traefik labels for `loki.local` (optional, API only)
- [ ] Create `loki/loki-config.yml`
- [ ] Configure storage (filesystem backend for local)
- [ ] Configure retention period (default 30d)
- [ ] Create `loki/data/` directory (add to .gitignore)
- [ ] Create `loki/README.md`
- [ ] Test Loki API at `http://loki.local` or `http://loki:3100`

### 5.2 Promtail Configuration
- [ ] Create `promtail/` directory
- [ ] Create `promtail/docker-compose.yml`
- [ ] Create `promtail/promtail-config.yml`
- [ ] Configure Docker log scraping
- [ ] Configure system log scraping (`/var/log`)
- [ ] Set up log labels (container_name, job, etc.)
- [ ] Create `promtail/README.md`
- [ ] Verify logs flowing to Loki via Grafana

---

## Phase 7: Uptime Kuma Setup

### 6.1 Uptime Kuma Configuration
- [ ] Create `uptime-kuma/` directory
- [ ] Create `uptime-kuma/docker-compose.yml`
- [ ] Add Traefik labels for `uptime.local`
- [ ] Create `uptime-kuma/data/` directory (add to .gitignore)
- [ ] Create `uptime-kuma/README.md`
- [ ] Test Uptime Kuma UI at `http://uptime.local`

### 6.2 Uptime Kuma Monitors
- [ ] Add HTTP monitor for Homepage (`http://homepage.local`)
- [ ] Add HTTP monitor for Prometheus (`http://prometheus.local`)
- [ ] Add HTTP monitor for Grafana (`http://grafana.local`)
- [ ] Add HTTP monitor for Loki (`http://loki.local`)
- [ ] Add HTTP monitor for Traefik (`http://traefik.local`)
- [ ] Configure notification channels (optional)
- [ ] Set check intervals (60s recommended)

---

## Phase 8: Immich Photos Setup

### 7.1 Immich Directory Setup
- [ ] Create `immich/` directory
- [ ] Download official docker-compose.yml from Immich releases
- [ ] Download example .env file from Immich releases
- [ ] Create `immich/README.md`

### 7.2 Immich Configuration
- [ ] Configure `.env` file:
  - [ ] Set `UPLOAD_LOCATION` for photo storage (e.g., `./library`)
  - [ ] Set `DB_DATA_LOCATION` for postgres data (e.g., `./postgres`)
  - [ ] Set `TZ` timezone variable
  - [ ] Set `IMMICH_VERSION=release`
  - [ ] Set `DB_PASSWORD` (use only A-Za-z0-9 characters)
- [ ] Add upload location and postgres data to `.gitignore`
- [ ] Update Traefik timeout settings in `traefik/traefik.yml`:
  - [ ] Set readTimeout to 600s (10 min for large uploads)
  - [ ] Set idleTimeout to 600s
  - [ ] Set writeTimeout to 600s

### 7.3 Immich Docker Compose Integration
- [ ] Add Traefik labels to immich-server service:
  - [ ] `traefik.enable=true`
  - [ ] `traefik.http.routers.immich.rule=Host(\`photos.home.local\`)`
  - [ ] `traefik.http.routers.immich.entrypoints=web`
  - [ ] `traefik.http.services.immich.loadbalancer.server.port=2283`
- [ ] Add immich-server to monitoring-network (external network)
- [ ] Verify services in docker-compose.yml:
  - [ ] immich-server (main web service)
  - [ ] immich-machine-learning (ML features)
  - [ ] redis (valkey/redis for caching)
  - [ ] database (postgres with vector extensions)
- [ ] Verify volumes are properly configured:
  - [ ] Upload location mounted to /data
  - [ ] Postgres data persisted
  - [ ] Model cache for ML service

### 7.4 Immich Deployment & Testing
- [ ] Start Immich stack: `cd immich && docker-compose up -d`
- [ ] Verify all 4 containers are healthy
- [ ] Access Immich at `http://photos.home.local`
- [ ] Complete initial setup wizard
- [ ] Register admin user account
- [ ] Test photo upload functionality
- [ ] Verify ML service is processing images

### 7.5 Immich Integration with Monitoring Stack
- [ ] Add Immich to Homepage dashboard at `http://photos.home.local`
- [ ] Add Uptime Kuma HTTP monitor for `http://photos.home.local`
- [ ] Configure Uptime Kuma check interval (60s recommended)
- [ ] Document mobile app connection in `immich/README.md`

### 7.6 Immich Backup Strategy
- [ ] Document backup requirements in `immich/README.md`:
  - [ ] Photos stored in `${UPLOAD_LOCATION}` (critical - manual backup required)
  - [ ] Postgres database in `${DB_DATA_LOCATION}` (user metadata)
  - [ ] Machine learning model cache (can be regenerated)
- [ ] Add backup reminder: Database contains only metadata, photos are the source of truth
- [ ] Test restore procedure for both photos and database

---

## Phase 9: Integration & Documentation

### 7.1 Service Integration
- [ ] Add all services to Homepage dashboard with `.local` URLs
- [ ] Configure Homepage widgets for Prometheus
- [ ] Configure Homepage widgets for Grafana
- [ ] Test all service links from Homepage
- [ ] Verify metrics flowing: Prometheus → Grafana
- [ ] Verify logs flowing: Promtail → Loki → Grafana
- [ ] Verify Traefik routing working for all services
- [ ] Check Traefik dashboard shows all routes

### 7.2 Root Documentation
- [ ] Update root `README.md` with architecture diagram
- [ ] Document all service URLs with `.local` domains
- [ ] Document hosts file setup instructions
- [ ] Create `SETUP.md` with initial setup steps
- [ ] Create `MAINTENANCE.md` with common tasks
- [ ] Document backup strategy
- [ ] Document upgrade procedure for each service
- [ ] Add Traefik label examples for future services

### 7.3 Best Practices Implementation
- [ ] Add health checks to all docker-compose files
- [ ] Configure restart policies (unless-stopped)
- [ ] Set resource limits where appropriate
- [ ] Use named volumes for persistent data
- [ ] Document exposed ports clearly (note: Traefik handles routing)
- [ ] Add labels for organization
- [ ] Use `.env` files for configuration (not hardcoded)
- [ ] Ensure all services connect to monitoring network
- [ ] Add Traefik labels consistently across services

---

## Phase 9: Testing & Validation

### 8.1 Functionality Tests
- [ ] Start all services: `docker-compose up -d` from each directory
- [ ] Check all containers are healthy
- [ ] Verify Homepage shows all services at `http://homepage.local`
- [ ] Verify Prometheus scraping all targets at `http://prometheus.local`
- [ ] Verify Grafana shows live metrics at `http://grafana.local`
- [ ] Verify Loki receiving logs
- [ ] Verify Uptime Kuma showing green status at `http://uptime.local`
- [ ] Check Traefik dashboard at `http://traefik.local`
- [ ] Test stopping a service and check alerts
- [ ] Verify no port conflicts (all routing through Traefik)

### 8.2 GitOps Validation
- [ ] Each service can be started independently
- [ ] Each service has proper `.gitignore`
- [ ] No secrets committed to git
- [ ] All configs are in version control
- [ ] READMEs are complete and accurate
- [ ] `.env.example` files are present

---

## Phase 10: Backup & Recovery

### 9.1 Backup Strategy
- [ ] Document which volumes need backing up
- [ ] Create backup script for Grafana dashboards
- [ ] Create backup script for Prometheus data (optional)
- [ ] Create backup script for Uptime Kuma config
- [ ] Test restore procedure
- [ ] Add backup instructions to `MAINTENANCE.md`

---

## Quick Start Commands Reference

```bash
# Start all services
docker-compose up -d

# Start individual service
cd homepage && docker-compose up -d

# View logs
docker-compose logs -f [service-name]

# Stop all services
docker-compose down

# Rebuild after config changes
docker-compose up -d --force-recreate
```

---

## Port Mapping

**With Traefik (Recommended):**
| Service | Domain | Internal Port |
|---------|--------|---------------|
| Traefik Dashboard | http://traefik.local | 8080 |
| Homepage | http://homepage.local | 3000 |
| Grafana | http://grafana.local | 3000 |
| Uptime Kuma | http://uptime.local | 3001 |
| Prometheus | http://prometheus.local | 9090 |
| Loki | http://loki.local | 3100 |
| Immich Photos | http://photos.home.local | 2283 |

**Without Traefik (fallback):**
| Service | Port | URL |
|---------|------|-----|
| Homepage | 3000 | http://localhost:3000 |
| Grafana | 3001 | http://localhost:3001 |
| Uptime Kuma | 3002 | http://localhost:3002 |
| Prometheus | 9090 | http://localhost:9090 |
| Loki | 3100 | http://localhost:3100 |
| Node Exporter | 9100 | http://localhost:9100 |
| cAdvisor | 8080 | http://localhost:8080 |

---

## Traefik Labels Example

Add these labels to your `docker-compose.yml` files:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.service-name.rule=Host(`service.local`)"
  - "traefik.http.routers.service-name.entrypoints=web"
  - "traefik.http.services.service-name.loadbalancer.server.port=INTERNAL_PORT"
```

---

## Notes

- Start with Homepage first - it's your dashboard
- Prometheus + Node Exporter should be set up together
- Grafana depends on Prometheus and Loki
- Promtail depends on Loki
- Each service should be independently startable
- Use `docker-compose logs -f` liberally for debugging
- Keep `.env.example` updated as you add variables
