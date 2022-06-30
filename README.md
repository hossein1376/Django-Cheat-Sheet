# Django Cheat Sheet

This is a list of intial commands, generic issues and tasks, Q&amp;A problems and niches.

- [Intial Commands](#intial-commands)
  - [Start a virtual environment](#start-a-virtual-environment)
  - [Activating the virtual environment](#activating-the-virtual-environment)
  - [Starting a Django Project](#starting-a-django-project)
  - [Run server](#run-server)
  - [Migration](#migration)
  - [Start an app](#start-an-app)
  - [Django MVC](#django-mvc)
  - [Create admin](#create-admin)
  - [Add a model to admin page](#add-a-model-to-admin-page)
- [How to display a page](#how-to-display-a-page)
  - [Pass the request to the app's urls.py](#pass-the-request-to-the-apps-urlspy)
  - [App urls.py](#app-urlspy)
  - [views.py file](#viewspy-file)
  - [models.py file](#modelspy-file)
  - [template.html file](#templatehtml-file)

&nbsp;

## Intial Commands

### Start a virtual environment

There are multiple ways of starting a virtual environment, I use venve due ti its simplicity and ease of use.

```console
python -m venv VIRTUAL_ENV_NAME
```

### Activating the virtual environment

If you're inside your django project folder which we will be creating in a few moments, you can put the .. before the path. Like this:

```console
..\scripts\activate
```

### Starting a Django Project

```console
django-admin startprojject Project
```

### Run server

You can specify the port or leave it empty to use the default, which is 8000.

```console
python manage.py runserver 8000
```

### Migration

```console
python manage.py makemigrations
python manage.py migrate
```

- You can specify the desired app to run the migration for that app alone, or leave it empty to make migrations for all apps in the project.

```console
python manage.py makemigrations App
```

- You need to run migrations everytime you modify models.py or settings.py files.

- If you are planning to integrate a custum user model, **Do Not** run migrations untill you have configured those.

### Start an app

```console
python manage.py startapp App
```

Then, you need to add it to settings.py.

### Django MVC

URL -> View -> (Model) -> Template

### Create admin

```console
python manage.py createsuperuser
```

### Add a model to admin page

```python
# App/admin.py
admin.site.register(Model)
```

&nbsp;

## How to display a page

### Pass the request to the app's urls.py

```python
# Project/urls.py
from django.urls import path, include

urlpatterns = [
  path('', include('app.urls')),
]
```

### App urls.py

```python
# App/urls.py
from django.urls import path
from .views import View

urlpatterns = [
  path('home/', View.as_view(), name= 'app home'),
]
```

You need to put the desired url as a string; and it's better to add a trailing slash after it too.  
Then, you mention the view. If your view is Class Based, you must add the `.as_view()` method after it.  
Lastly, you can define a name for this specefic url.

### views.py file

You can use function based views (FBV) or to use class based views(CBV). Each has its own advantages and uses.  
All of CBV classes can be found at [Class Class Based Views](https://ccbv.co.uk/).  
Each class has its own variables and methods that can be overridden if the need arises.  
For example:

```python
template_name: string -> html template name and path
extra_content: dictionary -> A dictionary of variables you need to pass to the template
```

So we have:

```python
# App/views.py
from django.views.generic import TemplateView

class View(TemplateView):
    template_name = 'template.html'
    extra_content = {'name': 'Django Cheat Sheet',}
```

### models.py file

Defining a model is not required, unless you need to input or output data to or from a database.  
Here is a list of some of the more common models:

```python
# App/models.py
from django.db import models

class Model(models.Model):
  boolean = models.BooleanField(default=False) # If no default is defined, it'll be None.
  character_field = models.CharField(max_length=255) # max_length is required
  date_field = models.DateField(auto_now=False, auto_now_add=False) # Both are optional
  date_time_field = models.DateTimeField(auto_now=False, auto_now_add=False)
  decimal_field = models.DecimalField(max_digits=5, decimal_places=2) # i.e: For up to 999.99
  email_field = models.EmailField(max_length=255, unique=True) # uniqe argument is optional
  integer_field = models.IntegerField()
  text_field = models.TextField() # max_length is optional

  ForeignKey_field = models.ForeignKey('self', on_delete=models.CASCADE) # Pass the desired model instead of 'self'
```

If we need to display the model inside the admin page, we can add it to admin.py file. [As shown here](#add-a-model-to-admin-page).  
To determine how our data should be displayed inside the admin panel page, we can add the following at the end the the View class:

```python
  def __str__(self):
    return self.email_field # Select whatever field you want to be displayed
```

### template.html file

Populate the template with html code. Use {% %} tag to insert code and use {{ }} to insert variables into the template.  
Also, you can extend an existing template.

```html
{% extends '_base.html' %}

<html>
  <head>
    <title>{{name}}</title>
  </head>
  <body>
    <h1>Welcome to {{name}}</h1>
  </body>
</html>
```
