## Createing project with django-admin startproject NAME results in following structure
```
    project_name/
        manage.py
        project_name/
            __init__.py
            settings.py
            urls.py
            asgi.py
            wsgi.py
```
- *outer project_name/*: folder is parent dir for project. Automatically created but can be
  skipped to make current dir parent dir by adding '.' while creating project:
    - django-admin startproject NAME .
- *manage.py*: CLI utility used to manage django projects. manage.py is created in ea django
  project. Has same functionality as django-admin but also sets the DJANGO_SETTINGS_MODULE
  env variable so that it points to project's settings.py. Both django-admin and manage.py
  have same functionality/can run same commands.
    - All 3 below can be used to obtain same result
        - django-admin <commad>
        - manage.py <commad>
        - python -m django <command>
- *inner project_name/*: Actual django project folder. It is the package name used to import anything
  inside.
- *project_name/__init__.py*: Tells python the current directory is a python package. 
- *project_name/settings.py*: Contains settings and config for the project. 
- *project_name/urls.py*: URL declarations for project. 
- *project_name/asgi.py*: Entry point for ASGI-compatible web servers.
- *project_name/wsgi.py*: Entry point for WSGI-compatible web servers.

## Running the development server/app
```python
python manage.py runserver
- Starts a local dev web server on port 8000. 
- DO NOT USE IN PRODUCTION SETTING!
- Development server automatically reloads code as needed.
```
## Creating apps
- Environment is now a project .:. can start creating apps
- Project is a collection of config and apps for a website. 
- Project can have multiple apps.
- App is a web application that does something.
- App can be in multiple projects.
- Apps can live anywhere in python path
- In the same inventory as *manage.py*
```python
python manage.py startapp app_name
```
- Creates an app with following structure
    ```python
    app_name/
        __init__.py
        admin.py
        apps.py
        migrations/
            __init__.py
        models.py
        tests.py
        views.py
    ```
## Creating views
- View is page/content to be displayed at given route
- Inside app_name/views.py
```python
#app_name/views.py
from django.http import HttpResponse
def index(request):
    return HttpResponse("hello, this is the index page.")
```
## Map view to URL
- In order for the view to be displayed, it must be mapped to a URL .:. need to define a URL config
  or "URLconf" for short. 
- URL config are defined inside each django app
- URL config are python files named urls.py
- Inside app_name/ create a urls.py file
```python
from django.urls import path
from . import views
urlpatterns = [
    path("", views.index, name="index"),
]
```
- app structure becomes
    ```python
    app_name/
        __init__.py
        admin.py
        apps.py
        migrations/
            __init__.py
        models.py
        tests.py
        urls.py
        views.py
    ```
## Configure global URLconf 
- The project's global URLconf in project_name/urls.py must be configured to include the URLconfig
  defined in app_name/urls.py
- import *django.urls.include*
- use include() to add path for app_name.urls
```python
from django.contrib import admin
from django.urls import include, path
urlpatterns = [
    path("app_name/", include("app_name.urls")),
    path("admin/", admin.site.urls),
]
```
- path() expects 2 arguments: route and view. 
- include() allows reference other URLconfs
- When django encounters include(), chops off whatever URL matched up to that points and sends remaining 
  string to the included URLconf for further processing
- include() makes it esy to plug & play URLs. Since app_name are in their own URLconf in app_name/urls.py
  they can be placed under /app_name or /content/app_name/ or any other path root
- ALWAYS use include() when including other URL patterns. 
- admin.site.urls is URLconf for admin site. Doesn't require include()

```python
from django.urls import path
from . import views
urlpatterns = [
    path("", views.index, name="index"),
]
```
- Results in config an index view to app_name URLconf .:. can visit *localhost:8000/app_name*
