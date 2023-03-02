# Making queries

According to Django's official documentation, once you’ve created your data models, Django automatically gives you a database-abstraction API that lets you create, retrieve, update and delete objects. In this article, we are just trying to get to know this API, but as always for further information, visit [Django's official documentation](https://docs.djangoproject.com/en/4.1/topics/db/queries/) on how to use models' API.

To perform an __INSERT__ on a model, you should call `save()` method. Creating an instance of model will not call `save()` method automatically.

```python
from blog.models import Blog
b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
b.save()
```

__get_absolute_url()__: Define a `get_absolute_url()` method to tell Django how to calculate the URL for a specific object.

```python
def get_absolute_url(self):
    return "/people/%i/" % self.id
```

Also, you can use `reverse()` function to calculate URLs professionally which will be discussed later.

```python
def get_absolute_url(self):
    from django.urls import reverse
    return reverse('people-detail', kwargs={'pk' : self.pk})
```
