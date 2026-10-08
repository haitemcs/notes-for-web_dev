# Frontend ↔ Backend Communication

## Development setup

Typical local setup:

```
React / Vite      http://localhost:5173
Django            http://localhost:8000
```

The frontend sends HTTP requests to the Django API.

## Axios

Axios can be used from React to call the API.

Example:

```javascript
axios.get('/api/notes/')
axios.post('/api/notes/', { text: 'hello' })
```

## Request flow

```
React
  ↓
Axios
  ↓
Vite proxy
  ↓
Django REST API
  ↓
Serializer
  ↓
Django ORM
  ↓
Database
```

The backend returns JSON, which React can use to update the UI.
