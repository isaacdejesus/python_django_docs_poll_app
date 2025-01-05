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
