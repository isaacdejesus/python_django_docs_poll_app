# 1. Set up and creating project
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
# 2. Databases set up
## Set up database for built in apps
- django projects come with built in apps with each requiring a database table in order to work properly
  .:. must run migrate command to create those tables. Migrate creates tables based on settings and models
```python python manage.py migrate```
## General process to create database and tables is
1. Create models in *app_name/models*
    ```python
    # polls/models.py
    from django.db import models
    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField("date published")

    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)
    ```
2. Activating models
- To include apps in project need to reference app config in INSTALLED_APPS setting of project.
  Ex. *app_name* polls. The PollsConfig class is in *polls/apps.py* file .:. dotted path is
  *polls.apps.PollsConfig*. Edit *project_name/settings.py* and add dotted path to INSTALLED_APPS
  ```python
  # poll_app/settings
    INSTALLED_APPS = [
        "polls.app.PollsConfig",
        "django.contrib.admin",
        "django.contrib.auth",
        "django.contrib.contenttypes",
        "django.contrib.sessions",
        "django.contrib.messages",
        "django.contrib.staticfiles",
    ]
  ```
- Now django knows *polls* app is part of the project and will use those models to create tables
  when migrate commands are ran

3. run *makemigrations*
    ```python
    python manage.py makemigrations app_name
    ```
   - *makemigrations* tells django to check models for changes and to store them as a migration.
     Migration is how django stores changes to models/database. 
4. Create model tables in database
    ```python python manage.py migrate```
    - Migrate takes all migrations and runs them against the database. Resulting in either creating new
      tables or modifying existing ones if models changed. .:. sync changes made to models with schema
      in db.
### Database procedure summary
- Create/modify models in *app_name/models.py*
- Run *python manage.py makemigration* to create migrations for changes to models
- Add app to project. See 3 above
- Run *python manage.py migrate* to apply changes to database

