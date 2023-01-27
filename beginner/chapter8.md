# Chapter 8

In this chapter we are going to learn how to create custom user models for our website using Django. Using a custom user model for you projects in highly recommended as if later decide to add additional fields to your custom user model, it can be simply done. Although, if you stick with Django's default user model, it would be a disaster to customize Django's default user model. I have provided two tutorials on how to use custom user models in Django. You may need to read both of them carefully to understand what is going on.

## First Tutorial

Source code for this project is available at [this link](https://github.com/thisisjab/django_custom_user_model).

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

Ok! Let's talk about `Meta` class. According to [geeksforgeeks](https://www.geeksforgeeks.org/meta-class-in-models-django):
>Model `Meta` is basically the inner class of your model class. Model `Meta` is basically used to change the behavior of your model fields like changing order options, verbose_name, and a lot of other options. It's completely optional to add a `Meta` class to your model.

`CustomUser` model contains all the fields of the default User model and our additional age field which we set. We have many default fields including *username*, *password*, *email*, *groups*, and so on; Though, as you have probably seen, only username, password and email are asked when a user tries to sign up.

Now let's configure admin settings in the `accounts` app and move on:

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin

from .forms import CustomUserCreationForm, CustomUserChangeForm
from .models import CustomUser


class CustomUserAdmin(UserAdmin):
    add_form = CustomUserCreationForm
    form = CustomUserChangeForm
    model = CustomUser
    list_display = [
        "email",
        "username",
        "age",
        "is_staff",
    ]
    fieldsets = UserAdmin.fieldsets + ((None, {"fields": ("age",)}),)
    add_fieldsets = UserAdmin.add_fieldsets + ((None, {"fields": ("age",)}),)


admin.site.register(CustomUser, CustomUserAdmin)
```

Now it's time to do migrations, create a superuser and boom! Run the server and navigate to admin panel.

## Second Tutorial

This tutorial is based on an article on [test driven](testdriven.io) website. I added some additional content, too.

First step is to start a Django a project and an app called `users` for example. Now add the app to settings:

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "users",
]
```

We need to use a custom manager. What is manager in Django? According to Django's official documentation, a `Manager` is the interface through which database query operations are provided to Django models. At least one `Manager` exists for every model in a Django application. Create a *managers.py* file inside `users` app:

```python
from django.contrib.auth.base_user import BaseUserManager
from django.utils.translation import gettext_lazy as _


class CustomUserManager(BaseUserManager):
    """
    Custom user model manager where email is the unique identifiers
    for authentication instead of usernames.
    """
    def create_user(self, email, password, **extra_fields):
        """
        Create and save a user with the given email and password.
        """
        if not email:
            raise ValueError(_("The Email must be set"))
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_superuser(self, email, password, **extra_fields):
        """
        Create and save a SuperUser with the given email and password.
        """
        extra_fields.setdefault("is_staff", True)
        extra_fields.setdefault("is_superuser", True)
        extra_fields.setdefault("is_active", True)

        if extra_fields.get("is_staff") is not True:
            raise ValueError(_("Superuser must have is_staff=True."))
        if extra_fields.get("is_superuser") is not True:
            raise ValueError(_("Superuser must have is_superuser=True."))
        return self.create_user(email, password, **extra_fields)
```

Here, we created a custom manager and told Django how to create our new users and superusers.

### User Model

Now, it's time to choose whether you want to use `AbstractUser` or `AbstractBaseUser`. We have already talked about how these two classes differ from each other in first tutorial, but here I will show you how to use each of them.

Suppose I want to use `AbstractUser`:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models
from django.utils.translation import gettext_lazy as _

from .managers import CustomUserManager


class CustomUser(AbstractUser):
    username = None
    email = models.EmailField(_("email address"), unique=True)

    USERNAME_FIELD = "email"
    REQUIRED_FIELDS = []

    objects = CustomUserManager()

    def __str__(self):
        return self.email
```

In third line of this code we have imported `gettext_lazi` from `django.utils.translation`. In simple words this function is only used to translate the text in case one day you decided to create a multilingual website. If you remove this import and its usage in the code, nothing important will happen.

In `CustomUser` model I removed `username` field and added `email` as its unique identifier. Also, we specified that all objects for the class come from the `CustomUserManager`.

If you wanted to use `AbstractBaseUser`, the code would look like this:

```python
from django.contrib.auth.models import AbstractBaseUser, PermissionsMixin
from django.db import models
from django.utils import timezone
from django.utils.translation import gettext_lazy as _

from .managers import CustomUserManager


class CustomUser(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(_("email address"), unique=True)
    is_staff = models.BooleanField(default=False)
    is_active = models.BooleanField(default=True)
    date_joined = models.DateTimeField(default=timezone.now)

    USERNAME_FIELD = "email"
    REQUIRED_FIELDS = []

    objects = CustomUserManager()

    def __str__(self):
        return self.email
```

Comparing codes for both `AbstractUser` and `AbstractBaseUser` shows that one of the major differences is the inheritance of `PermissionsMixin`. If you add the `mixin` to one of your models, it will add fields that are specific for objects that have permissions, like is_superuser, groups, and user_permissions.

Ok. Let's tell Django to use our newly created model for authentication. Edit project's *settings.py*:

```python
AUTH_USER_MODEL = "users.CustomUser"
```

At this time, we need to set up the required forms. In `users` app, create a file called *forms.py*:

```python
from django.contrib.auth.forms import UserCreationForm, UserChangeForm

from .models import CustomUser


class CustomUserCreationForm(UserCreationForm):

    class Meta:
        model = CustomUser
        fields = ("email",)


class CustomUserChangeForm(UserChangeForm):

    class Meta:
        model = CustomUser
        fields = ("email",)
```

Let's require Django to use these forms by setting `users` admin file:

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin

from .forms import CustomUserCreationForm, CustomUserChangeForm
from .models import CustomUser


class CustomUserAdmin(UserAdmin):
    add_form = CustomUserCreationForm
    form = CustomUserChangeForm
    model = CustomUser
    list_display = ("email", "is_staff", "is_active",)
    list_filter = ("email", "is_staff", "is_active",)
    fieldsets = (
        (None, {"fields": ("email", "password")}),
        ("Permissions", {"fields": ("is_staff", "is_active", "groups", "user_permissions")}),
    )
    add_fieldsets = (
        (None, {
            "classes": ("wide",),
            "fields": (
                "email", "password1", "password2", "is_staff",
                "is_active", "groups", "user_permissions"
            )}
        ),
    )
    search_fields = ("email",)
    ordering = ("email",)


admin.site.register(CustomUser, CustomUserAdmin)
```

You may wonder what `fieldsets` and `add_fieldsets` are. We use these to add your custom fields to `fieldsets` (for fields to be used in editing users) and to `add_fieldsets` (for fields to be used when creating a user). __I will add more information on each of variables and fields we have used in the code, in the future__. For now, stick with these tiny and shallow data.

Now, we are done! That was it.
