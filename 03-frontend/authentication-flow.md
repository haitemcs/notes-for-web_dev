# Frontend Authentication Flow

Notes from connecting a React frontend to an authenticated Django API.

## Login

A typical JWT flow is:

```
React login form
      ↓
POST /api/token/
      ↓
Django
      ↓
access token + refresh token
```

The access token is then sent with API requests:

```
Authorization: Bearer <access_token>
```

## Expired access token

A common flow is:

```
API request
   ↓
401 Unauthorized
   ↓
refresh token
   ↓
new access token
   ↓
retry original request
```

This is one reason Axios interceptors are useful.

## Security note

Token storage is an important design decision. Browser storage is convenient but has XSS considerations; secure httpOnly cookies require additional CSRF and cookie configuration.
