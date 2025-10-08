# Immich Photos

Self-hosted photo and video management solution.

## Access

- **Web UI**: http://photos.home.local
- **Direct access**: http://localhost:2283

## Services

- **immich-server**: Main web application and API
- **immich-machine-learning**: ML features (face recognition, object detection)
- **postgres**: Database with vector extensions for search
- **redis** (valkey): Caching layer

## Initial Setup

1. Navigate to http://photos.home.local
2. Click "Getting Started"
3. Register admin user account
4. Log in and start uploading photos

## Configuration

Environment variables in `.env`:
- `UPLOAD_LOCATION`: Photo storage directory (./library)
- `DB_DATA_LOCATION`: Postgres data directory (./postgres)
- `TZ`: Timezone (America/Chicago)
- `DB_PASSWORD`: Database password
- `IMMICH_VERSION`: release (or pin to specific version)

## Mobile Apps

- **iOS**: Search "Immich" in App Store
- **Android**: Search "Immich" in Play Store

Server URL: `http://photos.home.local`

## Backup Strategy

**CRITICAL**: The database only contains metadata. Photos are stored in `UPLOAD_LOCATION`.

### What to backup:
1. **Photos** (CRITICAL): `./library/` - Your actual photos and videos
2. **Database** (metadata): `./postgres/` - User data, albums, sharing info
3. **ML cache** (optional): Docker volume `model-cache` - Can be regenerated

### Backup commands:
```bash
# Backup photos and database
tar -czf immich-backup-$(date +%Y%m%d).tar.gz library/ postgres/

# Restore
tar -xzf immich-backup-YYYYMMDD.tar.gz
```

## Management

```bash
# View logs
docker-compose logs -f

# Restart services
docker-compose restart

# Stop services
docker-compose down

# Update to latest version
docker-compose pull
docker-compose up -d
```

## Troubleshooting

### Container health
```bash
docker-compose ps
```

### Check logs
```bash
docker-compose logs -f immich-server
docker-compose logs -f immich-machine-learning
```

### Verify Traefik routing
```bash
curl -H "Host: photos.home.local" http://localhost
```

## Resources

- [Official Documentation](https://docs.immich.app/)
- [GitHub Repository](https://github.com/immich-app/immich)
- [Discord Community](https://discord.gg/immich)

## Notes

- Immich requires port 2283 for the server
- Traefik timeout configured to 600s (10 min) for large uploads
- Machine learning features may take time to process large photo libraries
- Mobile apps require network access to the server
