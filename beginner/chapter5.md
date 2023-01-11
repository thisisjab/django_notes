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
