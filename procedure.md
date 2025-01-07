## Create virtual environment
- Navigate to project_folder
- Create env
    - python -m venv env
- Activate virtual env
    - source env/bin/activate
- Deactivate virtual env
    - deactivate
## Install django
- cd project_folder
- pip install django
## Create project
- django-admin startproject project_name .
    - '.' creates project in current dir. Without '.' an empty parent dir is created with same name is created.
## Running app/server
- Navigate to dir containing manage.py
    - python manage.py runserver
## Create app
- Navigate to folder containing *manage.py*
    - python manage.py startapp app_name
## Create a view
- Navigate to app_name folder
```python
#app_name/views.py
from django.http import HttpResponse
def index(request):
    return HttpResponse("Hello from index of app_name")
```
## Map view to URL
- Navigate to app_name and create *urls.py*
```python
#app_name/urls.py
from django.urls import path
from . import views
urlpatterns = [
    path("", views.index, name="index"),
]
```
## Config global URLcong to include app_name URLconf
- Navigate to main project and modify *urls.py*
```python
#project_name/urls.py
from django.contrib import admin
from django.urls import include, path
urlpatterns = [
    path("polls/", include("polls.urls")),
    path("admin/", admin.site.urls),
]
```
## Visit view
- *python manage.py runserver*
- Visit *localhost:8000/app_name*
