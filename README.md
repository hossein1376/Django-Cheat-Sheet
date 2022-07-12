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

- If you are planning to integrate a custom user model, **Do Not** run migrations untill you have configured those.

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

You can use function-based views (FBV) or to use class-based views(CBV). Each has its own advantages and uses.  
All of CBV classes can be found at [Classy Class-Based Views](https://ccbv.co.uk/).  
Each class has its own variables and methods that can be overridden if the need arises.  
For example:

```
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
To determine how our data should be displayed inside the admin panel page, we can add the following method to the end the the View class:

```python
  def __str__(self):
    return self.email_field # Select whatever field you want to be displayed
```

### template.html file

Populate the template with html code. Use {% %} tag to insert code and use {{ }} to insert variables into the template.  
Also, you can extend an existing template.

```html
<!-- _base.html -->
<html>
  <head>
    {% block title %}
    {% endblock title %}
  </head>
  <body>
    {% block content %}
    {% endblock content %}
  </body>
</html>
```

```html
<!-- template.html -->
{% extends '_base.html' %}

{block title}
  <title>Django Cheat Sheet</title>
{% endblock title %}

{% block content %}
  <h1>Welcome to {{name}}!</h1>
  <p><a href="{% url 'app home' %}">The url of this page</a></p>
{% endblock content %}
```

## Generic tasks and issues

### Change the default templates folder path

```python
# Project/settings.py
TEMPLATES = [
    {'DIRS': [str(BASE_DIR.joinpath('templates'))],},
]
```

### Change the default statics folder path

```python
# Project/settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [str(BASE_DIR.joinpath('static'))]
```

### Forms

You can configure you forms directly from views.py file. You need to mention the model's name that will store the data. Also, specify the fields to show to the user. Otherwise, all the avaliable fields will be displayed.  
If you want to customize the fields inside forms.py file, then you need to import that class from the forms.py and (instead of a model) insert the `form_class =`.

```python
# App/views.py
...

class Create(CreateView):
  model = Model
  template_name = 'template.html'
  fields = ['field1', 'field2',]


class Update(UpdateView):
  form_class = FormCLass
  template_name = 'template.html'
  fields = ['field1',]
  success_url = reverse_lazy('app home')

```

You can use `success_url` to redirect the user, or also you can define a `get_absolute_url` method in the model to specify the url to redirect to.  
Whenever you are receving data from user to modify your database; in your html form you need to use POST request method and include {% csrf_token %} for security purposes.

```html
<!-- template.html -->
{% extends '_base.html' %}

{% block content %}
  <form action="" method="POST">
    {% csrf_token %}
    {{ form }}
    <button type="submit">submit</button>
  </form>
{% endblock content %}
```

### More forms customizations

//

### Django user authentication

**Log In**  
You can use Django's own authentication for easy and fast development. In order to do that, the following goes:

```python
# Project/urls.py
urlpatterns = [
  ...
  path('accounts/', include('django.contrib.auth.urls')),
  ...
  ]
```

You templates need to be inside a folder called `registration`.  

```html
<!-- login.html -->
{% extends '_base.html' %}

{% block content %}
<h1><a href="{% url 'login' %}">Log In</a></h1>
  <form action="" method="POST">
    {% csrf_token %}
    {{ form }}
    <button type="submit">submit</button>
  </form>
{% endblock content %}
```

Also, we need to specify the login redirect url.

```python
# Project/settings.py
LOGIN_REDIRECT_URL = 'app home'
```

Notice that there is no need to define a view or a model.  
**Log Out**  
If we have already updated the urls.py file, all we need to do is to link the logout url as `{% url 'logout' %}` and add the redirect to the seetings.py file.

```python
# Project/settings.py
LOGIN_REDIRECT_URL = 'app home'
LOGOUT_REDIRECT_URL = 'app home' # Added this
```

That's it!

### Custom user authentication

Firstly, to let django know of the redirects, we add this:

```python
# Project/settings.py
LOGIN_REDIRECT_URL = 'app home'
LOGOUT_REDIRECT_URL = 'app home'
```

### Only logged in users can view

We can use Django template language for this task.

```html
<!-- template.html -->
...

  {% if user.is_authenticated %}
    <p>You are logged in.</p>
  {% else %}
    <p>You are not logged in.</p>
  {% endif %}
...
```

### Django UserCreationForm

UserCreationForm comes with three predefined fields: username, password1, and password2. If these met our needs, we can happily proceed. Otherwise, we need to make use of [Custom User Models](#custom-user-model).  
It's preferable to have a [seprate app](#start-an-app) for this. [Configure the url](#pass-the-request-to-the-apps-urlspy).  
**Note:** If you are making use of [Django user authentication](#django-user-authentication), then you should add the url *after* the `auth` urls.

```python
# Project/urls.py

urlpatterns = [
  ...
  path('accounts/', include('django.contrib.auth.urls')),
  path('accounts/', include('accounts.urls')),
  ...
]
```

Now, we take care of the url:

```python
# accounts/urls.py
from django.urls import path
from .views import SignUpView

urlpatterns = [
    path('signup/', SignUpView.as_view(), name='signup'),
]
```

Next, views:

```python
# accounts/views.py
from django.contrib.auth.forms import UserCreationForm
from django.urls import reverse_lazy
from django.views.generic import CreateView


class SignUpView(CreateView):
    form_class = UserCreationForm
    success_url = reverse_lazy('login')
    template_name = 'registration/signup.html'
```

Lastly, the template file:

```html
<!-- templates/registration/signup.html -->
{% extends 'base.html' %}

{% block content %}
  <h2>Sign Up</h2>
  <form method="POST">
    {% csrf_token %}
    {{ form }}
    <button type="submit">Sign Up</button>
  </form>
{% endblock content %}
```

### Custom User Model

After starting the project, we **should not** run migrations if we're planning to implement a custom user model.  
We start [a new  app](#start-an-app) and add its name and the following to settings.py:

```python
AUTH_USER_MODEL = 'accounts.CustomUser'
```

We make a new User Model:

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
  name = models.CharField(max_length=100)
```

We customize the model in the forms.py file:

```python
# accounts/forms.py
from django import forms
from django.contrib.auth.forms import UserCreationForm, UserChangeForm
from .models import CustomUser

class CustomUserCreationForm(UserCreationForm):
  class Meta(UserCreationForm):
    model = CustomUser
    fields = UserCreationForm.Meta.fields + ('age',)


class CustomUserChangeForm(UserChangeForm):
  class Meta:
    model = CustomUser
    fields = UserChangeForm.Meta.fields
```

Then, we need to update the admin.py:

```python
# accounts/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .forms import CustomUserCreationForm, CustomUserChangeForm
from .models import CustomUser

class CustomUserAdmin(UserAdmin):
  add_form = CustomUserCreationForm
  form = CustomUserChangeForm
  model = CustomUser

  list_display = ['email', 'username', 'name', ]
  fieldsets = UserAdmin.fieldsets + (
    (None, {'fields': ('name',)}),
    )
  add_fieldsets = UserAdmin.add_fieldsets + (
      (None, {'fields': ('name',)}),
  )

admin.site.register(CustomUser, CustomUserAdmin)
```

### Static files

Add this to your settings.py file to let it know where to look for static files.

```python
# Project/settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [str(BASE_DIR.joinpath('static'))]
STATIC_ROOT = str(BASE_DIR.joinpath('staticfiles'))
STATICFILES_STORAGE =
    'django.contrib.staticfiles.storage.StaticFilesStorage'
```
