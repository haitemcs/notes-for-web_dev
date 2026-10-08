# Schema of the Notes App

Here's the full picture — architecture, data flow, files, and the request/response cycle.

---

## 1. High-Level Architecture

```
┌───────────────────────────────────────────────────────────────────┐
│                          YOUR COMPUTER                            │
│                                                                   │
│   ┌─────────────────────────┐         ┌─────────────────────────┐ │
│   │   FRONTEND (React)      │         │   BACKEND (Django)      │ │
│   │   Vite Dev Server       │         │   Django Dev Server     │ │
│   │                         │         │                         │ │
│   │   http://localhost:5173 │         │  http://localhost:8000  │ │
│   │                         │         │                         │ │
│   │   ┌─────────────────┐   │  HTTP   │   ┌─────────────────┐   │ │
│   │   │   App.jsx       │   │ ──────► │   │   views.py      │   │ │
│   │   │   (UI + axios)  │   │  JSON   │   │   (API logic)   │   │ │
│   │   └─────────────────┘   │ ◄────── │   └────────┬────────┘   │ │
│   │                         │         │            │            │ │
│   │   ┌─────────────────┐   │         │   ┌────────▼────────┐   │ │
│   │   │ vite.config.js  │   │         │   │ serializers.py  │   │ │
│   │   │  (proxy /api)   │   │         │   └────────┬────────┘   │ │
│   │   └─────────────────┘   │         │            │            │ │
│   └─────────────────────────┘         │   ┌────────▼────────┐   │ │
│                                       │   │   models.py     │   │ │
│                                       │   └────────┬────────┘   │ │
│                                       │            │            │ │
│                                       │   ┌────────▼────────┐   │ │
│                                       │   │  SQLite (db)    │   │ │
│                                       │   └─────────────────┘   │ │
│                                       └─────────────────────────┘ │
└───────────────────────────────────────────────────────────────────┘
```

---

## 2. Folder Structure

```
notes-project/
│
├── backend/                        ← Django
│   ├── venv/                       (virtual environment)
│   ├── manage.py
│   ├── db.sqlite3                  (database)
│   │
│   ├── config/                     ← project settings
│   │   ├── settings.py             ⚙  apps, CORS, middleware
│   │   └── urls.py                 🔗 routes → /api/
│   │
│   └── notes/                      ← our app
│       ├── models.py               📦 Note (DB table)
│       ├── serializers.py          🔄 Note ⇄ JSON
│       ├── views.py                🧠 GET / POST logic
│       └── urls.py                 🔗 /notes/ endpoint
│
└── frontend/                       ← React
    ├── package.json
    ├── vite.config.js              ⚙  proxy /api → Django
    │
    └── src/
        └── App.jsx                 🎨 UI + axios calls
```

---

## 3. The Data Flow (Add a Note)

```
   USER               REACT                  VITE PROXY            DJANGO                 DB
    │                   │                        │                    │                   │
    │  types "hello"    │                        │                    │                   │
    │  clicks [Add]     │                        │                    │                   │
    ├──────────────────►│                        │                    │                   │
    │                   │                        │                    │                   │
    │                   │  POST /api/notes/      │                    │                   │
    │                   │  { "text": "hello" }   │                    │                   │
    │                   ├───────────────────────►│                    │                   │
    │                   │                        │  forward to        │                   │
    │                   │                        │  :8000             │                   │
    │                   │                        ├───────────────────►│                   │
    │                   │                        │                    │                   │
    │                   │                        │                    │ validate          │
    │                   │                        │                    │ via serializer    │
    │                   │                        │                    ├──────────────────►│
    │                   │                        │                    │                   │
    │                   │                        │                    │  INSERT row       │
    │                   │                        │                    │◄──────────────────┤
    │                   │                        │                    │                   │
    │                   │                        │  201 Created       │                   │
    │                   │                        │  { id:1, text,.. } │                   │
    │                   │                        │◄───────────────────┤                   │
    │                   │   JSON response        │                    │                   │
    │                   │◄───────────────────────┤                    │                   │
    │                   │                        │                    │                   │
    │                   │  reload list (GET)     │                    │                   │
    │                   ├───────────────────────►├───────────────────►│  SELECT *         │
    │                   │                        │                    ├──────────────────►│
    │                   │                        │                    │◄──────────────────┤
    │                   │◄───────────────────────┤◄───────────────────┤                   │
    │  sees new note    │                        │                    │                   │
    │◄──────────────────┤                        │                    │                   │
```

---

## 4. Endpoint Map

| Method | URL (from React) | Forwards to | Purpose |
|--------|------------------|-------------|---------|
| `GET`  | `/api/notes/`    | `:8000/api/notes/` | List all notes |
| `POST` | `/api/notes/`    | `:8000/api/notes/` | Create a note  |

The proxy in `vite.config.js` is why React can call `/api/...` instead of the full URL.

---

## 5. The Note Entity

```
┌───────────────────────────────────────┐
│              Note                     │
├───────────────────────────────────────┤
│  id          Integer   (PK, auto)     │
│  text        String(200)              │
│  created_at  DateTime  (auto)         │
└───────────────────────────────────────┘
              │
              │  serializer converts
              ▼
        { "id": 1, "text": "hello", "created_at": "2026-09-10T..." }
```

---

## 6. Request/Response in JSON

**POST** `/api/notes/`

Request body:
```json
{ "text": "buy milk" }
```

Response `201 Created`:
```json
{
  "id": 1,
  "text": "buy milk",
  "created_at": "2026-09-10T10:30:00Z"
}
```

**GET** `/api/notes/`

Response `200 OK`:
```json
[
  { "id": 1, "text": "buy milk", "created_at": "2026-09-10T10:30:00Z" },
  { "id": 2, "text": "walk dog", "created_at": "2026-09-10T10:31:00Z" }
]
```

---

## 7. Responsibilities (Who Does What)

```
┌─────────────────────────────────────────────────────────────────┐
│  LAYER                RESPONSIBILITY                            │
├─────────────────────────────────────────────────────────────────┤
│  React App.jsx        render UI, handle user input,             │
│                       send HTTP via axios                       │
├─────────────────────────────────────────────────────────────────┤
│  Vite Proxy           route /api/* → localhost:8000             │
│                       (avoids CORS in dev)                      │
├─────────────────────────────────────────────────────────────────┤
│  urls.py              decide which view handles the URL         │
├─────────────────────────────────────────────────────────────────┤
│  views.py             GET → query DB, POST → save to DB         │
├─────────────────────────────────────────────────────────────────┤
│  serializers.py       convert Model ⇄ JSON, validate input      │
├─────────────────────────────────────────────────────────────────┤
│  models.py            describe the DB table (ORM)               │
├─────────────────────────────────────────────────────────────────┤
│  SQLite               persist the actual data                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. One-Page Mental Model

```
   React                    Django REST
  ┌──────┐    axios        ┌───────────┐
  │ UI   │ ─────JSON────►  │  View     │
  └──────┘ ◄───JSON─────   └─────┬─────┘
                                 │
                           ┌─────▼─────┐
                           │Serializer │
                           └─────┬─────┘
                                 │
                           ┌─────▼─────┐
                           │  Model    │
                           └─────┬─────┘
                                 │
                           ┌─────▼─────┐
                           │  SQLite   │
                           └───────────┘
```

**React thinks in JSON. Django thinks in Python objects. The serializer is the translator between them.**

# Schema — JWT Auth Flow & Production Deployment

---

# PART A — JWT Authentication Flow

## A.1 Concept

Instead of sending username/password on every request, the user logs in **once**, receives two tokens, and sends the **access token** with every subsequent request.

```
┌──────────────────────────────────────────────────────────────┐
│  ACCESS TOKEN   short-lived (~5–60 min)   proves identity    │
│  REFRESH TOKEN  long-lived (~1–30 days)   gets new access    │
└──────────────────────────────────────────────────────────────┘
```

---

## A.2 New Pieces Added

```
backend/
├── config/
│   ├── settings.py         ⚙  add SIMPLE_JWT config
│   └── urls.py             🔗 /api/token/, /api/token/refresh/
└── notes/
    └── views.py            🧠 protect with @permission_classes([IsAuthenticated])

frontend/src/
├── api.js                  🔧 axios instance + interceptors
├── Login.jsx               🎨 login form
└── App.jsx                 🎨 protected UI
```

---

## A.3 Login Flow (First Time)

```
   USER              REACT                DJANGO                  DB
    │                  │                    │                     │
    │  username+pass   │                    │                     │
    ├─────────────────►│                    │                     │
    │                  │ POST /api/token/   │                     │
    │                  │ { username, pass } │                     │
    │                  ├───────────────────►│                     │
    │                  │                    │ verify credentials  │
    │                  │                    ├────────────────────►│
    │                  │                    │◄────────────────────┤
    │                  │                    │                     │
    │                  │  { access, refresh }                     │
    │                  │◄───────────────────┤                     │
    │                  │                    │                     │
    │                  │ store in           │                     │
    │                  │ localStorage       │                     │
    │                  │                    │                     │
    │  redirect to app │                    │                     │
    │◄─────────────────┤                    │                     │
```

---

## A.4 Authenticated Request Flow

```
   REACT                                             DJANGO
    │                                                  │
    │  GET /api/notes/                                 │
    │  Authorization: Bearer <access_token>            │
    ├─────────────────────────────────────────────────►│
    │                                                  │
    │                                       ┌──────────▼──────────┐
    │                                       │ verify token        │
    │                                       │ signature + expiry  │
    │                                       └──────────┬──────────┘
    │                                                  │
    │                                       ┌──────────▼──────────┐
    │                                       │ identify user       │
    │                                       │ from token payload  │
    │                                       └──────────┬──────────┘
    │                                                  │
    │  200 OK  [ ...notes... ]                         │
    │◄─────────────────────────────────────────────────┤
```

---

## A.5 Token Expiry & Refresh Flow

```
    │  GET /api/notes/  (access token expired)
    ├─────────────────────────────────────────────────►│
    │                                                  │
    │  401 Unauthorized                                │
    │◄─────────────────────────────────────────────────┤
    │                                                  │
    │  axios interceptor catches 401                   │
    │         │                                        │
    │         ▼                                        │
    │  POST /api/token/refresh/                        │
    │  { refresh: <refresh_token> }                    │
    ├─────────────────────────────────────────────────►│
    │                                                  │
    │  200 OK  { access: <new_access> }                │
    │◄─────────────────────────────────────────────────┤
    │                                                  │
    │  retry original request with new token           │
    ├─────────────────────────────────────────────────►│
    │                                                  │
    │  200 OK  [ ...notes... ]                         │
    │◄─────────────────────────────────────────────────┤
```

---

## A.6 Full Auth State Machine

```
                ┌────────────────┐
                │   NOT LOGGED   │
                └───────┬────────┘
                        │ POST /api/token/
                        │ (valid creds)
                        ▼
                ┌────────────────┐
        ┌──────►│   LOGGED IN    │
        │       │  has access    │
        │       │  + refresh     │
        │       └───────┬────────┘
        │               │
        │  401 on any   │ access expires
        │  request      │
        │               ▼
        │       ┌────────────────┐
        │       │  REFRESHING    │
        │       │ POST /token/   │
        │       │   refresh/     │
        │       └───────┬────────┘
        │               │
        │      success  │  failure (refresh expired)
        └───────────────┤
                        ▼
                ┌────────────────┐
                │  LOGGED OUT    │
                │ (clear tokens) │
                └────────────────┘
```

---

## A.7 Where Tokens Are Stored

```
┌───────────────────────────────────────────────────────────┐
│  localStorage          simple, XSS-risk                   │
│  sessionStorage        cleared on tab close, XSS-risk     │
│  httpOnly cookie       safest (CSRF risk, needs setup)    │
└───────────────────────────────────────────────────────────┘
```

For a tiny project → `localStorage` is fine.
For production apps → httpOnly cookies.

---

## A.8 Axios Interceptor Logic

```
┌─────────────────────────────────────────────────────────────┐
│  REQUEST INTERCEPTOR                                        │
│    for every outgoing request:                              │
│        if localStorage has access → add header              │
│            Authorization: Bearer <access>                   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  RESPONSE INTERCEPTOR                                       │
│    if response.status == 401:                               │
│        call /api/token/refresh/                             │
│        if success → retry original request                  │
│        if failure → clear storage, redirect to /login       │
└─────────────────────────────────────────────────────────────┘
```

---

# PART B — Production Deployment

## B.1 Two Deployment Models

```
┌──────────────────────────────────────────────────────────────────────┐
│  MODEL 1 — SEPARATE HOSTING (most common)                            │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────┐              ┌──────────────────────────┐  │
│   │  React SPA          │              │  Django API              │  │
│   │  Netlify / Vercel   │  HTTPS       │  Railway / Render / VPS  │  │
│   │  myapp.com          │─────────────►│  api.myapp.com           │  │
│   └─────────────────────┘              └────────────┬─────────────┘  │
│                                                     │                │
│                                            ┌────────▼─────────────┐  │
│                                            │  PostgreSQL          │  │
│                                            └──────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│  MODEL 2 — SINGLE SERVER (simplest)                                  │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────────────────────────────────┐    │
│   │  Nginx                                                      │    │
│   │   /             → serve React build/ (static files)         │    │
│   │   /api/*        → proxy to Gunicorn → Django                │    │
│   │   /static/*     → Django static files                       │    │
│   └───────────────────────────┬─────────────────────────────────┘    │
│                               │                                       │
│                    ┌──────────▼───────────┐                          │
│                    │  Gunicorn (Django)   │                          │
│                    └──────────┬───────────┘                          │
│                               │                                       │
│                    ┌──────────▼───────────┐                          │
│                    │  PostgreSQL          │                          │
│                    └──────────────────────┘                          │
└──────────────────────────────────────────────────────────────────────┘
```

---

## B.2 Model 1 — Full Request Path

```
   BROWSER
     │
     │  https://myapp.com
     ▼
┌──────────────┐
│  CDN         │  serves React static files (fast, cached)
│  Netlify     │
└──────┬───────┘
       │
       │  React bundle runs in browser
       │
       │  fetch('https://api.myapp.com/api/notes/')
       ▼
┌──────────────┐
│  Load         │  TLS termination
│  Balancer     │
└──────┬───────┘
       ▼
┌──────────────┐
│  Nginx       │  static + proxy
└──────┬───────┘
       ▼
┌──────────────┐
│  Gunicorn    │  runs Django (multiple workers)
└──────┬───────┘
       ▼
┌──────────────┐
│  Django app  │
└──────┬───────┘
       ▼
┌──────────────┐
│  PostgreSQL  │
└──────────────┘
```

---

## B.3 Model 2 — Single Server Path

```
   BROWSER
     │
     │  https://myapp.com
     ▼
┌─────────────────────────────────────────────┐
│  Nginx  (port 80/443)                       │
│                                             │
│  location /          → /var/www/react/      │
│  location /api/      → proxy_pass 127.0.0.1:8000
│  location /static/   → Django static dir    │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │  Gunicorn            │
        │  port 8000 (local)   │
        └──────────┬───────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │  Django              │
        └──────────┬───────────┘
                   │
                   ▼
        ┌──────────────────────┐
        │  PostgreSQL          │
        └──────────────────────┘
```

---

## B.4 Build & Deploy Pipeline

```
   DEVELOPER
      │
      │  git push origin main
      ▼
┌─────────────────┐
│  GitHub         │
└────────┬────────┘
         │
         │  webhook
         ▼
┌──────────────────────────┐
│  CI/CD (GitHub Actions)  │
│                          │
│  1. run tests            │
│  2. build React          │   npm run build
│  3. collect Django statics│  python manage.py collectstatic
│  4. deploy               │
└────────┬─────────────────┘
         │
         ├──────────────►  Frontend host (Netlify/Vercel)
         │                  uploads dist/
         │
         └──────────────►  Backend host (Railway/Render/VPS)
                            restarts Gunicorn
```

---

## B.5 Environment Variables

```
┌───────────────────────────────────────────────────────────┐
│  FRONTEND (.env.production)                               │
│    VITE_API_URL = https://api.myapp.com                   │
├───────────────────────────────────────────────────────────┤
│  BACKEND (.env)                                           │
│    SECRET_KEY     = <random long string>                  │
│    DEBUG          = False                                 │
│    DATABASE_URL   = postgres://user:pass@host:5432/db     │
│    ALLOWED_HOSTS  = api.myapp.com                         │
│    CORS_ALLOWED_ORIGINS = https://myapp.com               │
└───────────────────────────────────────────────────────────┘
```

Rule: **never commit `.env`.** Add it to `.gitignore`.

---

## B.6 Settings Diff — Dev vs Prod

```
┌─────────────────────┬──────────────────────┬──────────────────────┐
│  SETTING            │  DEVELOPMENT         │  PRODUCTION          │
├─────────────────────┼──────────────────────┼──────────────────────┤
│  DEBUG              │  True                │  False               │
│  ALLOWED_HOSTS      │  []                  │  ["api.myapp.com"]   │
│  Database           │  SQLite              │  PostgreSQL          │
│  Static files       │  served by Django    │  Nginx / WhiteNoise  │
│  Server             │  runserver           │  Gunicorn + Nginx    │
│  HTTPS              │  no                  │  yes, forced         │
│  CORS origins       │  localhost:5173      │  myapp.com only      │
│  SECRET_KEY         │  hardcoded (unsafe)  │  env var (random)    │
│  Proxy              │  Vite proxy          │  real domain         │
│  Tokens             │  localStorage        │  httpOnly cookies    │
└─────────────────────┴──────────────────────┴──────────────────────┘
```

---

## B.7 Production Security Checklist

```
┌──────────────────────────────────────────────────────────────┐
│  ✔  DEBUG = False                                            │
│  ✔  SECRET_KEY from env, long & random                       │
│  ✔  ALLOWED_HOSTS restricted                                 │
│  ✔  CORS only from trusted origins                           │
│  ✔  HTTPS everywhere (Let's Encrypt)                         │
│  ✔  SECURE_SSL_REDIRECT = True                               │
│  ✔  SESSION_COOKIE_SECURE = True                             │
│  ✔  CSRF_COOKIE_SECURE = True                                │
│  ✔  HSTS header enabled                                      │
│  ✔  Rate-limit login endpoint                                │
│  ✔  Database backups scheduled                               │
│  ✔  Logs monitored                                           │
└──────────────────────────────────────────────────────────────┘
```

---

## B.8 Combined Big Picture

```
                        ┌─────────────────────┐
                        │      USERS          │
                        └──────────┬──────────┘
                                   │
                        ┌──────────▼──────────┐
                        │   HTTPS (TLS)       │
                        └──────────┬──────────┘
                                   │
             ┌─────────────────────┴─────────────────────┐
             │                                           │
             ▼                                           ▼
   ┌───────────────────┐                    ┌───────────────────────┐
   │  React SPA        │                    │  Django REST API      │
   │  (Netlify / CDN)  │                    │  (Gunicorn + Nginx)   │
   │                   │                    │                       │
   │  - UI             │   fetch + JWT      │  - Views              │
   │  - axios          │───────────────────►│  - Serializers        │
   │  - router         │◄───────────────────│  - JWT auth           │
   └───────────────────┘      JSON          │  - Business logic     │
                                           └───────────┬───────────┘
                                                       │
                                                       ▼
                                           ┌───────────────────────┐
                                           │   PostgreSQL          │
                                           │   (+ backups)         │
                                           └───────────────────────┘
```

---

## Quick Recap Table

| Aspect | Development | Production |
|--------|-------------|-----------|
| React runs on | Vite dev server `:5173` | Static files on CDN/Nginx |
| Django runs on | `runserver :8000` | Gunicorn + Nginx |
| Communication | Vite proxy | Real domain, CORS config |
| Database | SQLite | PostgreSQL |
| Auth | localStorage JWT | httpOnly cookie JWT |
| Env | hardcoded | env vars |
| HTTPS | no | yes |

That's the full picture — **auth** and **deploy**. Want me to turn either of these into actual code (JWT setup in the tiny project, or a Docker Compose file for production)?