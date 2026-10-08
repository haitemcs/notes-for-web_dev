# React and Vite Basics

These are my notes from building a small React frontend for a Django backend.

## Vite

Vite provides the frontend development server and build tooling.

Creating a React project:

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
npm install axios
```

Run it with:

```bash
npm run dev
```

## vite.config.js

The Vite configuration can define a development proxy so the frontend can communicate with Django through `/api`.

```javascript
server: {
  proxy: {
    '/api': {
      target: 'http://localhost:8000',
    },
  },
}
```

The important idea is:

```
React → /api/... → Vite proxy → Django :8000
```
