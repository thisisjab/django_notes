# Chapter 10

In this chapter, you will be introduced to `crispy-forms` for Django. As we don't know how to design custom forms in a template, we are going to use this module to give our forms a Boostrap feel.

You already know how to use static files and you can simply use online or offline version of Bootstrap and you can use it for instance to add classes to a submit button. Let's talk about `crispy-forms`. `django-crispy-forms` is an application that helps to manage Django forms. It allows adjusting forms' properties (such as method, send button or CSS classes) on the backend without having to re-write them in the template. 

First step is to install `django-crispy-forms`:

```shell
python -m pip install django-crispy-forms==1.13.0
python -m pip install crispy-bootstrap5==0.6
```

Note that the second package is Bootstrap 5 template pack for `django-crispy-forms`.

Let's add the installed modules to Django app:

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "crispy_forms",
    "crispy_bootstrap5",
]
```

In project settings, add these two lines to configure crispy forms:

```python
CRISPY_ALLOWED_TEMPLATE_PACKS = "bootstrap5"
CRISPY_TEMPLATE_PACK = "bootstrap5"
```

From now on, whenever you want to add a form, do it the crispy way like so:

```python
{% extends "base.html" %}
{% load crispy_forms_tags %}
{% block title %}Sign Up{% endblock title%}

{% block content %}
<h2>Sign Up</h2>
<form method="post">{% csrf_token %}
    {{ form|crispy }}
    <button class="btn btn-success" type="submit">Sign Up</button>
  </form>
{% endblock content %}
```
