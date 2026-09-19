# Install & create the project :
cd notes-project
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate

pip install django djangorestframework django-cors-headers

django-admin startproject config backend
cd backend
python manage.py startapp notes

![alt text](image-1.png)

notes-project/
├── venv/                       # Virtual environment files
└── backend/                    # Root project folder
    ├── manage.py               # Command-line utility for running tasks
    ├── config/                 # Project settings and root routing
    │   ├── settings.py         # Global settings (database, installed apps, middleware)
    │   ├── urls.py             # Root URL routing table
    │   ├── wsgi.py / asgi.py   # Deployment web server gateways
    │   └── __init__.py
    └── notes/                  # Your application module
        ├── models.py           # Database tables
        ├── views.py            # Business logic / API endpoints
        ├── admin.py            # Admin panel configuration
        ├── apps.py             # App-level config
        └── migrations/         # Database schema changes













REGISTER THE APP — backend/config/settings.py


INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',      # add
    'corsheaders',         # add
    'notes',               # add
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',   # add at the top
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# add at the bottom
CORS_ALLOWED_ORIGINS = ["http://localhost:5173"]

    5173 is Vite's default port.


EXPLANATION :

1. INSTALLED_APPS (Registering Components)

Django operates on a modular "apps" system. Adding your installed packages and custom app here activates their models, admin interface integration, and internal configurations.

    'rest_framework': Plugs in Django REST Framework (DRF). This gives you access to API features like JSON responses, interactive API documentation views, and request parsers.

    'corsheaders': Registers the third-party library responsible for managing cross-origin security rules.

    'notes': Connects the notes app you created with startapp. This tells Django to track its database models, migrations, and templates.

2. MIDDLEWARE (Request Processing Pipeline)

Middleware acts as a series of hooks or "checkpoints" that every HTTP request and response must pass through before reaching your views or returning to the browser.

    'corsheaders.middleware.CorsMiddleware': Placed at the very top so it can inspect every incoming request before any other processing happens. It checks whether the request originates from a permitted origin (like a React or Vue frontend) and attaches the appropriate HTTP headers (like Access-Control-Allow-Origin) to the outgoing response.

        Why at the top? If placed lower down, an authentication or security middleware might reject a cross-origin request before CorsMiddleware gets a chance to attach the permitted origin headers.

3. CORS_ALLOWED_ORIGINS (Cross-Origin Authorization)

Browsers enforce a safety standard called the Same-Origin Policy, which blocks frontend web applications on one domain/port (e.g., http://localhost:5173) from making API calls to a backend running on another (e.g., http://localhost:8000).

    "http://localhost:5173": Specifically whitelists your Vite frontend dev server. When the browser sends an API request, Django will attach headers saying, "Yes, http://localhost:5173 is trusted and allowed to read this data."








THE MODEL : — backend/notes/models.py


from django.db import models

class Note(models.Model):
    text = models.CharField(max_length=200)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.text



┌───────────────────────────────────────┐
│              Note                     │
├───────────────────────────────────────┤
│  id          Integer   (PK, auto)     │
│  text        String(200)              │
│  created_at  DateTime  (auto)         │
└───────────────────────────────────────┘











Serializer :



The NoteSerializer class bridges your Python database models and standard format data like JSON. It acts as a translator and validation engine for your API.
Key Concepts & Components
Python

from rest_framework import serializers
from .models import Note

    serializers module: Django REST Framework (DRF) provides built-in tools to convert complex data structures (like database objects) into standard Python data types that can easily be rendered into JSON or XML.

    Note model: Importing the database structure you defined so the serializer knows what fields exist and what rules they follow.

Python

class NoteSerializer(serializers.ModelSerializer):

    serializers.ModelSerializer: A specialized parent class that acts as a shortcut. Instead of manually writing validation rules and field mappings for every database column, ModelSerializer automatically inspects your Note model and builds appropriate fields, defaults, and validation logic for you.

Python

    class Meta:
        model = Note
        fields = ['id', 'text', 'created_at']

    class Meta: An inner configuration class used to specify metadata options for the serializer.

    model = Note: Specifies which database model this serializer is configured to map.

    fields = [...]: Defines an explicit whitelist of attributes to convert and expose over the network:

        id: Automatically generated primary key in Django. Included so the frontend can identify specific records (e.g., when editing or deleting a specific note).

        text: The text contents of your note.

        created_at: The timestamp recording when the entry was created.

Core Responsibilities: Two-Way Data Processing

A Django serializer handles data flow in both directions:
1. Serialization (Outgoing: Database → JSON)

When a frontend application requests a list of notes via a GET request:

    The view fetches Note instances from the SQL database using Django ORM.

    The serializer converts those complex Django model instances into standard Python dictionaries.

    DRF renders those dictionaries into a JSON string sent to the browser:

JSON

{
  "id": 1,
  "text": "Buy groceries",
  "created_at": "2026-09-10T14:30:00Z"
}

2. Deserialization & Validation (Incoming: JSON → Database)

When a user submits a new note from the frontend via a POST request:

    The frontend sends raw JSON data to your API endpoint.

    The serializer parses the JSON payloads into Python data types.

    It validates the data against model constraints (e.g., ensuring text is not null or over length limits).

    Calling serializer.is_valid() checks data safety; calling serializer.save() creates or updates the record directly in the database.