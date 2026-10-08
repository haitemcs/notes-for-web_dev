# Django ORM and QuerySets

Notes from working with Django models and database queries.

## QuerySet

A QuerySet represents a collection of database objects.

```python
User.objects.all()
User.objects.filter(is_staff=True)
User.objects.exclude(is_superuser=True)
```

## Field lookups

Django uses double underscores to describe conditions.

```name__icontains="ethan"
name__startswith="Eth"
age__gt=18
age__gte=18
age__lt=18
age__lte=18
```

The idea is:

```
field __ condition = value
  ↑          ↑
what?       how?
```

## Common operations

- `filter()` → select matching objects
- `exclude()` → remove matching objects
- `get()` → retrieve one object
- `first()` → get the first result
- `all()` → retrieve all objects

## Mental model

Python ORM code → Django generates SQL → PostgreSQL/SQLite executes it → Django returns Python objects.
