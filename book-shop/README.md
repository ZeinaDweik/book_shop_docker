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