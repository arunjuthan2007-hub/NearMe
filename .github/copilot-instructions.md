## Repo Overview

- **Project:** A small Django site that serves an image-map based static site showing places around a city.
- **Layout:** The Django project package is at `arun/` with the app `mapapp/`.
- **Primary static pages:** `arun/mapapp/static/map.html`, `river.html`, `temple.html`, `school.html`, `hotel.html`, `boutique.html`.

## High-level architecture

- Django project with minimal app code. `arun/arun/settings.py` enables static file discovery via:
  - `STATICFILES_DIRS = [BASE_DIR / 'mapapp/static']` (so the `mapapp/static` folder is the source of the site's HTML assets).
- URL config currently only mounts the admin in `arun/arun/urls.py`. The app pages are static files (not Django templates) served by the staticfiles system during development (DEBUG=True).
- Database: `db.sqlite3` exists but there are no app models in `mapapp/models.py` at present.

## What to know before editing

- This repository implements the site as plain HTML files inside `mapapp/static/`. If you need to add dynamic views, prefer moving HTML into a `templates/` folder and use `render()` from `mapapp/views.py`.
- To expose app views via HTTP add a `mapapp/urls.py` and include it from `arun/arun/urls.py`, for example:

  ```py
  # arun/arun/urls.py
  from django.urls import include, path

  urlpatterns = [
      path('admin/', admin.site.urls),
      path('', include('mapapp.urls')),
  ]
  ```

  And `mapapp/urls.py`:

  ```py
  from django.urls import path
  from . import views

  urlpatterns = [
      path('', views.index),
  ]
  ```

## Developer workflow & useful commands

- Create and activate a virtualenv, then install dependencies (recommended Django version referenced in settings comment):

  ```powershell
  cd arun
  python -m venv .venv; .\.venv\Scripts\Activate.ps1
  pip install django==5.2.8
  ```

- Run the development server (serves static files automatically when `DEBUG=True`):

  ```powershell
  cd arun; python manage.py migrate; python manage.py runserver
  ```

- Add static assets: edit files under `arun/mapapp/static/`. Those files are referenced with relative links inside `map.html` (e.g., `href="river.html"`).

## Project-specific patterns & conventions

- Static-first: The project stores full HTML pages in `mapapp/static/` rather than Django templates. Search this directory when looking for UI changes.
- `map.html` uses an HTML image map with `<area>` tags that link to other static HTML files — coordinates are the source of navigation regions. Preserve these coords when editing the base map image (`Screenshot 2025-11-28 113004.png`).
- Minimal app code: `mapapp/views.py`, `models.py`, and `admin.py` are empty stubs. Tests are not present.

## Integration points and gotchas

- Static serving in production: `STATICFILES_DIRS` is set, but for production you must run `collectstatic` and serve the collected static files from a real static host (Nginx, CDN). The current `DEBUG=True` setting is development-only.
- If you convert pages to Django templates, update `TEMPLATES['DIRS']` or use app templates at `mapapp/templates/` and adjust `STATIC_URL` usage accordingly.

## Quick examples (editable tasks)

- Add a dynamic index view that renders `map.html` from templates: move `map.html` to `mapapp/templates/map.html` and use `return render(request, 'map.html')` in a view.
- To add a new point-of-interest page, add `newplace.html` to `mapapp/static/` and add a corresponding `<area href="newplace.html">` entry in `map.html`.

## When you need more context

- Open `arun/arun/settings.py`, `arun/arun/urls.py`, and `arun/mapapp/static/map.html` — these three show the project's routing and static-first UI pattern.
- If you want to introduce models or REST endpoints, prefer adding `mapapp/urls.py` and views and register models in `mapapp/admin.py` so the admin continues to work.

---
If any section is unclear or you want the instructions tailored for a different workflow (e.g., converting to a template-based app or containerizing), tell me which direction and I'll update this file accordingly.
