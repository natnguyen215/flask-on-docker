# Flask on Docker

[![Development Build](https://github.com/natnguyen215/flask-on-docker/actions/workflows/python-app.yml/badge.svg)](https://github.com/natnguyen215/flask-on-docker/actions/workflows/python-app.yml)

A Flask application containerized with Docker using PostgreSQL, Gunicorn, and Nginx. The application supports static files and image uploads.

## Demo

![Flask on Docker demo](demo.gif)

## Running the application

### 1. Clone the repository

```bash
git clone https://github.com/natnguyen215/flask-on-docker.git
cd flask-on-docker
```

### 2. Build and start the production services

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

### 3. Create the database

```bash
docker compose -f docker-compose.prod.yml exec web python manage.py create_db
```

### 4. Open the application

On the server, the application is available at:

```text
http://localhost:1143
```

The upload page is available at:

```text
http://localhost:1143/upload
```

If running the application on the Lambda server, forward the port to your local machine:

```bash
ssh -L 8080:localhost:1143 <username>@lambda
```

Then open:

```text
http://localhost:8080/upload
```

From the web interface:

1. Select an image from your computer.
2. Submit the upload form.
3. Visit `/media/<filename>` to view the uploaded file.

### 5. Verify static files

The example static file is available at:

```text
http://localhost:1143/static/hello.txt
```

or, through the SSH tunnel:

```text
http://localhost:8080/static/hello.txt
```

### 6. Stop the application

```bash
docker compose -f docker-compose.prod.yml down -v
```

## Development

To build and run the development version:

```bash
docker compose up -d --build
```

Create the development database with:

```bash
docker compose exec web python manage.py create_db
```

Stop the development containers with:

```bash
docker compose down -v
```

The GitHub Actions workflow automatically verifies that the development Docker configuration builds successfully.
