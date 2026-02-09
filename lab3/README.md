# Lab 3: Django Static Files - CSS, Images & Templates

This lab covers how to serve static files (CSS, JavaScript, images) in Django applications and create HTML templates with proper styling.

## Overview

Lab 3 demonstrates:
- Creating a static files directory structure
- Writing and linking CSS stylesheets
- Adding and referencing images in CSS
- Creating HTML templates with the `{% static %}` template tag
- Rendering dynamic content in templates

## Project Structure

```
lab3/
└── djangotutorial/
    ├── manage.py
    ├── db.sqlite3
    ├── mysite/
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   ├── wsgi.py
    │   └── asgi.py
    └── polls/
        ├── migrations/
        ├── static/                          # ← NEW: Static files directory
        │   └── polls/
        │       ├── style.css                # ← Main stylesheet
        │       └── images/
        │           └── background.png       # ← Background image
        ├── templates/                       # ← NEW: Templates directory
        │   └── polls/
        │       └── index.html               # ← Main template
        ├── __init__.py
        ├── admin.py
        ├── apps.py
        ├── models.py
        ├── tests.py
        ├── urls.py
        ├── views.py
        └── __pycache__/
```

## What Are Static Files?

Static files are resources that don't change dynamically and are served directly to the browser:

- **Stylesheets (CSS)**: Define the look and feel of your website
- **Images**: Backgrounds, icons, logos, etc.
- **JavaScript**: Add interactivity to your pages
- **Fonts**: Custom typography
- **Other Assets**: PDFs, videos, etc.

## Static Files Directory Structure

Django uses a specific directory naming convention to avoid conflicts when multiple apps have static files:

```
polls/static/polls/style.css
         ↓    ↓
    App name  Namespace
```

### Why Namespace?

If two apps both had `style.css` directly in `static/`, Django wouldn't know which one to use. By namespacing them as `polls/style.css`, each app can safely organize its own assets.

## Setting Up Static Files

### Step 1: Create Directory Structure

```
polls/static/polls/                    # Create this folder
polls/static/polls/images/             # Create this folder for images
polls/templates/polls/                 # Create this folder for HTML
```

These directories have been created for you in this lab.

### Step 2: Create Stylesheet

**File: `polls/static/polls/style.css`**

```css
li a {
    color: green;
}

body {
    background: white url("images/background.png") no-repeat;
}
```

This stylesheet:
- Makes question links green
- Adds a background image to the page body
- Uses a relative path for the image (`images/background.png`)

### Step 3: Create HTML Template

**File: `polls/templates/polls/index.html`**

```html
{% load static %}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Polls Application</title>
    <link rel="stylesheet" href="{% static 'polls/style.css' %}">
</head>
<body>
    <div class="container">
        <h1>Polls</h1>
        {% if latest_question_list %}
            <ul>
            {% for question in latest_question_list %}
                <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
            {% endfor %}
            </ul>
        {% else %}
            <p>No polls are available.</p>
        {% endif %}
    </div>
</body>
</html>
```

**Key Elements:**

- `{% load static %}`: Loads Django's static file template tag
- `{% static 'polls/style.css' %}`: Generates the absolute URL to the CSS file
- `{% url 'polls:detail' question.id %}`: Generates URLs using Django's URL reversal
- `{% for ... %}...{% endfor %}`: Loops through the questions
- `{{ question.question_text }}`: Displays dynamic question text

### Step 4: Update Views

**File: `polls/views.py`**

```python
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

This view:
1. Fetches the 5 most recent questions
2. Creates a context dictionary with the questions
3. Renders the template with the context

### Step 5: Add Images

Create an image file at `polls/static/polls/images/background.png` and reference it in your CSS:

```css
body {
    background: white url("images/background.png") no-repeat;
}
```

**Important:** In CSS files, use relative paths. The `{% static %}` tag is only available in HTML templates, not in CSS.

## Installation & Running

### 1. Install Django
```bash
cd lab3/djangotutorial
pip install django
```

### 2. Apply Migrations
```bash
python manage.py migrate
```

### 3. Create a Superuser
```bash
python manage.py createsuperuser
```

### 4. Add Sample Data

Open Django shell and create some poll questions:

```bash
python manage.py shell
```

```python
from polls.models import Question, Choice
from django.utils import timezone

q = Question.objects.create(
    question_text="What is your favorite Django feature?",
    pub_date=timezone.now()
)

q.choice_set.create(choice_text="ORM", votes=0)
q.choice_set.create(choice_text="Admin Interface", votes=0)
q.choice_set.create(choice_text="Templates", votes=0)

exit()
```

### 5. Run the Server

```bash
python manage.py runserver
```

### 6. View the Application

Visit: `http://localhost:8000/polls/`

You should see:
- ✅ A green list of poll questions
- ✅ A background image
- ✅ Clickable links to each question

## Static File Finders

Django's `AppDirectoriesFinder` (enabled by default) automatically discovers static files in:

- `app_name/static/` directories within each installed app
- Any location specified in `STATICFILES_DIRS` in settings

You can see configured finders in:
```bash
python manage.py findstatic polls/style.css
```

## Template Tags for Static Files

| Tag | Purpose |
|-----|---------|
| `{% load static %}` | Load the static files template tag library |
| `{% static 'path/to/file' %}` | Generate absolute URL for a static file |
| `{{ STATIC_URL }}` | Get the STATIC_URL setting value |

## Important: Relative vs. Absolute Paths

### ✅ In HTML Templates (Use {% static %})
```html
<link rel="stylesheet" href="{% static 'polls/style.css' %}">
<img src="{% static 'polls/images/logo.png' %}">
```

### ✅ In CSS Files (Use Relative Paths)
```css
body {
    background: url("images/background.png");
}

@font-face {
    src: url("fonts/custom.woff2");
}
```

### ❌ Don't Use {% static %} in CSS
Static files aren't processed by Django's template engine, so `{% static %}` won't work in CSS.

## Production Deployment

For production, run:

```bash
python manage.py collectstatic
```

This command:
- Collects all static files from all apps
- Copies them to a single location (specified by `STATIC_ROOT`)
- Prepares them for serving via a web server (Nginx, Apache, etc.)

Read more: [Django Static Files Documentation](https://docs.djangoproject.com/en/stable/howto/static-files/)

## Common Issues & Solutions

### Issue: Static files not loading (404 error)
**Solution:** Ensure DEBUG=True in settings.py for development, and restart the server after adding new static files.

### Issue: {% static %} tag not working in template
**Solution:** Add `{% load static %}` at the top of your template.

### Issue: CSS not linking to images correctly
**Solution:** Use relative paths in CSS (e.g., `url("images/background.png")`) without the `{% static %}` tag.

### Issue: Admin styling looks broken
**Solution:** This usually means Django hasn't found its own static files. Ensure `django.contrib.staticfiles` is in INSTALLED_APPS.

## Learning Objectives

✅ Create static file directory structure  
✅ Write and link CSS stylesheets  
✅ Reference images in CSS and HTML  
✅ Use the `{% static %}` template tag  
✅ Understand static file namespacing  
✅ Render templates with view context  
✅ Use Django template tags for dynamic content  
✅ Differentiate between relative and absolute paths  

## Next Steps

- Add more CSS styling and improve the design
- Create detail and results templates for polls
- Add JavaScript for interactivity
- Implement voting functionality in views
- Add custom fonts and icons
- Create a responsive design with media queries

## File Reference

- [style.css](polls/static/polls/style.css) - Main stylesheet
- [index.html](polls/templates/polls/index.html) - Index template
- [views.py](polls/views.py) - View logic
- [urls.py](polls/urls.py) - URL configuration

## Resources

- [Django Static Files Documentation](https://docs.djangoproject.com/en/stable/howto/static-files/)
- [Django Template Language](https://docs.djangoproject.com/en/stable/topics/templates/)
- [CSS Relative Paths Guide](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)

## Customizing the Django Admin Interface

Beyond basic model registration, Django's admin interface is highly customizable. This lab includes advanced admin configurations:

### 1. Custom Admin Forms (fieldsets)

**File: `polls/admin.py`**

Organize form fields into logical groups (fieldsets) to improve usability:

```python
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None, {"fields": ["question_text"]}),
        ("Date information", {"fields": ["pub_date"], "classes": ["collapse"]}),
    ]
```

The `"collapse"` class makes the fieldset collapsible to save screen space.

### 2. Inline Editing with TabularInline

Edit related Choice objects directly when creating/editing a Question:

```python
class ChoiceInline(admin.TabularInline):
    model = Choice
    extra = 3  # Provide 3 empty choice slots
```

Then add to QuestionAdmin:
```python
class QuestionAdmin(admin.ModelAdmin):
    inlines = [ChoiceInline]
```

**Benefits:**
- Create multiple related objects at once
- Compact, table-based layout
- Delete related objects with checkboxes
- More efficient workflow than separate forms

### 3. Customizing the Change List

The "change list" displays all objects of a model. Customize it with:

```python
class QuestionAdmin(admin.ModelAdmin):
    list_display = ["question_text", "pub_date", "was_published_recently"]
    list_filter = ["pub_date"]
    search_fields = ["question_text"]
```

**Features:**
- `list_display`: Show specific fields as columns (click headers to sort)
- `list_filter`: Add a sidebar filter by the `pub_date` field
- `search_fields`: Add a search box to filter by question text

### 4. Using the @admin.display Decorator

Make custom methods display nicely in the admin:

```python
@admin.display(
    boolean=True,
    ordering="pub_date",
    description="Published recently?",
)
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

**Decorator Options:**
- `boolean=True`: Display as a colored checkmark or X
- `ordering="field_name"`: Allow sorting by this field
- `description="text"`: Custom column header text

### 5. Customizing Admin Templates

Override Django's default admin templates to customize the look and feel:

**File: `templates/admin/base_site.html`**

```html
{% extends "admin/base.html" %}

{% block branding %}
<div id="site-name"><a href="{% url 'admin:index' %}">Django Polls Administration</a></div>
{% endblock %}
```

This changes the header text from "Django administration" to "Django Polls Administration".

### 6. Customizing the Admin Index Page

**File: `templates/admin/index.html`**

Create a custom homepage for the admin interface with hardcoded links and custom layout:

```html
{% extends "admin/base_site.html" %}

{% block content %}
<div id="content-main">
  <h1>Welcome to the Django Polls Administration</h1>
  <p>This is the administration panel for managing polls and questions.</p>
  
  <div class="module">
    <table>
      <caption>Polls</caption>
      <tr>
        <td><a href="{% url 'admin:polls_question_changelist' %}">Questions</a></td>
      </tr>
      <tr>
        <td><a href="{% url 'admin:polls_choice_changelist' %}">Choices</a></td>
      </tr>
    </table>
  </div>
</div>
{% endblock %}
```

### 7. Registering Related Models

Register the Choice model so it can be managed separately:

```python
admin.site.register(Choice)
```

Now users can manage choices independently, or inline with questions.

## Project Structure (Updated)

```
lab3/
└── djangotutorial/
    ├── manage.py
    ├── db.sqlite3
    ├── templates/                          # ← NEW: Project templates
    │   └── admin/
    │       ├── base_site.html              # ← Custom admin header
    │       └── index.html                  # ← Custom admin index
    ├── mysite/
    │   ├── settings.py                     # ← Updated with DIRS
    │   └── ...
    └── polls/
        ├── migrations/
        ├── static/polls/
        │   ├── style.css
        │   └── images/background.png
        ├── templates/polls/
        │   └── index.html
        ├── admin.py                        # ← Customized admin config
        ├── models.py                       # ← Added @admin.display
        └── ...
```

## Configuration Changes

### Updated: `mysite/settings.py`

Add project template directory to TEMPLATES:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / "templates"],  # ← Add this line
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

## Admin Interface Features

After these customizations, the admin offers:

| Feature | Description |
|---------|-------------|
| **Fieldsets** | Group related fields into collapsible sections |
| **Inline Editing** | Edit related objects without leaving the parent form |
| **Sortable Columns** | Click column headers to sort the change list |
| **Filters** | Sidebar filters for quick access to subsets |
| **Search** | Full-text search on specified fields |
| **Custom Methods** | Display computed values with formatting options |
| **Custom Templates** | Override admin look and feel |
| **Custom Design** | Tailored homepage and navigation |

## Accessing the Admin Interface

After running the server, visit:

1. **Admin Homepage**: `http://localhost:8000/admin/` (custom styled)
2. **Questions**: `http://localhost:8000/admin/polls/question/`
3. **Choices**: `http://localhost:8000/admin/polls/choice/`

## Template Inheritance Hierarchy

When Django renders the admin:

1. Custom `templates/admin/base_site.html` (your override)
2. Falls back to Django's default admin templates
3. Extends `admin/base.html` for core structure
4. Uses `{% block %}` tags for customizable sections

This hierarchy allows partial customization without re-creating the entire admin template.

## Best Practices

✅ Use fieldsets to organize form fields logically  
✅ Use TabularInline for compact display of related objects  
✅ Add filters and search for better data discovery  
✅ Use `@admin.display` decorator for custom method formatting  
✅ Keep custom admin templates minimal (only override what you need)  
✅ Use relative paths for static files in CSS  
✅ Test admin customizations with actual data  

## Summary

Lab 3 teaches you how to properly organize and serve static files in Django applications AND how to customize the Django admin interface. You learned:

### Static Files & Templates:
1. The importance of namespacing static files
2. How to use the `{% static %}` template tag in HTML
3. How to reference static files in CSS using relative paths
4. How to create and use HTML templates
5. How to pass context data from views to templates

### Admin Customization:
6. How to customize admin forms with fieldsets
7. How to edit related objects inline
8. How to customize the change list view
9. How to use the @admin.display decorator
10. How to override admin templates
11. How to customize the admin index page

These skills are essential for building professional Django applications with proper styling, user experience, and efficient admin interfaces for managing data.
