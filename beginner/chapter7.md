# Chapter 7

Since create a proper and systematic user authenticating system is a tough job, in this chapter we are going to use Django's built-in authenticating system.

Every time a Django project is created, *auth* app is installed as well. Auth app will provide a user object with following fields:

- username
- password
- email
- first_name
- last_name

## LoginView

As we have learned till now, Django provides many views for common use cases. First, we need to update the project's url settings:

```python
from django.urls import path, include

urlpatterns = [
    ...
    path("accounts/", include("django.contrib.auth.urls")),
    ...
]
```

Next step is to create the related template file. As the `LoginView` documentation notes, by default Django will look within a `templates` directory called `registration` for a file called login.html for a login form. So we need to create a new directory called `registration` and the requisite file within it.

```html
{% extends "base.html" %}

{% block content %}
<h2>Log In</h2>
<form method="post">{% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Log In</button>
</form>
{% endblock content %}
```

Last step is to tell Django where to redirect user when login is completed. Add this line to your `setting.py`:

```python
LOGIN_REDIRECT_URL = "home"
```

You can check whether user is logged in or not in your home page template:

```html
{% if user.is_authenticated %}
    <p>Hi {{ user.username }}!</p>
{% else %}
    <p>You are not logged in.</p>
    <a href="{% url 'login' %}">Log In</a>
{% endif %}
```

Furthermore, you can create logout link just by setting logout redirect URL:

```python
LOGOUT_REDIRECT_URL = "home"
```

## User signup

To implement a user signup functionality, we have to create an app called `accounts`. Now, let's configure the url settings of the project:

```python
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("accounts/", include("django.contrib.auth.urls")),
    path("accounts/", include("accounts.urls")),
    ...
]
```

Note that the order of `urlpattern` paths matter. Django will read this file from top to bottom and if it does not find one, then it will proceed to another.

Let's create the required signup view:

```python
from django.contrib.auth.forms import UserCreationForm
from django.urls import reverse_lazy
from django.views.generic import CreateView


class SignUpView(CreateView):
    form_class = UserCreationForm
    success_url = reverse_lazy("login")
    template_name = "registration/signup.html"
```

Why use `reverse_lazy` here instead of `reverse`? The reason is that for all generic class-based views the URLs are not loaded when the file is imported, so we have to use the lazy form of reverse to load them later when they’re available.

Let's create the template file as well:

```python
{% extends "base.html" %}

{% block content %}
<h2>Sign Up</h2>
<form method="post">{% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Sign Up</button>
</form>
{% endblock content %}
```

And finally let's set up the URLs in account's app URL settings:

```python
from django.urls import path
from .views import SignUpView

urlpatterns = [
    path("signup/", SignUpView.as_view(), name="signup"),
]
```
