# 1. Createing project with django-admin startproject NAME results in following structure
```python
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
```
- Starts a local dev web server on port 8000. 
- DO NOT USE IN PRODUCTION SETTING!
- Development server automatically reloads code as needed.
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
# 2. Database set up
- project_name/settings.py: Contains config for entire project. 
    - By default database is configured to use SQLite
    - Be sure to use a real database for real projects
    - INSTALLED_APPS: lists django apps that are part of the project. By default django comes with 
      following apps installed
        - django.contrib.admin: Admin site
        - django.contrib.auth: Authentication system
        - django.contrib.contenttypes: Framework for content types
        - django.contrib.sessions: Session framework
        - django.contrib.messages: Messaging framework
        - django.contrib.staticfiles: Framework for managing static files
    - Note apps can be used across multiple projects
## Creating tables
```python
python manage.py migrate
```
- Creates a database table for installed apps
- Each of the INSTALLED_APPS requires a database table before they can be used.
- migrate command looks at INSTALLED_APPS and creates necessary database tables according to settings
  in project_name/settings.py 
- migrate will only run migrations for apps in INSTALLED_APPS
## Creating models
- Models are the database layout
- Models are represented as python classes
- Creating two models for the polls app
    - Question: model has following attributes 
        - question_text
        - pub_date
    - Choice: model has following attributes
        - choice_text
        - votes
```python
#app_name/models.py
from django.db import models
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
``` 
- Each model is represented by a class that subclasses *django.db.models.Model*
- Each model has class variables which represent a database field in the model
- Each field is represented by an instance of a Field class. Examples:
    - CharField for char fields
    - DateTimeField for datetimes
- Some fields have required arguments which are used for validation
    - CharField has a max_length as required argument
- Field can have optional arguments
    - default=0
- Relations are defined using ForeignKey. In this case it tells Django *Choice*
  is related to a single *Question*
## Activating models
- model code allows django to:
    - Create a database schema(CREATE TABLE statements) for the app
    - Create a Python database-access API for accessing *Question* and 
      *Choice* objects
- Need to tell Django/project that *polls* app is installed
- In order to include the app in project,need to add a reference to its config class
  in the INSTALLED_APPS setting. *PollsConfig* class is in *polls/apps.py*, .:. dotted path
  is *polls.apps.PollsConfig*. 
    - edit *project_name/settings.py* and under INSTALLED_APPS add the dotted path:
        - *polls.apps.PollsConfig*
- Note: Django apps are pluggable. Meaning an app can be in multiple projects
- Result of adding *polls* app to project
```python
#project_name/settings.py
INSTALLED_APPS = [
    "polls.apps.PollsConfig",
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```
- Now django knows to include *polls* app
```python
python manage.py makemigrations polls
```
- *makemigrations* tells django that changes have been made to models and to store changes 
  as a migration.
- Migrations is how django stores changes made to models
- *migrate* command runs migrations and manage database schemas
- *sqlmigrate* command takes migration names and returns their SQL
```python
python manage.py sqlmigrate polls 0001
```
```sql
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" (
    "id" bigint NOT NULL PRIMARY KEY GENERATED BY DEFAULT AS IDENTITY,
    "question_text" varchar(200) NOT NULL,
    "pub_date" timestamp with time zone NOT NULL
);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
    "id" bigint NOT NULL PRIMARY KEY GENERATED BY DEFAULT AS IDENTITY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL,
    "question_id" bigint NOT NULL
);
ALTER TABLE "polls_choice"
    ADD CONSTRAINT "polls_choice_question_id_c5bb260_fk_polls_questions_id"
        FOREIGN KEY ("question_id")
        REFERENCES "polls_question" ("id")
        DEFERRABLE INITIALLY DEFERRED;
    CREATE INDEX "polls_choice_question_id_c5bb260" on "polls_choice" ("question_id");
COMMIT;
```
- *sqlmigrate* doesn't run the migration. Simply prints to screen the SQL code django will
  run to create tables/schema. 
- Table names generated by combining app_name and lowercase of model
    - polls_choice
    - polls_question
- Primary keys(ids) added automatically
- Django appends "_id" to foreign key field name
- Foreign key relationship made explicit by FOREIGN KEY constraint. 
```python
python manage.py check
```
- above command checks for mistakes without making migrations or touching the db
```python
python manage.py migrate
```
- migrate takes all migrations that haven't been applied and runs them against the db. 
  sync changes made to models with schema in db .:. migrate creates tables in db baseed 
  on changes made to models.
- migrations allow to change models over time without neeeding to delete db/tables and 
  having to make new ones .:. migrations allow to upgrade schemas live without losing data
### Steps to making changes to models
- create/modify models in app_name/models.py
- *python manage.py makemigration*  ---> creates migrations for models
- *python manage.py migrate*        ---> apply changes to db
## Playing with the API
- Activating models allows python to create the tables for app as well as create a
  python database access API for accessing the *Question* and *Choice* objects
- invoke the python shell bc manage.py sets the *DJANGO_SETTINGS_MODULE* environmental
  variable which gives django the python import path to *project_name/settings.py*
```python
python manage.py shell

# Import model classes
from polls.models import Choice, Question

# Create a new question object
from django.utils import timezone
q = Question(question_text="what's up?", pub_date=timezone.now())

# save newly created Question entry q to database
q.save()
# Saving entry to db gives it an ID
q.id               # 1

# Access model field values via python attributes
q.question_text    # "what's up?"
q.pub_date         # ugh, it's some long datetime string

# Change values by changing the attributes
q.question_text = "what's new?"
q.save()

# objects.all() displays all questions in db
Question.objects.all()    # <QuerySet [<Question: Question object (1)>]>
# Above output isn't very useful .:. add a *__str__() method to the models
```
- Updating models to provide better output
```python
#polls/models.py
from django.db import models
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    question = models.ForeignKey(Queestion, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```
- Adding *__str__()* methods to models is important for convenience and bc object 
  representation is used by django admin
- Add a custom method to model
```python
#polls/models.py
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")
    def __str__(self):
        return self.question_text
    def was_published_recently(self):   
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

class Choice(models.Model):
    question = models.ForeignKey(Queestion, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```
- Back to playing with API
```python
# launch shell
python manage.py shell
# import models
from polls.models import Choice, Question
# Test __str__() method works
Question.objects.all()  # <QuerySet [<Question: What's new?>]>
# Use API to search
Question.objects.filter(id=1)  # <QuerySet [<Question: What's new?>]>
Question.objects.filter(question_text__startswith="What") # <Queryset [<Question: What's new?>]>
# Retrieve question published this year
from django.utils import timezone
current_year = timezone.now().year
Question.objects.get(pub_date__year=current_year)  # <Question: What's new?>
# Requesting nonexistent id returns exception
Question.objects.get(id=2)  #DoesNotExist: Question matching query does not exist.

# Searching by primary key is common .:. django provides shortcut to search 
# Following is same as : Question.objects.get(id=1)
Question.objects.get(pk=1)  # <Question: What's new?>

# Check custom method works
q = Question.objects.get(pk=1)
q.was_published_recently()  #true

# Save question with primary key = 1 to a variable
q = Question.objects.get(pk=1)
# Note we have 2 models/tables: Question & Choice. 
# There is a one to many relationship between Question and Choice. Meaning a Question can have
# multiple choices. When there's a relationship between tables django automatically creates a
# modelname_set() set of functions that can be used to manipulate/set properties to table
# in this case choice_set allows to set/create a Choice object & link it to the Question object.
# use choice_set.all() to check Choice objects linked to current Question object
q.choice_set.all()  #<QuerySet []>
# Returns an empty array since there are no Choice objects currently linked to current Question object

# use choice_set.create() to create a new Choice object and link it to current Question object
# Note choice_text and votes are atttributes of Choice object/class/table
# Creates a Choice object with text "Not much" and links it to Question
q.choice_set.create(choice_text="Not much", votes=0)
# Creates a Choice object with text "The Sky" and links it to Question
q.choice_set.create(choice_text="The Sky", votes=0)
# Saves the newly created Choice object to variable c
c = q.choice_set.create(choice_text="Hacking again", votes=0)

# Choice objects have API access to Question object they are linked to
c.question   # <Question: What's up?>

# Question objects have API access to Choice objects linked to them
q.choice_set.all()
# Above query returns all Choice objects linked to current Question object
# <Queryset [<Choice: Not Much>, <Choice: The Sky>, <Choice: Hacking again>]>

# API follows relationships as needed
# __ used to separate relationships. Can go as deep as want
# Find all Choices for any question whose pub_date is in this year
Choice.objects.filter(question__pub_date__year=current_year)
# above says, search the Choice table for a question whose pub_date matches year in current_year var

# Deleting a Choice object
c = q.choice_set.filter(choice_text__startswith="Hacking again")
c.delete()
```
# django admin
## Creating an admin user
```python
python manage.py createsuperuser
```
- Will be asked to enter username:  #admin
- Will be asked to enter email:     #admin@example.com
- Will be asked to enteer pass:

- Start dev server ```python python manage.py runserver```
- Visit *localhost:8000/admin*
- Log in
## Register Question table to be accessible on django admin
- navigate to *app_name/admin.py*
```python
from django.contrib import admin
from .models import Question
admin.site.register(Question)
```
- Now that Question model/table has been registered, it will be displayed on django admin
- Select *Questions* which will display all available Question objects in the db and 
  allow to modify or delete them
- The form is generated from Question model
# Finished part 2. Start at Part 3 whenever I return
