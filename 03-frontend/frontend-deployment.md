# Frontend Deployment Notes

## Development vs production

Development:

```
React → Vite dev server
Django → runserver
Database → local database
```

Production can look like:

```
Browser
  ↓
Nginx / CDN
  ↓
React static files
  ↓
Django API
  ↓
Gunicorn
  ↓
PostgreSQL
```

## Build

The React application is built into static assets:

```bash
npm run build
```

The production frontend can then be served by a CDN, web server, or Nginx.

The backend API can be hosted separately or behind the same Nginx server.
