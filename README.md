# Django Cheat Sheet

This is a list of intial commands, generic issues and tasks, Q&amp;A problems and niches.

## Start a virtual environment

There are multiple ways of starting a virtual environment, I use venve due ti its simplicity and ease of use.

```console
python -m venv VIRTUAL_ENV_NAME
```

## Activating the virtual environment

If you're inside your django project folder which we will be creating in a few moments, you can put the .. before the path. Like this:

```console
..\scripts\activate
```

## Starting a Django Project

```console
dhango-admin startprojject Project
```

## Run server

You can specify the port or leave it empty to use the default, which is 8000.

```console
python manage.py runserver 8000
```

## Migration

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

## Start an app

```console
python manage.py startapp App
```

Then, you need to add it to settings.py.

## Django MVC

URL -> View -> (Model) -> Template

## Create admin

```console
python manage.py createsuperuser
```

## Add model to admin page

```python
# App/admin.py
admin.site.register(Model)
```
