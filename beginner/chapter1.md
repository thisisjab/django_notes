# Chapter 1: Initial Set Up
## Steps for creating a Django project:
- Create a virtual environment and activate it
- Install Djagno package using pip
- Start a new project by executing `django-admin startproject my_first_project .`

**Attention:** if you don't add a single dot character at the end of last command, an additional directory will be created with the same name of your project name which you may don't want to happen.
***
From now on, you are going to use `manage.py` file instead of `django-admin` command. Generally, when working on a single Django project, it's easier to use `manage.py` than `django-admin` command.
***
## Run your projet
You can run your project by `runserver` command:

`python manage.py runserver`

If you see migration errors, don't worry. Ignore for now or just run `python manage.py migrate` to get away with it. We will talk about migrations in future.