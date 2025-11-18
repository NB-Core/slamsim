# Quick Start Guide - Docker

## 1. Install Docker Desktop
Download from: https://www.docker.com/products/docker-desktop

## 2. Clone and Setup
```bash
git clone https://github.com/NB-Core/slamsim.git
cd slamsim
cp .env.template .env
```

## 3. Configure (Important!)
Edit `.env` and set your `SECRET_KEY`:
```bash
# Generate a secure key:
python -c 'import secrets; print(secrets.token_hex(32))'
# Copy the output and paste it in .env
```

## 4. Run
```bash
docker-compose up -d
```

## 5. Access
Open http://localhost:5000 in your browser

## Common Commands
```bash
# View logs
docker-compose logs -f

# Stop
docker-compose down

# Restart
docker-compose restart

# Rebuild after code changes
docker-compose up -d --build
```

## Help
For detailed instructions, see [DOCKER.md](DOCKER.md)
