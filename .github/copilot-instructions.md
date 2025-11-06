## Repository: Job_Portal_Backend — Quick orientation for AI coding agents

This repo is a small Django REST backend for a jobs/candidates app. The goal of these instructions is to give an AI coding agent the minimal, actionable context needed to edit, extend, and debug code here.

Key facts (big picture)
- Framework: Django 5.2.6 + Django REST Framework (see `requirements.txt`).
- App boundaries: primary app is `jobs/`. Project configuration is under `jobPortal/`.
- Main dataflow: HTTP clients -> DRF API endpoints implemented in `jobs/views.py` -> request data validated/serialized in `jobs/serializers.py` -> persisted via `jobs/models.py` -> stored in default SQLite (`db.sqlite3`).
- Static/media: `jobPortal/settings.py` sets `MEDIA_ROOT` and `STATIC_ROOT` and uses Whitenoise for static serving. CORS is wide-open (`CORS_ALLOW_ALL_ORIGINS = True`), so frontends can call the API directly.

Where to look first
- `README.md` — quick start and common developer commands (migrations, runserver, create superuser).
- `jobPortal/settings.py` — global settings (DB, CORS, MEDIA_ROOT, DEBUG). Adjust here for env-specific changes.
- `jobs/serializers.py`, `jobs/models.py`, `jobs/views.py`, `jobs/urls.py` — primary places to implement/modify API behavior.
- `jobs/dockploy.py` — contains a small deployment helper that calls an external API with an API key in-source; treat this as an integration example and flag secrets.

Project-specific conventions and patterns
- Views use DRF APIView-style classes (example: `jobs/views.py` uses `APIView` and `CandidateSerializer`). Follow that pattern for new endpoints unless a ViewSet is specifically introduced.
- Serializers validate file uploads and candidate fields (see `jobs/serializers.py`). Uploads are saved to `MEDIA_ROOT` configured in `jobPortal/settings.py`.
- Admin wiring: `jobs/admin.py` configures the admin model registration; prefer reusing existing admin patterns when exposing new models.
- Migrations exist under `jobs/migrations/`; run `makemigrations` and `migrate` before running or testing locally.

Developer workflows (concrete commands)
- Local dev (Windows PowerShell):
  - Create venv and install: `python -m venv venv` then `venv\Scripts\activate` and `pip install -r requirements.txt`.
  - Migrate: `python manage.py makemigrations; python manage.py migrate`.
  - Create superuser: `python manage.py createsuperuser`.
  - Run server: `python manage.py runserver` (API base: `http://127.0.0.1:8000/`, admin at `/admin/`).
- Tests: run `python manage.py test` (no special test runner configured).
- Docker: repository includes `Dockerfile` and `docker-compose.yaml` (useful for production-like runs); inspect those files before changing container behavior.

Integration & security notes (what to watch for)
- `jobs/dockploy.py` includes a hard-coded API URL and API key — treat this as a secret leakage and recommend moving to environment variables. When modifying, preserve behavior but suggest an env-driven alternative.
- CORS and DEBUG are permissive; changes to production-facing settings must be guarded behind environment checks.

How to make safe edits (contract)
- Inputs: change files under `jobs/` or `jobPortal/` and ensure migrations are generated for model changes.
- Outputs: code compiles (no syntax errors), migrations created if models changed, and unit tests (if added) pass via `python manage.py test`.
- Error modes: watch for missing imports, settings regressions (e.g., MEDIA_ROOT/STATIC_ROOT), and secret exposure (API keys in source).

Concrete examples to reference in edits
- Follow `jobs/views.py` for APIView structure and response patterns using `CandidateSerializer`.
- Follow `jobs/serializers.py` for file-upload validation and field-level validation rules.
- Check `jobPortal/settings.py` for middleware order (Whitenoise, CORS) and media/static configuration.

If `.github/copilot-instructions.md` already exists: merge by preserving any existing repository-specific rules and adding the above sections; do not overwrite custom policies without confirmation.

Next steps / ask the human
- Tell me if you want stricter rules (linting/tests) added to these instructions or want the agent to automatically create a PR for config changes (e.g., move secrets to env). If there are private CI/CD steps or credentials, provide a secure summary so the agent can reference them.

— End of file
