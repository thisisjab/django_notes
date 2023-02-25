# Chapter 11

In the present chapter, we are going to complete the project's authorization part. We will add password change and reset functions.

As always, Django has done the job for us. We only need to create custom templates.

# Change Password

Login to your account in website. Then navigate to `http://127.0.0.1:8000/accounts/password_change/`. You will see that this page exists and works as well, but how to personalize it using crispy forms? Create a `pasword_chage_form.html` file inside `templates/registration`:

```python
{% extends "base.html" %}
{% load crispy_forms_tags %}

{% block title %}Password Change{% endblock title %}

{% block content %}
<h1>Password change</h1>
<p>Please enter your old password, for security's sake, and then enter
your new password twice so we can verify you typed it in correctly.</p>

<form method="POST">{% csrf_token %}
  {{ form|crispy }}
  <input class="btn btn-success" type="submit"
    value="Change my password">
</form>
{% endblock content %}
```

Also, you can create a `password_change_done.html` file as well.

# Password Reset

Password reset functionality is implemented as well, but we need to do a one single configuration and it's to tell Django how to send password reset link through email.Password reset handles the common case of users forgetting their passwords. The steps are very similar to configuring password change, as we just did. Django already provides a default implementation that we will use and then customize the templates so it matches the look and feel of the rest of our site.

The only configuration required is telling Django how to send emails. After all, a user can only reset a password if they have access to the email linked to the account. In production, we’ll use the email service *SendGrid* to actually send the emails but for testing purposes we can rely on Django’s console backend setting which outputs the email text to our command line console instead.

To set up email backend, add this line to the project's settings:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

Now navigate to `http://127.0.0.1:8000/accounts/password_reset/` to check this functionallity out. Note that no email is going to be sent to any email, instead the email will be shown in console.

For password reset, you can create these templates:

- `templates/registration/password_reset_form.html`
- `templates/registration/password_reset_done.html`
- `templates/registration/password_reset_confirm.html`
- `templates/registration/password_reset_complete.html`
