# Django Authentication and Permissions

## Authentication vs Authorization

**Authentication** = Who are you?

**Authorization** = What are you allowed to do?

Django permissions are part of authorization.

## Users and permissions

A user can receive permissions directly or through groups.

Examples:

```
view_document
add_document
change_document
delete_document
```

Useful checks:

```python
user.has_perm("app.view_document")
user.is_staff
user.is_superuser
```

## Mental model

```
User
 ↓
Groups / Permissions
 ↓
Allowed actions
```

For API projects, authentication identifies the caller while permissions determine which resources or actions that caller can access.
