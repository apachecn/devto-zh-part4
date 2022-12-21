# Django UserContextMixin

> 原文：<https://dev.to/serhatteker/django-usercontextmixin-5cak>

如果你想得到当前的`logged-in user`并使用它——比如在每个模板的顶部，在基于类的视图中可能很难实现。

然而，有一种简单的 python/djangoic 方法可以实现这一点:只需使用一个`Mixin`。实际上`Django`框架由非常广泛的`Mixins`组成，如`SingleObjectMixin`或`TemplateResponseMixin`。更多细节: [Django 基于类的混合](https://docs.djangoproject.com/en/2.2/ref/class-based-views/mixins/)。

所以现在我们可以编写自己的`Mixin`来完成这项工作:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- 
from django.views.generic.base import ContextMixin

class UserContextMixin(ContextMixin):
    """Get current `User` from request and add it to context"""

    def get_context_data(self, **kwargs):
        # Call the base implementation first to get a context
        context = super().get_context_data(**kwargs)
        user = self.request.user
        context['user'] = user

        return context 
```

Enter fullscreen mode Exit fullscreen mode

上面我们从`built-in` `ContextMixin`类继承了我们的`Mixin`，可以在下面检查:

```
class ContextMixin:
    """
    A default context mixin that passes the keyword arguments received by
    get_context_data() as the template context.
    """
    extra_context = None

    def get_context_data(self, **kwargs):
        if 'view' not in kwargs:
            kwargs['view'] = self
        if self.extra_context is not None:
            kwargs.update(self.extra_context)
        return kwargs 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以像下面这样在你的`Class-based view`里继承:

```
from .utils.views import UserContextMixin

class OrderCreateView(UserContextMixin, CreateView):
    template_name = "template.html"
    success_url = reverse_lazy("product:order")
    form_class = OrderModelForm
    extra_context = {'some_detail': 'Some Other Info'}

    … 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你可以在视图的其他属性中使用你的`user context`。您也可以在您的模板中访问它，如下:

```
{% extends "app/base_site.html" %}
{% load staticfiles %}

{% block title %} Order {% endblock title %}

{% block content %}
…

User : {{ user }}
User Name : {{ user.name }}

…

{% endblock content %} 
```

Enter fullscreen mode Exit fullscreen mode

***亲提示*** :总是在`Class-based View`类之前继承你的`Mixin`。

全部完成！