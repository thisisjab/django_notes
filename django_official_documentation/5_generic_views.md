# Generic Views

Generic view are introduced by Django to help you with tedious and repetitive (monotonous) tasks. For example, showing a list of items, displaying detail of a specific item, signing up, logging in, etc. are common views that Django has created pre-coded views for them. You can also create your own customized views.

## Base Views

According to Django, three classes provide much of the functionality needed to create Django views. These are `View`, `TemplateView`, and `RedirectView`. You may think of them as parent views, which can be used by themselves or inherited from. They may not provide all the capabilities required for projects, in which case there are Mixins and Generic class-based views.

*What are mixins*? Mixins are a way of reusing view logic. A Mixin is a special kind of inheritance in Python to allow classes to share methods between any class. Django has several built-in mixins that can be plugged into any class to provide additional functionality such as `LoginRequiredMixin`. If you don't understand what mixins really are, don't worry. They will be discussed later.

Let's talk about our base views.

### View Class

The base view class. All other class-based views inherit from this base class. It isn’t strictly a generic view and thus can also be imported from `django.views`. Example:

```python
from django.http import HttpResponse
from django.views import View


class MyView(View):
    # `http_method_name`: allowed http methods
    # Defaults to ['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']
    http_method_names = ['get']

    def get(self, request, *args, **kwargs):
        # `kwargs` are all the url parameters
        return HttpResponse('Hello ' + kwargs.get("username"))
```

### TemplateView Class

Renders a given template, with the context containing parameters captured in the URL.

This view inherits from three other views:

1) `TemplateResponseMixin`: Provides a mechanism to construct a `TemplateResponse`, given suitable context. The template to use is configurable and can be further customized by subclasses. This mixin has a number of attributes like *template_name*, *template_engine*, etc. and set of methods like *get_template_names()*.
2) `ContextMixin`: This mixin is used when dealing with context. It has a very useful method called `get_context_data()` which is used return template context.
3) `View`: Each `TemplateView` is actually a view. Right?

### RedirectView Class

Though its name is pretty self-explanatory, this class is used to redirect user to another page.

This view only inherits from `View` class. There are several ways that you can use this class. For example:

```python
from django.urls import path
from django.views.generic import RedirectView

from . import views

urlpatterns = [
    path('', RedirectView.as_view(pattern_name='register-personal'),
         name='register'),
    path('youtube_link', RedirectView.as_view(url="https://youtube.com")),
    path('personal/', views.personal, name='register-personal'),
    path('professional/', views.professional, name='register-professional'),
    path('subscriptions/', views.subscriptions, name='register-subscriptions'),
]
```

Also, you can use it as an actual view that does something:

```python
from django.shortcuts import get_object_or_404
from django.views.generic.base import RedirectView

from articles.models import Article

class ArticleCounterRedirectView(RedirectView):

    permanent = False
    query_string = True
    pattern_name = 'article-detail'

    def get_redirect_url(self, *args, **kwargs):
        article = get_object_or_404(Article, pk=kwargs['pk'])
        article.update_counter()
        return super().get_redirect_url(*args, **kwargs)
```

*urls.py*:

```python
from django.urls import path
from django.views.generic.base import RedirectView

from article.views import ArticleCounterRedirectView, ArticleDetailView

urlpatterns = [
    path('counter/<int:pk>/', ArticleCounterRedirectView.as_view(), name='article-counter'),
    path('details/<int:pk>/', ArticleDetailView.as_view(), name='article-detail'),
    path('go-to-django/', RedirectView.as_view(url='https://www.djangoproject.com/'), name='go-to-django'),
]
```

Let's talk about `permanent` option in `RedirectView`. The only difference here is the HTTP status code returned. If True, then the redirect will use status code 301. If False, then the redirect will use status code 302. By default, permanent is False.
