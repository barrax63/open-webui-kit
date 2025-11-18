# Open WebUI Kit

**Open WebUI Kit** is an open-source template that quickly sets up a local AI environment featuring Open WebUI with PostgreSQL database, pgvector support, and optional Cloudflare Tunnel integration for secure remote access.

### What's included

✅ [**Open WebUI**](https://github.com/open-webui/open-webui) - Feature-rich, self-hosted WebUI for LLMs with support for Ollama, OpenAI APIs, and more

✅ [**PostgreSQL with pgvector**](https://github.com/pgvector/pgvector) - Powerful relational database with vector similarity search capabilities for RAG applications

✅ [**Cloudflared**](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) - Secure reverse proxy to access your Open WebUI instance from anywhere without exposing ports

### What you can build

⭐️ **AI Chat Interface** with support for multiple LLM providers (OpenAI, Ollama, etc.)

⭐️ **RAG Applications** using pgvector for efficient semantic search and document retrieval

⭐️ **Web Search Integration** with Tavily API for real-time information retrieval

⭐️ **Voice Conversations** with Speech-to-Text and Text-to-Speech capabilities

⭐️ **Image Generation** using OpenAI DALL-E or other compatible engines

⭐️ **Private AI Assistant** with secure, self-hosted infrastructure

## Installation

### Prerequisites

- Docker and Docker Compose installed on your system
- (Optional) Cloudflare account for tunnel setup
- (Optional) OpenAI API key for using OpenAI models
- (Optional) Tavily API key for web search functionality

### Running Open WebUI using Docker Compose

#### Standard Setup

```bash
git clone <your-repository-url>
cd <repository-name>
cp .env.example .env # Update the STANDARD CONFIGURATION section inside
docker compose up -d
```

> [!IMPORTANT]
> Make sure to update the following variables in your `.env` file:
> - `POSTGRES_PASSWORD` - Set a strong database password
> - `OPENAI_API_KEY` - Your OpenAI API key (if using OpenAI models)
> - `TAVILY_API_KEY` - Your Tavily API key (if using web search)
> - `HOST` - Your domain name or IP address
> - `TZ` - Your timezone (e.g., `Europe/Berlin`, `America/New_York`)

## ⚡️ Quick start and usage

The Open WebUI Docker Kit is built around a Docker Compose file that's pre-configured with network isolation, security hardening, and persistent storage.

After completing the installation steps above, simply visit <http://localhost:8080/> in your browser.

### First-time Setup

1. **Create Admin Account**: On first visit, you'll be prompted to create an admin account
2. **Configure Models**: Add your preferred LLM providers (OpenAI, Ollama, etc.) in the settings
3. **Set Up RAG**: Configure document embeddings using the built-in pgvector database
4. **Enable Features**: Customize web search, image generation, and voice features as needed

## Containers and access

The kit consists of multiple containers with restricted access for enhanced security.
For more information on how to access the services, please refer to the table below.

| Container      | Version     | Hostname    | Port | Network accessible?             |
|----------------|-------------|-------------|------|---------------------------------|
| `open-webui`   | `main`      | open-webui  | 8080 | From host (without cloudflared) |
| `postgres`     | `pg16`      | postgres    | 5432 | From Docker network only        |
| `cloudflared`  | `latest`    | cloudflared | -/-  | -/-                             |

### Expose Open WebUI through Cloudflare Tunnel (optional)

1. [Create a Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/) and add an HTTP route that points to `http://open-webui:8080`.
2. Copy the generated **tunnel token** into your `.env` file as `CLOUDFLARED_TUNNEL_TOKEN`.
3. Start Cloudflared alongside Open WebUI:

   ```bash
   docker compose --profile cloudflared up -d
   ```

Cloudflared connects directly to Cloudflare's edge using the `token`, so no additional configuration files are required. Your Open WebUI instance will be accessible via your custom domain without exposing any ports.

## Upgrading

### Update all containers to latest versions

```bash
git pull
docker compose pull
docker compose up -d
```

This will:
- Pull the latest code changes
- Download the latest container images
- Recreate containers with new versions
- Preserve all your data in volumes

### Enable auto-updates

You can enable automatic updates by adding a cron job. Run:

```bash
crontab -u <USER> -e
```

Add this entry:

```bash
# Every Sunday at 00:00 run git pull and compose up with latest images
0 0 * * 0 cd /home/<USER>/open-webui-kit && /usr/bin/git pull && /usr/bin/docker compose pull && /usr/bin/docker compose up -d >> /home/<USER>/open-webui-kit/cron.log 2>&1
```

## 👓 Recommended reading

Open WebUI offers extensive documentation and guides for getting the most out of your AI setup.

### Official Documentation

- [Open WebUI Documentation](https://docs.openwebui.com/)
- [Getting Started Guide](https://docs.openwebui.com/getting-started/)
- [Features Overview](https://docs.openwebui.com/features/)
- [API Documentation](https://docs.openwebui.com/api/)

### Key Features

- **Multi-Model Support**: Connect to multiple LLM providers simultaneously
- **RAG (Retrieval Augmented Generation)**: Upload documents and chat with them using pgvector
- **Web Search**: Integrate real-time web search into your conversations
- **Voice Interface**: Speech-to-text and text-to-speech capabilities
- **Image Generation**: Create images using DALL-E or compatible engines
- **User Management**: Multi-user support with role-based access control

### Database & Vector Store

- [pgvector Documentation](https://github.com/pgvector/pgvector)
- [PostgreSQL Best Practices](https://www.postgresql.org/docs/current/index.html)
- [Understanding Vector Databases for AI](https://www.pinecone.io/learn/vector-database/)

## Configuration Guide

### Environment Variables

The `.env.example` file contains all configurable options divided into two sections:

#### Standard Configuration (Required)

- **POSTGRES_PASSWORD**: Database password for PostgreSQL
- **OPENAI_API_KEY**: Your OpenAI API key for GPT models
- **TAVILY_API_KEY**: API key for Tavily web search
- **HOST**: Your domain name or IP address (important for webhooks and OAuth)
- **CLOUDFLARED_TUNNEL_TOKEN**: Cloudflare tunnel token for remote access
- **TZ**: Your timezone (e.g., `Europe/Berlin`)

#### Advanced Configuration (Optional)

- **Database Settings**: Vector DB type, database configuration
- **Web Search**: Enable/disable and configure search engines
- **Audio**: STT/TTS engine and model selection
- **Image Generation**: Enable/disable and configure image generation
- **Security**: Admin access controls, API key authentication
- **RAG Settings**: Embedding engine and model configuration

### Security Features

This setup includes several security hardening measures:

- **No new privileges**: Containers cannot gain additional privileges
- **AppArmor**: Additional security layer for container isolation
- **Capability dropping**: Minimal required capabilities only
- **Read-only filesystems**: Where applicable
- **Network isolation**: Internal network for service communication
- **Resource limits**: CPU and memory constraints to prevent resource exhaustion
- **Health checks**: Automatic container health monitoring

## Tips & tricks

### Accessing PostgreSQL Database

The PostgreSQL database is only accessible from within the Docker network for security. To access it:

```bash
# Connect to the database from the host
docker exec -it postgres psql -U root -d open-webui

# Or use a database client connecting to localhost:5432 (if you expose the port)
```

### Backup and Restore

#### Backup Database

```bash
docker exec postgres pg_dump -U root open-webui > backup.sql
```

#### Restore Database

```bash
cat backup.sql | docker exec -i postgres psql -U root -d open-webui
```

#### Backup Volumes

```bash
docker run --rm -v open-webui_storage:/data -v $(pwd):/backup ubuntu tar czf /backup/open-webui-backup.tar.gz /data
docker run --rm -v postgres_storage:/data -v $(pwd):/backup ubuntu tar czf /backup/postgres-backup.tar.gz /data
```

### Monitoring and Logs

```bash
# View logs for all services
docker compose logs -f

# View logs for specific service
docker compose logs -f open-webui
docker compose logs -f postgres

# Check container health status
docker compose ps
```

### Resource Management

The Docker Compose file includes resource limits to prevent any single container from consuming all system resources:

- **Open WebUI**: Max 2 CPUs, 2GB RAM
- **PostgreSQL**: Max 1 CPU, 1GB RAM
- **Cloudflared**: Max 1 CPU, 1GB RAM

Adjust these limits in `docker-compose.yml` based on your system resources.

### Troubleshooting

#### Open WebUI won't start

```bash
# Check logs
docker compose logs open-webui

# Verify database is healthy
docker compose ps postgres

# Restart services
docker compose restart
```

#### Database connection issues

```bash
# Check database health
docker exec postgres pg_isready -U root -d open-webui

# Verify environment variables
docker compose config
```

#### Cloudflare tunnel not working

```bash
# Check cloudflared logs
docker compose logs cloudflared

# Verify tunnel token is set correctly
docker compose config | grep CLOUDFLARED_TUNNEL_TOKEN
```

## 📜 License

This project is licensed under the Apache License 2.0 - see the LICENSE file for details.

## 💬 Support

### Open WebUI Community

- [GitHub Discussions](https://github.com/open-webui/open-webui/discussions)
- [Discord Server](https://discord.gg/5rJgQTnV4s)
- [GitHub Issues](https://github.com/open-webui/open-webui/issues)

### Getting Help

- **Report Bugs**: Use GitHub Issues to report bugs or problems
- **Feature Requests**: Share your ideas for new features
- **Community Support**: Join Discord for real-time help and discussions
- **Documentation**: Check the official docs for detailed guides

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Acknowledgments

- [Open WebUI](https://github.com/open-webui/open-webui) - The amazing open-source WebUI for LLMs
- [pgvector](https://github.com/pgvector/pgvector) - PostgreSQL extension for vector similarity search
- [Cloudflare](https://www.cloudflare.com/) - Secure tunnel infrastructure
