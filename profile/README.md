# FocusSpark

FocusSpark is an AI-assisted study platform made of three projects that work together:

- `FocusSpark-Backend`: FastAPI API for authentication, study data, AI tools, focus analysis, settings, notifications, and exports.
- `FocusSpark-Frontend`: Public website and authenticated web dashboard for onboarding, goals, analytics, reports, profile, and settings.
- `FocusSpark-Extension`: Chrome extension study workspace for quick access to Pomodoro sessions, AI tutor, focus tools, flashcards, quizzes, and strict-focus behavior.

The usual local development flow is backend first, then the web frontend, then the Chrome extension.

## Quick Start

1. Install dependencies in each project folder.
2. Create the `.env` files shown in the backend, frontend, and extension READMEs.
3. Start the backend, then start the web frontend, then build and load the extension.

```bash
cd FocusSpark-Backend
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000

cd ..\FocusSpark-Frontend
npm run dev

cd ..\FocusSpark-Extension
npm run build
```

Expected result: the backend health check opens at `http://127.0.0.1:8000/`, the web app opens at `http://localhost:3000`, and the extension can be loaded from `FocusSpark-Extension/build`.

Both the frontend and extension Vite configs use port `3000`, so run only one Vite dev server at a time unless you change one of the ports.

## Clone / First-Time Setup

Clone or open the repository, then install each app's dependencies from its own folder:

```bash
cd FocusSpark-Backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt

cd ..\FocusSpark-Frontend
npm install

cd ..\FocusSpark-Extension
npm install
```

After dependencies are installed, create the `.env` files described below before starting the apps.

## Project Map

```text
FocusSpark/
  FocusSpark-Backend/    FastAPI, PostgreSQL, AI providers, focus analysis, Cloudinary avatars
  FocusSpark-Frontend/   React/Vite web app for public pages and the student dashboard
  FocusSpark-Extension/  React/Vite Chrome Extension Manifest V3 app
```

## Core Features

- Email signup, verification, signin, forgot-password, server-invalidated logout, JWT auth, and protected routes
- Student dashboard with profile, avatar, goals, analytics, achievements, reports, and notifications
- Dashboard bootstrap APIs for the web app and extension to load dashboard, profile, settings, notifications, and achievement popup data with fewer requests
- Cloudinary-backed profile picture upload and display
- AI chat, document chat, generated artifacts, flashcards, and quizzes
- Pomodoro sessions, distractions, emotion logs, focus tracking, and strict-mode behavior
- JSON/CSV exports and browser-generated PDF reports
- Chrome extension workspace with tab/app focus warnings and route locking during focus mode

## Local URLs

- Backend API: `http://127.0.0.1:8000`
- Backend Swagger UI: `http://127.0.0.1:8000/docs`
- Web frontend: `http://localhost:3000`
- Extension Vite dev page: `http://localhost:3000` when run separately
- Docker Compose Adminer: `http://localhost:5555`

## Prerequisites

- Node.js 20 or newer
- npm 10 or newer
- Python 3.11 or newer
- PostgreSQL, or Docker Desktop for the Compose database
- Google Chrome or another Chromium-based browser for extension testing
- Optional service credentials:
  - GitHub Models or Gemini API key for AI features
  - Cloudinary credentials for profile-picture uploads
  - SMTP credentials for real email delivery

## 1. Start The Backend

```bash
cd FocusSpark-Backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

Create `.env` in `FocusSpark-Backend/`. See `FocusSpark-Backend/README.md` for the full example.

For local PostgreSQL, create a database that matches your `DATABASE_URL`, for example `focusspark`.

```bash
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Expected result: Uvicorn reports that it is running on `http://127.0.0.1:8000`, and `GET /` returns `{"message": "FocusSpark Backend Running"}`.

Docker Compose alternative:

```bash
cd FocusSpark-Backend
docker compose up --build
```

## Recommended Startup Order

For a normal local demo:

```bash
cd FocusSpark-Backend
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000

cd ..\FocusSpark-Frontend
npm run dev

cd ..\FocusSpark-Extension
npm run build
```

Then load `FocusSpark-Extension/build` in Chrome. Use `npm run dev` in the extension mainly for extension UI preview while developing.

## 2. Start The Web Frontend

```bash
cd FocusSpark-Frontend
npm install
npm run dev
```

Create `.env` in `FocusSpark-Frontend/`.

```env
VITE_BACKEND_BASE_URL=http://127.0.0.1:8000
```

Open the Vite URL, normally `http://localhost:3000`.

Expected result: Vite prints a local URL and the web app loads in the browser.

## 3. Build And Load The Extension

```bash
cd FocusSpark-Extension
npm install
npm run build
```

Create `.env` in `FocusSpark-Extension/`.

```env
VITE_BACKEND_BASE_URL=http://127.0.0.1:8000
VITE_FRONTEND_BASE_URL=http://localhost:3000
```

Load the extension in Chrome:

1. Open `chrome://extensions`.
2. Turn on Developer mode.
3. Click Load unpacked.
4. Select `FocusSpark-Extension/build`.
5. Reload the extension after each new build.

Expected result: Chrome loads the extension without manifest errors and the FocusSpark extension icon opens the app.

## Common Commands

Backend:

```bash
cd FocusSpark-Backend
python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
python -m compileall app
docker compose up --build
docker compose down
```

Frontend:

```bash
cd FocusSpark-Frontend
npm run dev
npm run build
npm run lint
npm run preview
```

Extension:

```bash
cd FocusSpark-Extension
npm run dev
npm run build
npm run lint
npm run preview
```

## How The Apps Connect

- The frontend and extension both call the backend through `VITE_BACKEND_BASE_URL`.
- The extension opens website-only pages through `VITE_FRONTEND_BASE_URL`.
- Profile-picture uploads happen through the web profile page and backend. The extension reads the resulting `avatar_url`.
- Settings, notifications, goals, reports, achievements, Pomodoro data, and analytics are persisted through the backend.
- Web and extension dashboards use dedicated backend bootstrap routes to reduce duplicate startup requests.
- Strict Mode and Pomodoro focus locking are extension-only behaviors, coordinated with the extension background service worker.

## Demo Checklist

- Backend starts and `GET /` returns the health message.
- Swagger UI opens at `/docs`.
- A user can sign up, verify email, and sign in.
- Web and extension logout invalidate the current backend token and return the user to the logged-out screen.
- Web dashboard loads profile and study data.
- Profile picture upload stores and displays a Cloudinary-backed avatar.
- Goals, reports, analytics, achievements, settings, and notifications work in the web app.
- Extension build loads in Chrome without manifest errors.
- Extension signin works and dashboard data loads.
- Pomodoro focus mode and Strict Mode lock navigation to the tutor workspace.
- Extension notification dropdown reflects backend settings.

## Troubleshooting

- If the frontend cannot reach the API, confirm the backend is running and `VITE_BACKEND_BASE_URL` is set.
- If logout appears to fail, confirm the backend is reachable; the apps still clear local auth state if the logout request cannot complete.
- If port `3000` is busy, stop the other Vite app or run only one of the frontend/extension dev servers at a time.
- If backend startup fails on the database, confirm PostgreSQL is running and the database in `DATABASE_URL` exists.
- If Docker Compose cannot connect to Postgres, use the Compose `db` hostname inside `.env.docker`.
- If profile-picture upload fails, confirm Cloudinary environment variables are configured.
- If emails do not send locally, check SMTP settings. Without SMTP, the backend can log email content instead.

## More Details

Each subproject has its own README with deeper setup notes, routes, scripts, and demo checks:

- `FocusSpark-Backend/README.md`
- `FocusSpark-Frontend/README.md`
- `FocusSpark-Extension/README.md`

Additional project docs:

- `PROJECT_ROUTES.md` for backend, frontend, and extension route details
- `FocusSpark.postman_collection.json` for API testing in Postman
