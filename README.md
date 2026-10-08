# Web Development Notes

My personal notes, references, and learning material while studying web development and backend engineering.

The notes stay practical: I write down concepts when I learn them, problems I run into, and things I use while building projects.

## Structure

### 01 — Git & GitHub
- [Git Clone](01-git-github/git-clone.md)
- [Updating a Repository](01-git-github/update-repository.md)

### 02 — Django

My main backend learning area.

#### Django fundamentals
- [Model Fields and Options](02-django/model-fields-and-options.md)
- [Models and Migrations](02-django/models-migrations.md)
- [Creating and Saving Data](02-django/creating-and-saving-data.md)
- [ORM and QuerySets](02-django/orm-and-querysets.md)

#### APIs and authentication
- [REST API Basics](02-django/rest-api-basics.md)
- [Authentication and Permissions](02-django/authentication-and-permissions.md)
- [Django Permissions](02-django/django-permissions.md)

#### Databases and projects
- [Django + PostgreSQL](02-django/django-postgresql.md)
- [Project From Scratch](02-django/project-from-scratch.md)
- [Django AI Agent Notes](02-django/django-ai-agent-notes.md)

### 03 — Frontend

Notes related to using React as the frontend for my Django APIs.

- [React and Vite Basics](03-frontend/react-vite-basics.md)
- [Vite Frontend Notes](03-frontend/vite-frontend-notes.md)
- [Frontend ↔ Backend Communication](03-frontend/frontend-backend-communication.md)
- [Running Backend and Frontend](03-frontend/run-backend-and-frontend.md)
- [Frontend Authentication Flow](03-frontend/authentication-flow.md)
- [Frontend Deployment](03-frontend/frontend-deployment.md)

Workflow:

```
React → Axios → Vite Proxy → Django REST API → PostgreSQL
```

### 04 — Projects
- [Notes App Architecture](04-projects/notes-app-architecture.md)

### 05 — Tools
- [OpenAI in VS Code](05-tools/openai-vscode.md)

### 06 — ML Notes

Machine-learning notes connected to projects I am actually building.

#### BTC volatility forecasting
- [BTC Volatility Project](06-ml-notes/btc-volatility-project.md)
- [Feature Engineering for Volatility](06-ml-notes/feature-engineering-for-volatility.md)
- [Time-Series Validation](06-ml-notes/time-series-validation.md)
- [Model Evaluation and Failure Analysis](06-ml-notes/model-evaluation-and-failure-analysis.md)
- [ML Model Selection](06-ml-notes/ml-model-selection.md)
- [Model Evaluation Notes](06-ml-notes/model-evaluation-notes.md)

## Learning Progress

This repository is intentionally a work in progress.

I add notes when I:
- learn a new concept
- make a mistake and understand why
- solve a problem in a project
- discover a better way to implement something

Current direction:

**Django → Django REST Framework → PostgreSQL → React → Docker → Backend Engineering → ML/AI integration**

## Related Projects

The notes are connected to my practical work, especially:
- Django Booking-App
- Django Async Task Processing Platform
- BTC Volatility Forecasting

The goal is to show the progression from learning a concept to actually applying it in projects.
