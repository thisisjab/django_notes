# Chapter 2: Hello World App
Django uses MVT pattern which is kind of a customized version of traditional MVC pattern. Although naming MVT as MVTU is more accurate, but we will stick with MVT. Let's see what MVT pattern is.
- **Model:** Manages data and core business logic
- **View:** Describes which data is sent to the user but not its presentation
- **Template:** Presents the data as HTML with optional CSS, JavaScript, and Static Assets
- **URL Configuration:** Regular-expression components configured to a View


You may want to take a look at this short paragraph of Django For Beginners book which explains how user requests are handled in general.
> This interaction is fundamental to Django yet very confusing to newcomers so let’s map out the order of a given HTTP request/response cycle. When you type in a URL, such as https://djangoproject.com, the first thing that happens within our Django project is a URL pattern (contained in urls.py) is found that matches it. The URL pattern is linked to a single view (contained in views.py) which combines the data from the model (stored in models.py) and the styling from a template (any file ending in .html). The view then returns a HTTP response to the user.

## Create an app
Remember that each Django project is a container for several apps. Each app is related to a one specific and isolated functionallity. For example, think about an e-commerce website. This website consists of several apps like:
- accounts
- products
- blog

Each app will have its own models, views and url configuration. Also, you will have a one project-level url configuration file.

You can create apps by running `python manage.py startapp accounts`. Django will not know about the you have created unless you add it explicitly. This is done by adding this item to `INSTALLED_APPS` in `settings.py` file: `accounts.apps.Accounts`.

To return static Http responses you may want to use `HttpResponse` object which is a part of `django.http` package.

There are two types of views in Django:
### Function-based view
This is the first type of views that Django started working with. They are easy to use.
```
def home_page_view(request):
    return HttpResponse("Hello, World!")
```
### Class-based views
These type of views give you a number of powerful options, but they may seem hard for beginners.
### Generic class-based views
These are a several built-in classes for you handling common use cases such as crud.

## Url configuration for a function-based view
Import `path` from `django.urls` and your function-based view and configure `urlpatterns` inside your app's urls file.
```
urlpatterns = [
    path("/home", function_based_view, name="HomeURL")
]
```

The `name` parameter is optional and will be used as a reference name to this url pattern.

As we said earlier, Django has a project-level url configuration. Let's see what to do in this file. First, import `path` and `include` from `django.urls`.
```
urlpatterns = [
    path("", include("YouApp.urls"))
]
```
