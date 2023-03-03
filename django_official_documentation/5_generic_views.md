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
