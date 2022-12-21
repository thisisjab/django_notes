# Chapter 3
In this chapter we are going to learn how to use class-based views and templates.

According to the book, templates are individual HTML files that can be linked together and also include basic logic.

First thing to consider is to find a place to keep our templates. We've got two options. By default Djagno's template loader finds each app's default folder. Then looks for a templates folder. Inside templates folder looks for app's folder. Then starts to find html or template files. This approach is tedious and repetitive.

Another approach is to have a single project-level directory and place all templates there and tell django to look for templates within that folder. Let's see how.

First, create a directory called `templates` inside project base directory. Now, update `TEMPLATES` inside `settings.py`.
```
TEMPLATES = [
    {
        ...
        "DIRS": [BASE_DIR / "templates"],
        ...
    },
]
```
Now, add all of your html files inside templates directory.

As we discussed earlier, generic class-based views are created to save us from writing views for common use cases. Now, let's create a view using a `TemplateView` which is used to show a html page.

In your app's views file, import `TemplateView` from `django.views.generic` and create a class which inherits `TemplateView`.
```
from django.views.generic import TemplateView


class HomePageView(TemplateView):
    template_name = "home.html"
```
When configuring urls of the app, note that you should use `ClassName.as_view()`.
```
urlpatterns = [
    path("", HomePageView.as_view(), name="home"),
]
```
## Adding links to other pages
Remember name argument when adding urls to each app's urls file? You can use that name to find the url to that specific page. Look:
```
<header>
  <a href="{% url 'home' %}">Home</a> |
  <a href="{% url 'about' %}">About</a>
</header>
```
## Extending Templates
Templates can be extended. For example you can have a single `header.html` file which has been extended by a few templates. In this case all those classes will share that piece of code inside `header.html`.

Let's define a `header.html` file as a base template:
```
<header>
  <a href="{% url 'home' %}">Home</a> |
  <a href="{% url 'about' %}">About</a>
</header>

{% block content %} {% endblock content %}
```

Now every other template can inherit from this template like:
```
{% extends "base.html" %}

{% block content %}
<h1>Homepage</h1>
{% endblock content %}
```
## Writing Unit Tests
Although you can use python's *unittest* module for writing unit tests, but Django itself provides some default classes for writing tests. First, read this part which is taken from the book:
> Django’s own testing framework provides several extensions on top of Python’s unittest.`TestCase` base class. These include a test client for making dummy Web browser requests, a number of Django-specific additional assertions, and four test case classes: `SimpleTestCase`, `TestCase`, `TransactionTestCase`, and `LiveServerTestCase`. >

> Generally speaking, `SimpleTestCase` is used when a database is not necessary while `TestCase` is used when you do want to test the database. `TransactionTestCase` is useful if you need to directly test database transactions while `LiveServerTestCase` launches a live server thread useful for testing with browser-based tools like Selenium. >

To write tests we will use `tests.py` file which is included with every single Django app.

First, let's check if url exists and returns a 200 http response.
```
class HomepageTests(SimpleTestCase):

    def test_url_exists_at_correct_location(self):
        response = self.client.get("/")
        self.assertEqual(response.status_code, 200)
```
Now let's check if url is accessible by its name:
```
def test_url_available_by_name(self):
        response = self.client.get(reverse("home"))
        self.assertEqual(response.status_code, 200)
```
Also we can check if the right template is used and some content is inside a page:
```
def test_template_name_correct(self):
        response = self.client.get(reverse("home"))
        self.assertTemplateUsed(response, "home.html")

    def test_template_content(self):
        response = self.client.get(reverse("home"))
        self.assertContains(response, "<h1>Homepage</h1>")
```