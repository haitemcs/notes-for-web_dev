# Celery with Django

Celery is used to run work outside the normal Django request/response cycle.

Instead of making a user wait while Django performs a slow operation, Django can send a task to Celery. A worker then processes the task in the background.

## Basic architecture

```
Client
  ↓
Django / DRF
  ↓
Celery task
  ↓
Redis (message broker)
  ↓
Celery Worker
  ↓
Task execution
```

## When to use Celery

Celery is useful for work such as:

- Sending emails
- Processing uploaded files
- Generating reports
- Running long calculations
- Calling external APIs
- Scheduled/background jobs
- Retrying failed operations

Do not use Celery just because an operation exists. A normal Django request is usually simpler when the work is fast and does not need to run asynchronously.

## 1. Install dependencies

For a Django project using Redis:

```bash
pip install celery redis
```

Run Redis locally or use a Redis container.

## 2. Create celery.py

Place `celery.py` beside Django's `settings.py`.

Example:

```text
project/
├── manage.py
└── project/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── celery.py
    └── ...
```

```python
import os

from celery import Celery

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project.settings")

app = Celery("project")

app.config_from_object("django.conf:settings", namespace="CELERY")
app.autodiscover_tasks()
```

## 3. Load Celery when Django starts

In `project/__init__.py`:

```python
from .celery import app as celery_app

__all__ = ("celery_app",)
```

This makes the Celery application available when Django starts.

## 4. Configure Redis

In `settings.py`:

```python
CELERY_BROKER_URL = "redis://localhost:6379/0"
CELERY_RESULT_BACKEND = "redis://localhost:6379/1"
```

The broker transports tasks from Django to Celery workers.

The result backend can store task results when result tracking is needed.

For production, keep connection settings in environment variables instead of hard-coding them.

Example:

```python
import os

CELERY_BROKER_URL = os.getenv(
    "CELERY_BROKER_URL",
    "redis://localhost:6379/0",
)

CELERY_RESULT_BACKEND = os.getenv(
    "CELERY_RESULT_BACKEND",
    "redis://localhost:6379/1",
)
```

## 5. Create a task

Inside a Django app, create `tasks.py`:

```python
from celery import shared_task


@shared_task
def add_numbers(a, b):
    return a + b
```

Because the task uses `@shared_task`, Celery can discover it from an installed Django application.

## 6. Start the Celery worker

From the directory containing `manage.py`:

```bash
celery -A project worker --loglevel=INFO
```

Keep the worker running in a separate terminal.

Then Django can submit tasks to it.

## 7. Call the task

Do not call the task like a normal Python function when you want asynchronous execution.

Use:

```python
add_numbers.delay(10, 20)
```

This sends the task to the broker.

The Celery worker receives it and executes it independently of the Django request.

## 8. Understanding delay() and apply_async()

Simple task:

```python
add_numbers.delay(10, 20)
```

More control:

```python
add_numbers.apply_async(
    args=[10, 20],
    countdown=10,
)
```

`delay()` is a shortcut for common asynchronous calls.

`apply_async()` is useful when you need options such as:

- countdown
- ETA
- queue
- retry-related options

## 9. Retries

External services can fail temporarily. Celery can retry a task.

Example:

```python
from celery import shared_task


@shared_task(
    bind=True,
    autoretry_for=(Exception,),
    retry_backoff=True,
    max_retries=3,
)
def call_external_service(self):
    # external API call
    return "success"
```

Retries should be designed carefully. Do not automatically retry every error, especially permanent validation errors.

## 10. Django transactions and Celery

A common problem occurs when a Celery task is triggered before a database transaction has committed.

For example:

```python
from django.db import transaction

transaction.on_commit(
    lambda: process_booking.delay(booking.id)
)
```

This ensures the task is queued only after the database transaction successfully commits.

This is especially important when the Celery task immediately reads data that Django has just created or updated.

## 11. Celery with Docker

A common development architecture is:

```text
                 ┌─────────────┐
                 │   Django    │
                 └──────┬──────┘
                        │
                        ↓
                 ┌─────────────┐
                 │    Redis    │
                 └──────┬──────┘
                        │
                        ↓
                 ┌─────────────┐
                 │   Celery    │
                 │   Worker    │
                 └─────────────┘
```

With Docker Compose, Django, PostgreSQL, Redis, and Celery can run as separate services.

Example service names:

```yaml
services:
  web:
    ...
  db:
    ...
  redis:
    ...
  celery:
    ...
```

The Celery container runs the worker command:

```bash
celery -A project worker --loglevel=INFO
```

Inside Docker, Redis is normally reached through its service name:

```text
redis://redis:6379/0
```

not `localhost`.

## 12. Common mistakes

### Running Celery without a worker

Sending:

```python
task.delay()
```

does not execute the task by itself.

A Celery worker must be running.

### Using localhost incorrectly in Docker

Inside a container, `localhost` refers to that same container.

Use the Docker Compose service name instead.

### Triggering tasks before database commits

Use `transaction.on_commit()` when the task depends on newly committed database data.

### Making everything asynchronous

Celery adds infrastructure and operational complexity.

Use it when asynchronous/background processing provides a real benefit.

## 13. Useful commands

Start Django:

```bash
python manage.py runserver
```

Start a Celery worker:

```bash
celery -A project worker --loglevel=INFO
```

Inspect registered tasks:

```bash
celery -A project inspect registered
```

## Key idea

The main mental model is:

```
Django receives request
        ↓
Create/update database state
        ↓
Queue Celery task
        ↓
Redis transports task
        ↓
Celery worker executes task
        ↓
Store result / update database
```

Celery is therefore not a replacement for Django. It is a background task processing system that works alongside Django.
