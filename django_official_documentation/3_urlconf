# URLConf

To map our URLs to views, we use a URLConf module. This module is pure python and tells Django which URL is related to which view. You can name this module anything you want. You can reference other URLConf files since they are only python codes.

## What happens when you request a URL?

This is the whole algorithm that is executed to determine which python code to execute.

1. Django finds the URLConf file. Django will use the default URLConf if `ROOT_URLCONF` value is not changed inside *settings.py*. Also, if `HttpRequest` object has its own URLConf value, default URLConf will be ignored.
2. Now, Django looks for `urlpatterns` variables which are a list of `django.urls.path()` objects.
3. Having found the right `urlpatterns` variable, it's time to start from the first `django.urls.path()` object till the end of the list to find the first `django.urls.path()` that matches URL.
4. At this moment that we know which view to execute, Django will pass these arguments to that view or function: An instance of `HttpRequest`, `kwargs`, and on other that's not going to be explained here.
5. If no URL patterns matches, then an exception will be raised.

This is an example URLConf file:

```python
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```

Each `<type:name>` is a converter. There are several types of converters according to Django:

* `str` - Matches any non-empty string, excluding the path separator, '/'. This is the default if a converter isn’t included in the expression.
* `int` - Matches zero or any positive integer. Returns an int.
* `slug` - Matches any slug string consisting of ASCII letters or numbers, plus the hyphen and underscore characters. For example, *building-your-1st-django-site*.
* `uuid` - Matches a formatted UUID. To prevent multiple URLs from mapping to the same page, dashes must be included and letters must be lowercase. For example, 075194d3-6885-417e-a8a8-6c931e272f00. Returns a UUID instance.
* `path` - Matches any non-empty string, including the path separator, '/'. This allows you to match against a complete URL path rather than a segment of a URL path as with *str*.

Also, keep in mind that you should not add '/' before any URL since every URL by default does have it. So, `articles` is correct, but `/articles` is not.

Another thing to mention is that the URLConf doesn’t look at the request method. In other words, all request methods – __POST__, __GET__, __HEAD__, etc. – will be routed to the same function for the same URL.
