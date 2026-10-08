# Django Models and Migrations

## Model

A Django model describes data that Django stores in a database table.

```python
class Room(models.Model):
    room_number = models.CharField(max_length=10)
    room_type = models.CharField(max_length=20)
```

Common fields:

- `CharField` → relatively short text
- `TextField` → longer text
- `BooleanField` → True/False
- `DateTimeField` → dates and times
- `ForeignKey` → relationship between models

## Timestamps

```python
created_at = models.DateTimeField(auto_now_add=True)
updated_at = models.DateTimeField(auto_now=True)
```

## Migrations

The normal workflow is:

```
models.py
   ↓
makemigrations
   ↓
migration file
   ↓
migrate
   ↓
database table
```

`makemigrations` creates instructions for changing the database.

`migrate` applies those instructions to the database.
