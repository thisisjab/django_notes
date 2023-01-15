# Chapter 5

Database models can have relationships with each other. For example, a school model can have many students and one student can be related to one school; A teacher can have many students and each student can have many teachers. Each students has one id and one id is only for one student. It this chpater only many-to-one relationships are discussed.

## Many-to-one relationships

To define a many-to-one relationship, we use `ForeignKey`. In this example, which is references Django's official documentation, a `Reporter` can be associated with many `Article` objects, but an `Article` can only have one `Reporter` object:

```python
from django.db import models

class Reporter(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    email = models.EmailField()

    def __str__(self):
        return "%s %s" % (self.first_name, self.last_name)

class Article(models.Model):
    headline = models.CharField(max_length=100)
    pub_date = models.DateField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

    def __str__(self):
        return self.headline
```

In another example, one `User` can have many `Post` objects, but each `Post` is only for one user:

```python
from django.db import models
from django.urls import reverse


class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(
        "auth.User",
        on_delete=models.CASCADE,
    )
    body = models.TextField()

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse("post_detail", kwargs={"pk": self.pk})
```

`on_delete` keyword argument tells Django what to do when a foreign key is deleted from database. Here are the values you can pass and information about each of them, which are obtained from [this answer](https://stackoverflow.com/a/38389488) in stackoverflow and [Django's official documentation](https://docs.djangoproject.com/en/4.1/ref/models/fields/#foreignkey):

- `CASCADE`: When the referenced object is deleted, also delete the objects that have references to it (when you remove a blog post for instance, you might want to delete comments as well).
- `PROTECT`: Forbid the deletion of the referenced object. To delete it, you will have to delete all objects that reference it manually. SQL equivalent: `RESTRICT`.
- `RESTRICT`: Similar behavior as PROTECT that matches SQL's `RESTRICT` more accurately.
- `SET_NULL`: Set the reference to NULL (requires the field to be nullable). For instance, when you delete a User, you might want to keep the comments he posted on blog posts, but say it was posted by an anonymous (or deleted) user. SQL equivalent: `SET NULL`.
- `SET_DEFAULT`: Set the default value. SQL equivalent: `SET DEFAULT`.
- `SET()`: Set a given value. This one is not part of the SQL standard and is entirely handled by Django.
- `DO_NOTHING`: Probably a very bad idea since this would create integrity issues in your database (referencing an object that actually doesn't exist). SQL equivalent: `NO ACTION`.

### `reverse` and `get_absolute_url`

`reverse` function takes an url name and gives the actual url. If the URL accepts arguments, you may pass them in `args`. For example:

```python
from django.urls import reverse

def myview(request):
    return HttpResponseRedirect(reverse('arch-summary', args=[1945]))
```

You can also pass `kwargs` instead of `args`. For example:

```python
reverse('admin:app_list', kwargs={'app_label': 'auth'})
```

Note that `args` and `kwargs` cannot be passed to `reverse()` at the same time.

## Using templates

It is suggested to keep all your templates in a single directory. First, create a directory called `templates`. Now, let's tell Django to look for templates in this directory. In `settings.py` file, modify `TEMPLATES` section this way:

```python
TEMPLATES = [
    {
        ...
        "DIRS": [BASE_DIR / "templates"],
        ...
    },
]
```

Let's create our first template. Create a file called `index.html`:

```html
<html>
  <head>
    <title>Django blog</title>
  </head>
  <body>
    <header>
      <h1><a href="{% url 'home' %}">Django blog</a></h1>
    </header>
    <div>
      {% block content %}
      {% endblock content %}
    </div>
  </body>
</html>
```

If you pay attention to a tag, you see `{% url 'home' %}`. These are called Django template tags. You will be introduced to many tags in future. Between `{% %}` you can write python code like if statements, loops and so on.

Let's talk about url template tag. This template tag finds a url name in `urls.py` and returns it. You can also pass parameters to urls this way: `{% url 'app-views-client' client.id %}`

## Extending templates

If you have some sort of code that is being repeated in many pages, for example website's header and navbar, you can write a tempelate and use it in other templates. This is called extending. Look at this example:

```html
{% extends "base.html" %}

{% block content %}
{% for post in post_list %}
  <div class="post-entry">
    <h2><a href="">{{ post.title }}</a></h2>
    <p>{{ post.body }}</p>
  </div>
{% endfor %}
{% endblock content %}
```

## Generic Views

Django has created a bunch of reusable view classes called generic view classes. According to Django's documentation, generic views take certain common idioms and patterns found in view development and abstract them so that you can quickly write common views of data without having to write too much code. Let's get introduced to a few generic view classes:

### ListView

This is used if you have to show a list of database models in a page. You should pass a template name and a model. This model is the model you want to fetch its database records. All records will be returned through a context object. For example, if you have chosen to use Post model, in the template you have to all Posts using `post_list` context object.

```python
from django.views.generic import ListView
from .models import Post


class BlogListView(ListView):
    model = Post
    template_name = "home.html"
```

And this is `home.html` template file:

```html
{% extends "base.html" %}

{% block content %}
{% for post in post_list %}
  <div class="post-entry">
    <h2><a href="">{{ post.title }}</a></h2>
    <p>{{ post.body }}</p>
  </div>
{% endfor %}
{% endblock content %}
```

You can also change context object name by passing `context_object_name` in view class:

```python
from django.views.generic import ListView
from .models import Post


class HomePageView(ListView):
  model = Post
  template_name = 'home.html'
  context_object_name = 'all_posts_list'
```

### DetailView

If you want to show all information about a single database record in a single page, you may want to use `DetailView`. This view expects you to pass template name and the model and the context object name is the model's name by default:

```python
from django.views.generic import DetailView
from .models import Post


class BlogDetailView(DetailView):
    model = Post
    template_name = "post_detail.html"
```

And this is how your template may look like:

```html
{% block content %}
<div class="post-entry">
  <h2>{{ post.title }}</h2>
  <p>{{ post.body }}</p>
</div>
{% endblock content %}
```

## URLs with parameters

Remember how we defined the post model?

```python
from django.db import models
from django.urls import reverse


class Post(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(
        "auth.User",
        on_delete=models.CASCADE,
    )
    body = models.TextField()

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse("post_detail", kwargs={"pk": self.pk})
```

Now, how to add a URL related to this model so that we can access to one record using a URL? Look at this example:

```python
path("post/<int:pk>/", BlogDetailView.as_view(), name="post_detail")
```

`pk` is a URL parameter and its type is integer.

## Using static files

Images, css files, js scripts and fonts in our website are referred as static files. If you want to use static file in you Django website, first, you have to create a directory in root called `static` or whatever you want. Now let's tell Django where to look for static files:

```python
# django_project/settings.py

STATIC_URL = "/static/"
STATICFILES_DIRS = [BASE_DIR / "static"]
```

Now let's create a css file in this directory and use it in our templates:

```css
/* static/css/base.css */

header h1 a {
  color: red;
}
```

To use static files in a template, first load them and then use:

```html
{% load static %}
<html>
  <head>
    <title>Django blog</title>
    <link rel="stylesheet" href="{% static 'css/base.css' %}">
  </head>
</html>
```

## Test Case

Earlier we said if we are going to perform database related tests, we should use `TestCase`. First, we should set up test data. Then we add this test data to temp database and check if we get the same values back:

```python
from django.contrib.auth import get_user_model
from django.test import TestCase

from .models import Post


class BlogTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.user = get_user_model().objects.create_user(
            username="testuser", email="test@email.com", password="secret"
        )

        cls.post = Post.objects.create(
            title="A good title",
            body="Nice body content",
            author=cls.user,
        )

    def test_post_model(self):
        self.assertEqual(self.post.title, "A good title")
        self.assertEqual(self.post.body, "Nice body content")
        self.assertEqual(self.post.author.username, "testuser")
        self.assertEqual(str(self.post), "A good title")
        self.assertEqual(self.post.get_absolute_url(), "/post/1/")
```