# Django REST API Basics

My notes while moving from Django pages toward API-based applications.

## Basic flow

```
Client
  ↓ HTTP request
URL
  ↓
View
  ↓
Serializer
  ↓
Model / ORM
  ↓
Database
```

The API normally receives and returns JSON.

## Typical operations

```
GET    /api/notes/       → retrieve data
POST   /api/notes/       → create data
PUT    /api/notes/1/     → replace data
PATCH  /api/notes/1/     → partially update data
DELETE /api/notes/1/     → delete data
```

## Why serializers?

Serializers sit between Python/Django objects and JSON.

They also provide input validation before data reaches the database.

## Project connection

I use this pattern in my Django projects, including the Booking-App and the async task-processing project.
