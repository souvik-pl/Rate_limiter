# Rate Limiter

A dockerized rate limiting solution using nginx as an API gateway with FastAPI backend service.

## Architecture

```
Client → nginx (API Gateway) → FastAPI Backend
```

- **API Gateway**: nginx with fixed window rate limiting
- **Backend**: FastAPI service
- **Containerization**: Docker Compose orchestration

## Features

- **Rate Limiting**: 30 requests per minute per IP address
- **API Gateway**: nginx reverse proxy with rate limiting
- **Backend Service**: FastAPI with a simple hello endpoint
- **Dockerized**: All services containerized
- **Modern Tooling**: Uses `uv` for Python package management

## Project Structure

```
Rate_limiter/
├── api_gateway/
│   ├── Dockerfile
│   └── nginx.conf          # nginx configuration with rate limiting
├── backend/
│   ├── Dockerfile
│   ├── main.py             # FastAPI application
│   ├── pyproject.toml      # Python dependencies
│   └── .flake8            # Code quality configuration
├── docker-compose.yml      # Service orchestration
└── README.md
```

## Rate Limiting Configuration

The nginx API gateway implements fixed window rate limiting:

- **Type**: Fixed window (30 requests per minute)
- **Scope**: Per IP address
- **Response**: HTTP 429 with JSON error message

### Rate Limiting Details

```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=30r/m;
```

- **Zone**: `api_limit` with 10MB shared memory
- **Rate**: 30 requests per minute
- **Client Identification**: Binary remote address
- **Error Response**: JSON format with proper HTTP 429 status

## Quick Start

### Prerequisites

- Docker Desktop installed

### Running the Application

1. **Build and start all services:**
   ```bash
   docker-compose up --build
   ```

2. **Access the API:**
   - **API Gateway**: http://localhost:8080
   - **Backend Direct**: http://localhost:8000

3. **Stop the services:**
   ```bash
   docker-compose down
   ```

### Docker Cache Management

For troubleshooting build issues or configuration changes not applying:

```bash
# Clean Docker cache (removes unused images, containers, and build cache)
docker system prune -f

# Rebuild without cache (forces fresh build when configuration changes aren't applied)
docker-compose build --no-cache
```

**Purpose:**
- `docker system prune -f`: Cleans up unused Docker resources that can cause build conflicts
- `docker-compose build --no-cache`: Forces complete rebuild without using cached layers

## API Endpoints

### Through API Gateway (Rate Limited)

```bash
curl http://localhost:8080
```

**Response:**
```json
{"message": "Hello"}
```

**Rate Limited Response (429):**
```json
{"error": "Too Many Requests", "message": "Rate limit exceeded"}
```

