# Django-LABS

A collection of Django tutorials and learning exercises for the Software Project subject.

## Project Structure

```
Django-LABS/
└── lab1/
    └── djangotutorial/          # Main Django project
        ├── manage.py             # Django management script
        ├── db.sqlite3            # SQLite database
        ├── mysite/               # Project configuration
        │   ├── __init__.py
        │   ├── settings.py       # Project settings
        │   ├── urls.py           # URL routing configuration
        │   ├── wsgi.py           # WSGI configuration
        │   └── asgi.py           # ASGI configuration
        └── polls/                # Polls application
            ├── models.py         # Database models
            ├── views.py          # View logic
            ├── urls.py           # App URL patterns
            ├── admin.py          # Admin interface configuration
            ├── apps.py           # App configuration
            ├── tests.py          # Unit tests
            └── migrations/       # Database migrations
```

## Getting Started

### Prerequisites
- Python 3.x
- pip (Python package manager)

### Installation

1. Navigate to the Django project directory:
```bash
cd lab1\djangotutorial
```

2. Install Django (if not already installed):
```bash
pip install django
```

### Running the Development Server

Start the Django development server:
```bash
python manage.py runserver
```

The application will be available at `http://localhost:8000/`


## Available Apps

### Polls App
The polls application handles poll functionality:

- **Models**: Question and Choice models for poll management
- **Views**: Render poll pages and handle voting logic
- **Admin**: Manage polls through Django's admin interface
- **URLs**: Custom URL patterns for poll views


## Useful Management Commands

- `python manage.py runserver` — Start the development server

## Project Purpose

This repository contains Django lab work for the Software Project subject, demonstrating Django fundamentals including:
- Project and app structure
- Views and URL routing
