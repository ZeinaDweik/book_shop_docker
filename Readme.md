# Book Shop Docker Deployment

This project containerizes the Django Book Shop application using Docker and Docker Compose. SQLite was replaced with PostgreSQL.

## Services

- `db`: PostgreSQL 15 database
- `backend`: Django Book Shop application running with Gunicorn

Nginx configuration was skipped because Step 5 was not required for this submission.

## Requirements

- Docker
- Docker Compose

No Python installation is required on the host machine.

## Setup

Clone the repository:

```bash
git clone https://github.com/ZeinaDweik/book_shop_docker
cd book_shop_docker/book-shop
```

Create a `.env` file based on `.env.example`:

```bash
cp .env.example .env
```

Build the containers:

```bash
docker compose build
```

Start the database:

```bash
docker compose up -d db
```

Run migrations:

```bash
docker compose run --rm backend python manage.py migrate
```

Create an admin user:

```bash
docker compose run --rm backend python manage.py createsuperuser
```

Collect static files:

```bash
docker compose run --rm backend python manage.py collectstatic --noinput
```

Run the application:

```bash
docker compose up --build
```

Open the app:

```text
http://localhost:8000
```

Open the admin page:

```text
http://localhost:8000/admin/
```

## Admin Access

The admin user is not included in the repository.  
After running migrations, create a new admin user using:

```bash
docker compose run --rm backend python manage.py createsuperuser
```

Then log in through:

```text
http://localhost:8000/admin/
```

## Environment Variables

See `.env.example` for the required variables:

- `SECRET_KEY`
- `DEBUG`
- `ALLOWED_HOSTS`
- `POSTGRES_DB`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `POSTGRES_HOST`
- `POSTGRES_PORT`

## Database

The application uses PostgreSQL through Docker Compose.  
Database data is stored in a named Docker volume called `postgres_data`.

## Notes

The real `.env` file is ignored by Git and should not be committed. Only `.env.example` is included as a template.
# Phase 2 — CI/CD Pipelines

This project implements three GitHub Actions CI/CD pipelines for the Dockerized Django Book Shop application.

## Group Size and Registry Choice

Group size: 2 students  
Registry: Docker Hub

This matches the assignment rule that groups of 2 push images to Docker Hub.

## Branch Strategy

The project uses three deployment branches:

| Branch | Philosophy | Deployment |
|---|---|---|
| dev | Artifact-first | Builds an artifact, commits it to `artifacts/`, builds the image from that artifact, and deploys to EC2 |
| test | Image-first | Rebuilds a fresh artifact from source, builds a fresh image, pushes it to Docker Hub, and deploys by pulling the image |
| prod | Promotion only | Does not build anything; it pulls the image version defined by the GitHub repository variable `IMAGE_VERSION` |

## Deployment Ports

All branches deploy to the same EC2 instance, but they do not conflict because each branch uses a different compose project name and host port.

| Environment | Compose Project | Port |
|---|---|---|
| dev | `bookshop_dev` | `8081` |
| test | `bookshop_test` | `8082` |
| prod | `bookshop_prod` | `8083` |

Application URLs:

```text
dev:  http://EC2_HOST:8081
test: http://EC2_HOST:8082
prod: http://EC2_HOST:8083
