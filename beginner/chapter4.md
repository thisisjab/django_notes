# Chapter 4: Using Django ORM
Django ORM provides a built-in support for many database backends which means you write your models in python code and Django will create related tables by itself. So you rarely will need to mess with database.

Django by default uses sqlite database - a file based database and easy to use for beginners. To create your database and related fields you need to run `migrate` command using `python manage.py migrate`.

According to the book:
> Technically, a `db.sqlite3` file is created the first time you run either `migrate` or `runserver`, however `migrate` will sync the database with the current state of any database models contained in the project and listed in `INSTALLED_APPS`. In other words, to make sure the database reflects the current state of your project, you’ll need to run `migrate` (and also `makemigrations`) each time you update a model.

## Creating database models
If you want to create a model using python code which will be used to create database tables automatically, you need to import `models` from `django.db` and for each model you create, you need to inherit from `models`.
To create a table's fields or columns, you need to use `models.FIELD_TYPE` which are known as model fields and full reference is available at [this link](https://docs.djangoproject.com/en/4.1/ref/models/fields/):
```python
from django.db import models


class Post(models.Model):
    text = models.TextField()

    def __str__(self):
        return self.text[:50]
```

We added a `__str__` method to our model class, because when intracting with Django admin panel (which we will do shortyly), Django will call this method to get each model's name.
## Activating created models
To activate created models we need two steps:
* First, running `makemigrations MIGRATION_NAME` to make migrations which is intended to keep track of every migration in database.
* Second, applying those migrations using `migrate` command which you are fimiliar with.

## Using Django admin page
One of the most interesting features of Django is its default powerful admin page. To use this feature we need to have a super user. To create one, type in this command: `python manage.py createsuperuser`

Now, navigate to `your.website/admin` and it enter you username and password and boom!

To inform admin page that a model does exist, we should register it in app's `admin.py` file. Imagine you have create a model named `Post`:
```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```

## Writing test
In previous chapter, we used `SimpleTestCase` as we did not have a database involved. Now let's use `TestCase`. `TestCase` will make a isolated database and run out tests on that, but not a actual database. Here, we create a Post object and then check if its content is what we expected:
```python
from django.test import TestCase
from .models import Post


class PostTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.post = Post.objects.create(text="This is a test!")

    def test_model_content(self):
        self.assertEqual(self.post.text, "This is a test!")
```