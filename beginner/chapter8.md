# Chapter 8

In this chapter we are going to learn how to create custom user models for our website using Django. Using a custom user model for you projects in highly recommended as if later decide to add additional fields to your custom user model, it can be simply done. Although, if you stick with Django's default user model, it would be a disaster to customize Django's default user model. Source code for this project is available at [this link](https://github.com/thisisjab/django_custom_user_model).

First, create an app called `accounts` for example. Note that till now, you shouldn't have done any migrations as we are going to create our custom user model and then migrate.

Let's create our custom user model in `accounts` app and add any additional field as desired:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models


class CustomUser(AbstractUser):
    age = models.PositiveIntegerField(null=True, blank=True)
```

As you notice, we inherit from `AbstractUser` in our custom user model, and then we add any extra fields. Also, we could use `AbstractBaseUser` instead of `AbstractUser`, but as using `AbstractBaseUser` requires a very deep understanding of Django and makes things very complex for beginners, we simply use `AbstractUser`. Using `AbstractBaseUser` means rewriting the whole Django, but when you use `AbstractUser` you indicate that you are satisfied with Django's default fields and configuration, and you only need a few custom fields. Later we will discuss `AbstractBaseUser` as well.

Now, we have got to create required forms for this model. We need to create two forms: one for creating user and another for editing a user that's already been created. In `accounts` app, create a forms file and put these codes in it:

```python
from django.contrib.auth.forms import UserCreationForm, UserChangeForm
from .models import CustomUser


class CustomUserCreationForm(UserCreationForm):
    class Meta(UserCreationForm):
        model = CustomUser
        fields = UserCreationForm.Meta.fields + ("age",)


class CustomUserChangeForm(UserChangeForm):
    class Meta:
        model = CustomUser
        fields = UserChangeForm.Meta.fields
```
