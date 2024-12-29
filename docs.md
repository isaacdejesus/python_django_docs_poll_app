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
- outer project_name/: folder is parent dir for project. Automatically created but can be
  skipped to make current dir parent dir by adding '.' while creating project:
    - django-admin startproject NAME .
- manage.py: CLI utility used to manage django projects. manage.py is created in ea django
  project. Has same functionality as django-admin but also sets the DJANGO_SETTINGS_MODULE
  env variable so that it points to project's settings.py. Both django-admin and manage.py
  have same functionality/can run same commands.
    - All 3 below can be used to obtain same result
        - django-admin <commad>
        - manage.py <commad>
        - python -m django <command>
