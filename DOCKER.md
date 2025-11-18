# Docker Setup for SlamSim!

This guide explains how to run SlamSim! using Docker Desktop or any Docker-compatible environment.

## Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop) (recommended) or Docker Engine
- Docker Compose (included with Docker Desktop)

## Quick Start

### 1. Setup Environment Variables

First, copy the environment template file to create your own `.env` file:

```bash
cp .env.template .env
```

Edit the `.env` file with your preferred text editor and configure the following:

- **SECRET_KEY**: Generate a secure secret key for Flask sessions
  ```bash
  # Generate a secure key using Python:
  python -c 'import secrets; print(secrets.token_hex(32))'
  ```

- **API Keys** (Optional): If you plan to use AI-powered match summary generation:
  - `GOOGLE_API_KEY`: Get from [Google AI Studio](https://makersuite.google.com/app/apikey)
  - `OPENAI_API_KEY`: Get from [OpenAI Platform](https://platform.openai.com/api-keys)

### 2. Build and Run with Docker Compose

Start the application using Docker Compose:

```bash
docker-compose up -d
```

This command will:
- Build the Docker image (first time only)
- Create and start the container in detached mode
- Mount volumes for persistent data storage

### 3. Access the Application

Open your web browser and navigate to:
```
http://localhost:5000
```

## Docker Commands

### View Logs
```bash
docker-compose logs -f
```

### Stop the Application
```bash
docker-compose down
```

### Restart the Application
```bash
docker-compose restart
```

### Rebuild After Changes
```bash
docker-compose up -d --build
```

## Alternative: Using Docker without Compose

If you prefer to use Docker directly without Docker Compose:

### Build the Image
```bash
docker build -t slamsim:latest .
```

### Run the Container
```bash
docker run -d \
  --name slamsim \
  -p 5000:5000 \
  -e SECRET_KEY="your-secret-key-here" \
  -v $(pwd)/data:/app/data \
  -v $(pwd)/includes:/app/includes \
  slamsim:latest
```

### Stop and Remove
```bash
docker stop slamsim
docker rm slamsim
```

## Data Persistence

The Docker setup uses volumes to persist your data:

- `./data` - Contains all league data (wrestlers, events, championships, etc.)
- `./includes` - Contains uploaded files (league logos, etc.)

These directories are mounted from your host system, so your data persists even if you remove the container.

## Security Best Practices

### 1. Secret Key
- **Never commit your `.env` file** to version control
- Generate a strong, random SECRET_KEY for production use
- Use a different SECRET_KEY for each environment

### 2. API Keys
- Store API keys only in the `.env` file (never in code)
- Restrict API key permissions to only what's necessary
- Rotate API keys regularly
- Monitor API usage for unusual activity

### 3. Container Security
- The application runs as a non-root user inside the container
- Only necessary ports are exposed
- No sensitive data is included in the Docker image

### 4. Network Security
When deploying to production:
- Use HTTPS/TLS encryption
- Consider using a reverse proxy (nginx, Caddy)
- Implement rate limiting
- Use a firewall to restrict access

## Troubleshooting

### Port Already in Use
If port 5000 is already in use, you can change it in the `.env` file:
```
FLASK_PORT=8080
```

Then access the app at `http://localhost:8080`

### Permission Issues
If you encounter permission issues with volumes:
```bash
# Fix ownership of data directories
sudo chown -R $USER:$USER data includes
```

### Container Won't Start
Check the logs:
```bash
docker-compose logs slamsim
```

### Reset Everything
To completely reset the application and remove all data:
```bash
docker-compose down -v
rm -rf data includes
```

## Environment Variables Reference

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `SECRET_KEY` | Yes* | (dev key) | Flask session secret key |
| `FLASK_ENV` | No | production | Flask environment |
| `FLASK_DEBUG` | No | 0 | Enable debug mode (0 or 1) |
| `FLASK_PORT` | No | 5000 | Port to expose the application |
| `GOOGLE_API_KEY` | No | - | Google Gemini API key for AI features |
| `OPENAI_API_KEY` | No | - | OpenAI API key for AI features |

*A default is provided for development, but you should set a secure key for production.

## Production Deployment

For production deployments, consider:

1. **Use a production WSGI server**: Replace Flask's development server with Gunicorn or uWSGI
2. **Set up a reverse proxy**: Use nginx or Caddy to handle HTTPS and static files
3. **Enable HTTPS**: Use Let's Encrypt for free SSL certificates
4. **Set up backups**: Regularly backup the `data` and `includes` directories
5. **Monitor the application**: Set up logging and monitoring tools
6. **Update regularly**: Keep dependencies and Docker images up to date

## Support

For issues or questions:
- Check the main [README.md](readme.md) for application usage
- Review Docker and Docker Compose documentation
- Open an issue on the project repository
