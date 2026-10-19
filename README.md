# Budget Manager

Budget Manager is a Django server-rendered web application. This repository currently contains only the initial project scaffold and a basic home page; financial features have not been implemented.

## Requirements

- Python 3.12 or later
- Docker Engine with the Docker Compose plugin, for the local PostgreSQL database

## Local Setup

Run these commands from the repository root on Linux or macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
docker compose up -d --wait db
python manage.py migrate
python manage.py runserver
```

Open <http://127.0.0.1:8000/> in a browser. The development server listens on localhost and reloads when Python files change.

Stop the development server with `Ctrl+C`, then stop the database container with:

```bash
docker compose down
```

The PostgreSQL data is kept in a Docker volume when containers are stopped. To remove that local data as well, run `docker compose down --volumes`.

## Checks

With the virtual environment active and PostgreSQL running, execute:

```bash
python manage.py check
python manage.py test
```

## Architecture

The proposed architecture and alternatives are documented in [docs/architecture.md](docs/architecture.md), with decisions recorded in [docs/adr/](docs/adr/). The initial scaffold follows the proposed Django Templates and PostgreSQL approach. Do not use the development settings or server as a production deployment configuration.