# Flask Image Upload Service

[![Development Build](https://github.com/natnguyen215/flask-on-docker/actions/workflows/python-app.yml/badge.svg)](https://github.com/natnguyen215/flask-on-docker/actions/workflows/python-app.yml)

A containerized Flask web application for uploading and viewing images. The project provides a reproducible local development environment using Docker Compose, with Flask serving the application and PostgreSQL providing persistent data storage. Its development build is verified automatically with GitHub Actions on every push and pull request.

## Demo

The following demo shows the application running, an image being uploaded, and the uploaded image being viewed.

![Flask image upload demonstration](docs/demo.gif)

<!--
GIF placeholder:
Record the completed demonstration and save it as:

    docs/demo.gif

The recording should show:
1. Starting or opening the application.
2. Selecting and uploading an image.
3. Navigating to the uploaded image and viewing it.
-->

## Features

- Upload images through a browser-based interface
- View uploaded images from the application
- Flask development server with debug mode enabled
- PostgreSQL-backed development environment
- Reproducible setup through Docker Compose
- Automated development-image builds with GitHub Actions

## Technology Stack

- **Python / Flask** — web application and request handling
- **PostgreSQL** — application database
- **Docker** — isolated application containers
- **Docker Compose** — local service orchestration
- **GitHub Actions** — automated build verification

## Prerequisites

Install the following tools before starting:

- [Git](https://git-scm.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) or Docker Engine with the Compose plugin

Confirm that Docker and Docker Compose are available:

```bash
docker --version
docker compose version
```

## Build Instructions

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPOSITORY.git
cd YOUR_REPOSITORY
```

### 2. Create the development environment file

Create a file named `.env.dev` in the repository root:

```bash
cat > .env.dev <<'EOF'
FLASK_APP=project/__init__.py
FLASK_DEBUG=1
DATABASE_URL=postgresql://hello_flask:hello_flask@db:5432/hello_flask_dev
SQL_HOST=db
SQL_PORT=5432
DATABASE=postgres
APP_FOLDER=/usr/src/app
EOF
```

The development environment file contains local-only configuration and should not contain production credentials.

### 3. Build and start the services

```bash
docker compose -f docker-compose.yml up --build
```

To run the services in the background instead:

```bash
docker compose -f docker-compose.yml up --build -d
```

Docker Compose will build the application image, create the required network, and start the Flask and PostgreSQL services.

### 4. Open the application

After the services have started, visit:

[http://localhost:5000](http://localhost:5000)

From the web interface:

1. Select an image from your computer.
2. Submit the upload form.
3. Open or follow the displayed image link to view the uploaded file.

### 5. Verify the services

Check that all containers are running:

```bash
docker compose -f docker-compose.yml ps
```

Follow application logs:

```bash
docker compose -f docker-compose.yml logs -f
```

To follow only the Flask service, replace `web` below if the application service has a different name in `docker-compose.yml`:

```bash
docker compose -f docker-compose.yml logs -f web
```

### 6. Stop the application

Stop and remove the running containers:

```bash
docker compose -f docker-compose.yml down
```

To also remove development volumes and reset persisted database data:

```bash
docker compose -f docker-compose.yml down --volumes
```

## Development Workflow

Rebuild the application after changing dependencies or Docker configuration:

```bash
docker compose -f docker-compose.yml up --build
```

Open a shell inside the Flask container:

```bash
docker compose -f docker-compose.yml exec web sh
```

Inspect the database logs:

```bash
docker compose -f docker-compose.yml logs db
```

## Continuous Integration

The repository uses GitHub Actions to verify that the development services can be built successfully. The workflow runs for every push and pull request and performs the following steps:

1. Checks out the repository.
2. Creates the development environment file.
3. Builds the Docker Compose services.

The workflow is defined in:

```text
.github/workflows/python-app.yml
```

A successful workflow run confirms that the development container images can be assembled from a clean GitHub-hosted runner.

## Project Structure

```text
.
├── .github/
│   └── workflows/
│       └── python-app.yml
├── docs/
│   └── demo.gif
├── project/
│   └── __init__.py
├── .env.dev
├── docker-compose.yml
└── README.md
```

Additional templates, static assets, database models, and upload-handling modules may be located beneath the `project/` directory.

## Troubleshooting

### The application is not available

Confirm that the containers are running:

```bash
docker compose ps
```

Then inspect their logs:

```bash
docker compose logs
```

### Port 5000 is already in use

Stop the process currently using port `5000`, or change the host-side port mapping in `docker-compose.yml`.

### The application cannot connect to PostgreSQL

Make sure the database container is healthy and that `SQL_HOST` remains set to the Compose service name:

```env
SQL_HOST=db
SQL_PORT=5432
```

Inside Docker Compose, the application should connect to `db` rather than `localhost`.

### Start again with a clean database

```bash
docker compose down --volumes
docker compose up --build
```

> This removes local database data stored in Docker volumes.

## Security Notes

The credentials in `.env.dev` are intended only for local development. Production deployments should use unique secrets, disable Flask debug mode, validate uploaded files, enforce upload-size limits, and store sensitive configuration in a secure secret-management system.

