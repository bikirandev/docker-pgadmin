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
   git clone https://github.com/bikirandev/docker-pgadmin.git
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


## Acknowledgments

- Built with [pgAdmin 4](https://www.pgadmin.org/)
- Containerized using [Docker](https://www.docker.com/)


## 📄 License

MIT License - see the [LICENSE](https://github.com/bikirandev/docker-pgadmin/blob/master/LICENSE) file for details.

## 👨‍💻 Author

**Developed by [Bikiran](https://bikiran.com/)**

- 🌐 Website: [bikiran.com](https://bikiran.com/)
- 📧 Email: [Contact](https://bikiran.com/contact)
- 🐙 GitHub: [@bikirandev](https://github.com/bikirandev)

---

<div align="center">

**Made with ❤️ for the Docker community**

[⭐ Star this repo](https://github.com/bikirandev/docker-pgadmin) • [🐛 Report Bug](https://github.com/bikirandev/docker-pgadmin/issues) • [💡 Request Feature](https://github.com/bikirandev/docker-pgadmin/issues/new)

</div>

---

## 🏢 About Bikiran

**[Bikiran](https://bikiran.com/)** is a software development and cloud infrastructure company founded in 2012, headquartered in Khulna, Bangladesh. With 15,000+ clients and over a decade of experience, Bikiran builds and operates a suite of products spanning domain services, cloud hosting, app deployment, workflow automation, and developer tools.

| SL  | Topic        | Product                                                              | Description                                             |
| --- | ------------ | -------------------------------------------------------------------- | ------------------------------------------------------- |
| 1   | Website      | [Bikiran](https://bikiran.com/)                                      | Main platform — Domain, hosting & cloud services        |
| 2   | Website      | [Edusoft](https://www.edusoft.com.bd/)                               | Education management software for institutions          |
| 3   | Website      | [n8n Clouds](https://n8nclouds.com/)                                 | Managed n8n workflow automation hosting                 |
| 4   | Website      | [Timestamp Zone](https://www.timestamp.zone/)                        | Unix timestamp converter & timezone tool                |
| 5   | Website      | [PDFpi](https://pdfpi.bikiran.com/)                                  | Online PDF processing & manipulation tool               |
| 6   | Website      | [Blog](https://blog.bikiran.com/)                                    | Technical articles, guides & tutorials                  |
| 7   | Website      | [Support](https://support.bikiran.com/)                              | 24/7 customer support portal                            |
| 8   | Website      | [Probackup](https://probackup.bikiran.com/)                          | Automated database backup for SQL, PostgreSQL & MongoDB |
| 9   | Service      | [Domain](https://www.bikiran.com/domain)                             | Domain registration, transfer & DNS management          |
| 10  | Service      | [Hosting](https://www.bikiran.com/services/hosting/web)              | Web, app & email hosting on NVMe SSD                    |
| 11  | Service      | Email & SMS                                                          | Bulk email & SMS notification service                   |
| 12  | npm          | [Chronopick](https://www.npmjs.com/package/@bikiran/chronopick)      | Date & time picker React component                      |
| 13  | npm          | [Rich Editor](https://www.npmjs.com/package/@bikiran/editor)         | WYSIWYG rich text editor for React                      |
| 14  | npm          | [Button](https://www.npmjs.com/package/@bikiran/button)              | Reusable React button component library                 |
| 15  | npm          | [Electron Boilerplate](https://www.npmjs.com/package/create-edx-app) | CLI to scaffold Electron.js project templates           |
| 16  | NuGet        | [Bkash](https://www.nuget.org/packages/Bikiran.Payment.Bkash)        | bKash payment gateway integration for .NET              |
| 17  | NuGet        | [Bikiran Engine](https://www.nuget.org/packages/Bikiran.Engine)      | Core .NET engine library for Bikiran services           |
| 18  | Open Source  | [PDFpi](https://github.com/bikirandev/pdfpi)                         | PDF processing tool — open source                       |
| 29  | Open Source  | [Bikiran Engine](https://github.com/bikirandev/Bikiran.Engine)       | Core .NET engine — open source                          |
| 20  | Open Source  | [Drive CLI](https://github.com/bikirandev/DriveCLI)                  | CLI tool to manage Google Drive from terminal           |
| 21  | Docker       | [Pgsql](https://github.com/bikirandev/docker-pgsql)                  | Docker setup for PostgreSQL                             |
| 22  | Docker       | [n8n](https://github.com/bikirandev/docker-n8n)                      | Docker setup for n8n automation                         |
| 23  | Docker       | [Pgadmin](https://github.com/bikirandev/docker-pgadmin)              | Docker setup for pgAdmin                                |
| 24  | Social Media | [LinkedIn](https://www.linkedin.com/company/bikiran12)               | Bikiran on LinkedIn                                     |
| 25  | Social Media | [Facebook](https://www.facebook.com/bikiran12)                       | Bikiran on Facebook                                     |
| 26  | Social Media | [YouTube](https://www.youtube.com/@bikiranofficial)                  | Bikiran on YouTube                                      |
| 27  | Social Media | [FB n8nClouds](https://www.facebook.com/n8nclouds)                   | n8n Clouds on Facebook                                  |

