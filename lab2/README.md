# Lab 2: Django Polls Application - Models & Admin

Building upon the Django fundamentals, Lab 2 focuses on creating database models and implementing the Django admin interface for managing poll data.

## Overview

This lab extends the basic Django setup by:
- Creating database models for questions and choices
- Setting up the Django admin interface
- Implementing model methods and string representations
- Managing relationships between models using Foreign Keys

## Project Structure

```
lab2/
└── djangotutorial/
    ├── manage.py                 # Django management script
    ├── db.sqlite3                # SQLite database
    ├── mysite/                   # Project configuration
    │   ├── __init__.py
    │   ├── settings.py           # Django settings
    │   ├── urls.py               # URL routing configuration
    │   ├── wsgi.py               # WSGI application
    │   └── asgi.py               # ASGI application
    └── polls/                    # Polls application
        ├── migrations/           # Database migration files
        ├── __init__.py
        ├── admin.py              # Admin interface configuration
        ├── apps.py               # App configuration
        ├── models.py             # Database models
        ├── tests.py              # Unit tests
        ├── urls.py               # URL patterns
        └── views.py              # View logic
```

## Prerequisites

- Python 3.x
- Django installed
- Basic understanding of Python and databases

## Installation & Setup

### 1. Navigate to the Project Directory
```bash
cd lab2/djangotutorial
```

### 2. Install Django (if not already installed)
```bash
pip install django
```

### 3. Apply Database Migrations
```bash
python manage.py migrate
```

### 4. Create a Superuser
```bash
python manage.py createsuperuser
```

You'll be prompted to enter:
- Username
- Email address
- Password (twice for confirmation)

## Running the Application

Start the development server:
```bash
python manage.py runserver
```

Access the admin interface:
- URL: `http://localhost:8000/admin/`
- Log in with your superuser credentials

## Database Models

### Question Model
Represents a poll question with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `question_text` | CharField | The text of the question (max 200 characters) |
| `pub_date` | DateTimeField | The publication date and time |

**Methods:**
- `was_published_recently()`: Returns True if the question was published within the last 24 hours
- `__str__()`: Returns the question text as the string representation

### Choice Model
Represents a voting option for a question with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `question` | ForeignKey | Reference to the Question model (CASCADE on delete) |
| `choice_text` | CharField | The text of the choice (max 200 characters) |
| `votes` | IntegerField | Number of votes for this choice (default: 0) |

**Methods:**
- `__str__()`: Returns the choice text as the string representation

### Model Relationships

```
Question (1)
   ↓
   ├── (1 to Many)
   ↓
Choice (∞)
```

When a Question is deleted, all associated Choices are automatically deleted (CASCADE behavior).

## Admin Interface

The admin interface is configured in `admin.py`:

```python
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

### Features Available in Admin:
- ✅ Create new poll questions
- ✅ Edit existing questions
- ✅ Delete questions (and cascading choices)
- ✅ View all questions and choices

## Creating Migrations

After modifying models, create and apply migrations:

```bash
# Create migration files
python manage.py makemigrations polls

# Apply migrations to database
python manage.py migrate
```

## Using the Admin Interface

### Adding a Question:
1. Log in to `http://localhost:8000/admin/`
2. Click "Questions" → "Add Question"
3. Enter the question text and publication date
4. Click Save

### Add Choices to a Question:
1. Navigate to "Choices" → "Add Choice"
2. Select the associated Question
3. Enter choice text and initial vote count
4. Click Save

Or edit a Question and add choices inline if configured.

## Common Django Commands

| Command | Description |
|---------|-------------|
| `python manage.py makemigrations` | Create migration files for model changes |
| `python manage.py migrate` | Apply migrations to the database |
| `python manage.py createsuperuser` | Create an admin account |
| `python manage.py shell` | Open Django shell for database queries |
| `python manage.py runserver` | Start development server |

## Interacting with Models in Django Shell

Open the interactive shell:
```bash
python manage.py shell
```

Example queries:
```python
from polls.models import Question, Choice
from django.utils import timezone

# Create a new question
q = Question.objects.create(
    question_text="What is your favorite color?",
    pub_date=timezone.now()
)

# Add choices
q.choice_set.create(choice_text="Red", votes=0)
q.choice_set.create(choice_text="Blue", votes=0)

# Query all questions
questions = Question.objects.all()

# Filter questions
recent = Question.objects.filter(pub_date__gte=timezone.now())

# Check if published recently
q.was_published_recently()
```

## Learning Objectives

This lab covers:

✅ Creating Django Models  
✅ Model Fields and Data Types  
✅ Foreign Key Relationships  
✅ Model Methods and __str__() representation  
✅ Django Admin Interface  
✅ Database Migrations  
✅ Admin Registration  
✅ Query Django ORM in Python Shell  

## Next Steps

- Customize the admin interface with `ModelAdmin` classes
- Create views to display poll data using templates
- Implement voting functionality
- Add model validation and constraints
- Write unit tests for models and views
- Create custom admin actions

## Troubleshooting

### Issue: Migration errors
**Solution:** Delete `db.sqlite3` and `migrations/__pycache__`, then run:
```bash
python manage.py migrate
```

### Issue: Can't access admin panel
**Solution:** Ensure you created a superuser:
```bash
python manage.py createsuperuser
```

### Issue: Models not showing in admin
**Solution:** Make sure models are registered in `admin.py`:
```python
admin.site.register(Question)
```

## Resources

- [Django Models Documentation](https://docs.djangoproject.com/en/stable/topics/db/models/)
- [Django Admin Documentation](https://docs.djangoproject.com/en/stable/ref/contrib/admin/)
- [Django QuerySet API](https://docs.djangoproject.com/en/stable/ref/models/querysets/)

## Notes

Lab 2 provides the foundation for working with databases in Django. The models and admin interface created here will be extended with views, templates, and additional functionality in subsequent labs.
