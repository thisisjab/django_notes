# Chapter 9

In previous chapter, we learned how to create a custom user model. This time we are going to add login, signup, and logout functionalities to our website.

## Templates Configuration

First, let's set up our *template* folder. Create a directory in project's source folder and inside that, create another folder called *registration*. In project's settings, change templates settings like so:

```python
TEMPLATES = [
    {
        ...
        "DIRS": [BASE_DIR / "templates"],
        ...
    }
]
```

Now, it's time to create our essential templates:

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>{% block title %}Newspaper App{% endblock title %}</title>
</head>
<body>
  <main>
    {% block content %}
    {% endblock content %}
  </main>
</body>
</html>
```

```html
<!-- templates/home.html -->
{% extends "base.html" %}

{% block title %}Home{% endblock title %}

{% block content %}
{% if user.is_authenticated %}
  Hi {{ user.username }}!
  <p><a href="{% url 'logout' %}">Log Out</a></p>
{% else %}
  <p>You are not logged in</p>
  <a href="{% url 'login' %}">Log In</a> |
  <a href="{% url 'signup' %}">Sign Up</a>
{% endif %}
{% endblock content %}
```

```html
<!-- templates/registration/login.html -->
{% extends "base.html" %}

{% block title %}Log In{% endblock title %}

{% block content %}
<h2>Log In</h2>
<form method="post">{% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Log In</button>
</form>
{% endblock content %}
```

```html
<!-- templates/registration/signup.html -->
{% extends "base.html" %}

{% block title %}Sign Up{% endblock title %}

{% block content %}
<h2>Sign Up</h2>
<form method="post">{% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Sign Up</button>
</form>
{% endblock content %}
```

Phew! We are done.

## URLs

First, let's tell Django where to redirect user after login and logout process finished successfully:

```python
LOGIN_REDIRECT_URL = "home"
LOGOUT_REDIRECT_URL = "home"
```

Now, it's time to configure project's URLs.

```python
from django.contrib import admin
from django.urls import path, include
from django.views.generic.base import TemplateView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("accounts/", include("accounts.urls")),
    path("accounts/", include("django.contrib.auth.urls")),
    path("", TemplateView.as_view(template_name="home.html"),
      name="home"),
]
```

One of the tips that I want to mention is the placement of *accounts* URL. As you see we have placed our app at first since Django looks for URLs from top to down. Also, as I wanted to show home page and I didn't want to create a `pages` app yet, I used a shortcut.

For our `accounts` app, configure URLs this way:

```python
from django.urls import path
from .views import SignUpView

urlpatterns = [
    path("signup/", SignUpView.as_view(), name="signup"),
]
```

Now, let's create a view for our signup page in `accounts` app:

```python
from django.urls import reverse_lazy
from django.views.generic import CreateView

from .forms import CustomUserCreationForm


class SignUpView(CreateView):
    form_class = CustomUserCreationForm
    success_url = reverse_lazy('login')
    template_name = "registration/signup.html"
```
