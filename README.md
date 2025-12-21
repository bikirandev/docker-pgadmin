# Docker pgAdmin Setup

A secure, containerized pgAdmin 4 setup using Docker Compose for local PostgreSQL database management.

## Features

- 🔒 Secure configuration with enhanced cookie protection and CSRF protection
- 🌐 Localhost-only binding for improved security
- 📁 Persistent data storage using Docker volumes
- ⚙️ Environment-based configuration
- 🔄 Auto-restart on failure

## Prerequisites

- Docker
- Docker Compose

## Quick Start

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd docker-pgadmin
   ```

2. **Configure environment variables**

   ```bash
   cp .env.example .env
   ```

   Edit `.env` and set your credentials:

   ```env
   PGADMIN_PORT=8101
   PGADMIN_DEFAULT_EMAIL=your-email@example.com
   PGADMIN_DEFAULT_PASSWORD=your-secure-password
   ```

3. **Start pgAdmin**

   ```bash
   docker compose up -d
   ```

4. **Access pgAdmin**

   Open your browser and navigate to: `http://localhost:8101`

   Login with the credentials you set in the `.env` file.

## Configuration

### Environment Variables

| Variable                   | Description                              | Default  |
| -------------------------- | ---------------------------------------- | -------- |
| `PGADMIN_PORT`             | Port for accessing pgAdmin web interface | `8101`   |
| `PGADMIN_DEFAULT_EMAIL`    | Default admin email for login            | Required |
| `PGADMIN_DEFAULT_PASSWORD` | Default admin password                   | Required |

### Security Settings

The following security features are enabled by default:

- Server mode enabled
- Master password required
- Enhanced cookie protection
- CSRF protection enabled
- Session cookies set to Strict mode
- Localhost-only binding (127.0.0.1)

⚠️ **Note**: `PGADMIN_CONFIG_SESSION_COOKIE_SECURE` is set to `False` for local development. Set it to `True` if using HTTPS.

## Usage

### Start pgAdmin

```bash
docker compose up -d
```

### Stop pgAdmin

```bash
docker compose down
```

### View Logs

```bash
docker compose logs -f pgadmin
```

### Restart pgAdmin

```bash
docker compose restart pgadmin
```

### Remove Everything (including data)

```bash
docker compose down -v
```

## Data Persistence

pgAdmin data is stored in a named Docker volume (`pgadmin_data`) which persists between container restarts. Your server configurations and saved queries will be retained.

## Troubleshooting

### Container keeps restarting

Check the logs:

```bash
docker compose logs pgadmin
```

### Cannot access pgAdmin

Ensure the port is not already in use:

```bash
netstat -an | findstr :8101
```

Change `PGADMIN_PORT` in `.env` if needed, then restart:

```bash
docker compose down
docker compose up -d
```

### Permission issues

The container runs as root user to ensure proper permissions for the data volume.

## Production Deployment

For production use, consider:

1. Setting `PGADMIN_CONFIG_SESSION_COOKIE_SECURE: 'True'` when using HTTPS
2. Using strong, randomly generated passwords
3. Setting up a reverse proxy with SSL/TLS
4. Restricting network access appropriately
5. Regular backups of the `pgadmin_data` volume

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

Kumar Bishojit Paul

## Acknowledgments

- Built with [pgAdmin 4](https://www.pgadmin.org/)
- Containerized using [Docker](https://www.docker.com/)
