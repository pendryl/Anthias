# Anthias AGENT Instructions

This repository contains the source for **Anthias**, an open source digital signage solution for Raspberry Pi and PC devices.  The application is primarily a Django project packaged into several Docker containers.  Most Python code lives under `anthias_app`, `anthias_django`, and `viewer`.

### Building for balenaOS

Deployments for Raspberry Pi devices running balenaOS are performed using the script `./bin/deploy_to_balena.sh`.  Example usage:

```bash
./bin/deploy_to_balena.sh --board pi4 --fleet <YourFleetName>
```

This script prepares the balena release (via `balena.yml` and `docker-compose.balena.yml.tmpl`) and pushes it using the balena CLI.  Ensure you have logged in with `balena login` beforehand.

### Development Notes

* Python style and linting is enforced with **ruff**.  Install dev dependencies and run:

```bash
poetry install --only=dev-host
poetry run ruff check .
```

* Unit tests run inside Docker using `docker-compose.test.yml`.  See `docs/developer-documentation.md` for the full workflow.  A short version:

```bash
poetry run python -m tools.image_builder --dockerfiles-only --disable-cache-mounts \
  --service celery --service redis --service test

docker compose -f docker-compose.test.yml up -d --build
docker compose -f docker-compose.test.yml exec anthias-test ./manage.py test
```

* Front-end sources are CoffeeScript/SCSS and compiled through Webpack inside the `anthias-server` container.  Use `npm run dev` from inside that container when modifying those files.

### File layout tips

Some configuration files are generated from templates (e.g. files under `docker-compose.*.yml.tmpl`).  Names might therefore not match the final files used at runtime.  When editing build or deployment logic always inspect the template and any scripts under `bin/` that generate them.

For an overview of other coding conventions see the documents in `.cursor/rules/`.

