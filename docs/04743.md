# 类型检查 Django 内部和查询集

> 原文：<https://dev.to/wemake-services/typechecking-django-internals-and-querysets-1m47>

**最初发表于我的博客**:【https://sobolevn.me/2019/08/typechecking-django-and-drf T2】

正如你已经[已经](https://sobolevn.me/2019/02/python-exceptions-considered-an-antipattern) [知道](https://sobolevn.me/2018/07/real-python-contants)我[爱](https://sobolevn.me/2019/01/simple-dependent-types-in-python)随意静打字。问题是，有时这不是可有可无的，而是不可能的。因为在 Python 的生态系统中，我们有很多大型的非类型化项目。

Django 和 Django-Rest-Framework 就是其中的两个。曾经是。因为现在可以打字了！我来介绍一下 [TypedDjango](https://github.com/typeddjango) 的组织以及[`django`](https://github.com/typeddjango/django-stubs)[`drf`](https://github.com/typeddjango/djangorestframework-stubs)的存根。

这将是一个简明的教程和入门指南。

## 荣誉称号

我想对领导这个项目的 [@mkurnikov](https://github.com/mkurnikov) 以及所有为此做出贡献的人说声“谢谢”。你们都很牛逼！

## TLDR

在本文中，我将展示类型如何与`django`和`drf`一起工作。你可以在这里看一下[的结果。](https://github.com/sobolevn/django_stubs_example)

你也可以使用 [`wemake-django-template`](https://github.com/wemake-services/wemake-django-template) 来启动你的新项目，所有的东西都已经配置好了。它看起来与示例项目完全一样。

## 入门

在这个小教程中，我将向您展示`django-stubs`和`djangorestframework-stubs`的几个功能。我希望这能让你相信有人在你身后复查是一件好事。

您可以随时参考原始文档。所有的步骤也包括在内。

开始时，我们需要一个新项目和一个干净的虚拟环境，这样我们就可以安装我们的依赖项:T4

```
pip install django django-stubs mypy 
```

然后我们需要正确配置`mypy`。它可以分为两步。首先，我们配置`mypy`本身:

```
# setup.cfg [mypy]
# The mypy configurations: https://mypy.readthedocs.io/en/latest/config_file.html python_version = 3.7

check_untyped_defs = True
disallow_any_generics = True
disallow_untyped_calls = True
disallow_untyped_decorators = True
ignore_errors = False
ignore_missing_imports = True
implicit_reexport = False
strict_optional = True
strict_equality = True
no_implicit_optional = True
warn_unused_ignores = True
warn_redundant_casts = True
warn_unused_configs = True
warn_unreachable = True
warn_no_return = True 
```

然后我们配置`django-stubs`插件:

```
# setup.cfg [mypy]
# Appending to `mypy` section: plugins =
  mypy_django_plugin.main

[mypy.plugins.django-stubs]
django_settings_module = server.settings 
```

我们在这里做什么？

1.  我们添加了一个定制的`mypy` [插件](https://mypy.readthedocs.io/en/latest/extending_mypy.html)来帮助类型检查器在一些复杂的特定于 Django 的情况下猜测类型(比如模型、查询集、设置等)
2.  我们还为`django-stubs`添加了自定义配置，使其指向我们用于 Django 的设置。它将需要导入它。

最终结果可以在这里找到[。](https://github.com/sobolevn/django_stubs_example/blob/master/setup.cfg#L78)

我们现在已经安装并配置好了所有的东西。让我们打字检查东西！

## 类型检查视图

让我们从输入视图开始，因为这是使用这个插件最简单的事情。

下面是我们简单的基于函数的视图:

```
# server/apps/main/views.py from django.http import HttpRequest, HttpResponse
from django.shortcuts import render

def index(request: HttpRequest) -> HttpResponse:
    reveal_type(request.is_ajax)
    reveal_type(request.user)
    return render(request, 'main/index.html') 
```

让我们运行一下，看看它知道哪些类型。注意，我们可能需要修改`PYTHONPATH`，这样`mypy`就能够导入我们的项目:

```
» PYTHONPATH="$PYTHONPATH:$PWD" mypy server
server/apps/main/views.py:14: note: Revealed type is 'def () -> builtins.bool'
server/apps/main/views.py:15: note: Revealed type is 'django.contrib.auth.models.User' 
```

让我们试着打破一些东西:

```
# server/apps/main/views.py def index(request: HttpRequest) -> HttpResponse:
    return render(request.META, 'main/index.html') 
```

不，有一个错别字，`mypy`会发现它:

```
» PYTHONPATH="$PYTHONPATH:$PWD" mypy server
server/apps/main/views.py:18: error: Argument 1 to "render" has incompatible type "Dict[str, Any]"; expected "HttpRequest" 
```

有用！好吧，但这很直接。让我们把我们的例子变得复杂一点，创建一个定制模型来展示我们如何输入模型和查询集。

## 类型检查模型和查询集

姜戈的 ORM 是一个杀手锏。它非常灵活和动态。这也意味着很难打字。让我们来看看一些已经被`django-stubs`涵盖的功能。

我们的模型定义:

```
# server/apps/main/models.py from django.contrib.auth import get_user_model
from django.db import models

User = get_user_model()

class BlogPost(models.Model):
    author = models.ForeignKey(User, on_delete=models.CASCADE)

    text = models.TextField()

    is_published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self) -> str:
        reveal_type(self.id)  # example reveal of all fields in a model
        reveal_type(self.author)
        reveal_type(self.text)
        reveal_type(self.is_published)
        reveal_type(self.created_at)
        return '<BlogPost {0}>'.format(self.id) 
```

而这个模型的每一个领域都被`django-stubs`覆盖。来看看都透露了哪些类型:

```
» PYTHONPATH="$PYTHONPATH:$PWD" mypy server
server/apps/main/models.py:21: note: Revealed type is 'builtins.int*'
server/apps/main/models.py:22: note: Revealed type is 'django.contrib.auth.models.User*'
server/apps/main/models.py:23: note: Revealed type is 'builtins.str*'
server/apps/main/models.py:24: note: Revealed type is 'builtins.bool*'
server/apps/main/models.py:25: note: Revealed type is 'datetime.datetime*' 
```

一切看起来都很好！`django-stubs`提供一个定制的`mypy`插件，将模型字段转换成正确的实例类型。这就是为什么所有类型都被正确揭示。

`django-stubs`插件的第二大特点是我们可以输入`QuerySet` :

```
# server/apps/main/logic/repo.py from django.db.models.query import QuerySet

from server.apps.main.models import BlogPost

def published_posts() -> 'QuerySet[BlogPost]':  # works fine!
    return BlogPost.objects.filter(
        is_published=True,
    ) 
```

下面是检查的方法:

```
reveal_type(published_posts().first())
# => Union[server.apps.main.models.BlogPost*, None] 
```

我们甚至可以用`.values()`和`.values_list()`调用来注释 querysets。这个插件很智能！

几年来，我一直在与返回`QuerySet` s 的注释方法作斗争。这个特性为我解决了一个大问题:不再有`Iterable[BlogPost]`或`List[User]`。我现在可以使用真实类型了。

## 类型检查 API

但是，输入视图、模型、表单、命令、URL 和 admin 并不是我们所拥有的全部。TypedDjango 也有`djangorestframework`的类型。让我们安装并配置它:

```
pip install djangorestframework djangorestframework-stubs 
```

并且我们可以开始创建序列化器:

```
# server/apps/main/serializers.py from rest_framework import serializers

from server.apps.main.models import BlogPost, User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['username', 'email']

class BlogPostSerializer(serializers.HyperlinkedModelSerializer):
    author = UserSerializer()

    class Meta:
        model = BlogPost
        fields = ['author', 'text', 'is_published', 'created_at'] 
```

观点:

```
# server/apps/main/views.py from rest_framework import viewsets

from server.apps.main.serializers import BlogPostSerializer
from server.apps.main.models import BlogPost

class BlogPostViewset(viewsets.ModelViewSet):
    serializer_class = BlogPostSerializer
    queryset = BlogPost.objects.all() 
```

和路由器:

```
# server/apps/main/urls.py from django.urls import path, include
from rest_framework import routers

from server.apps.main.views import BlogPostViewset, index

router = routers.DefaultRouter()
router.register(r'posts', BlogPostViewset)

urlpatterns = [
    path('', include(router.urls)),
    # ... ] 
```

它甚至看起来不像发生了什么变化，但是里面的一切都是键入的:设置、序列化程序、视图集和路由器。它将允许你在最需要的地方增加输入。

我们试着把`queryset = BlogPost.objects.all()`
改成`queryset = [1, 2, 3]`在我们看来:

```
» PYTHONPATH="$PYTHONPATH:$PWD" mypy server
server/apps/main/views.py:25: error: Incompatible types in assignment (expression has type "List[int]", base class "GenericAPIView" defined the type as "Optional[QuerySet[Any]]") 
```

不行，没用的！修复您的代码！

## 结论

键入框架接口是一件很棒的事情。当与 [`returns`](https://github.com/dry-python/returns) 和 [`mappers`](https://github.com/dry-python/mappers) 等工具结合使用时，它将允许编写类型安全和声明性的业务逻辑，并将其包装到类型化的框架接口中。并且减少这两者之间的层中的错误数量。

可选的渐进静态类型还允许您快速启动，并且只有在 API 稳定时才添加类型，或者从一开始就进行类型驱动的开发。

但是，`django-stubs`和`djangorestframework-stubs`是新项目。仍然有很多 bug，计划的特性，缺失的类型规格。我们欢迎来自社区的每一份贡献，让 Python 中的开发人员工具变得真正令人敬畏。