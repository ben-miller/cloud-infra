# Nextcloud Setup

This is a Docker-based Nextcloud installation with MariaDB and Redis.

## Quick Start

1. **Configure environment variables** (optional but recommended):
   Edit `.env` and change the default passwords:
   - `MYSQL_ROOT_PASSWORD`
   - `MYSQL_PASSWORD`
   - `NEXTCLOUD_ADMIN_PASSWORD`

2. **Start Nextcloud**:
   ```bash
   docker-compose up -d
   ```

3. **Access Nextcloud**:
   Open http://localhost:8080 in your browser

4. **Login**:
   - Username: `admin` (or what you set in `NEXTCLOUD_ADMIN_USER`)
   - Password: `admin_password_change_me` (or what you set in `NEXTCLOUD_ADMIN_PASSWORD`)

## Installing the Tasks App

1. Once logged in, click your profile icon (top right) → **Apps**
2. Search for "Tasks" in the app store
3. Click **Download and enable** on the "Tasks" app
4. Access Tasks from the top navigation menu

## Managing the Installation

- **Stop Nextcloud**: `docker-compose down`
- **View logs**: `docker-compose logs -f`
- **Restart**: `docker-compose restart`
- **Remove everything** (including data): `docker-compose down -v`

## Data Persistence

Your Nextcloud data is stored in Docker volumes:
- `nextcloud_data`: All Nextcloud files and configuration
- `db_data`: Database files

These persist even when containers are stopped.
