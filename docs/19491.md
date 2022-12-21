# 为你的 Django 应用程序构建一个简单的链接缩短器

> 原文：<https://dev.to/chris1610/building-a-simple-link-shortener-for-your-django-app-3apf>

作为我关于[滑动](https://slidezing.com)的工作的一部分，我想包括一个简单的链接缩短器，以便更容易分享真正长的 URL。本文将介绍为什么您可能想要这样做，以及如何在您现有的 django 应用程序中实现一个相对简单的方法。

对于本文，我假设您知道如何创建 django 应用程序的基础知识，并且您希望在现有的应用程序中包含这个缩写。

## 为什么要缩短一个环节？

Django 应用程序经常在模型定义中使用 slug 字段。slug 只是一个简短的名字，在 url 中使用，给它一个友好的名字。关于如何使用[废料场](https://docs.djangoproject.com/en/2.2/ref/models/fields/#slugfield)的更多信息，请参考官方文件。

例如，如果您正在创建一个博客，您可能有一个博客的标题字段，但是您还希望 url 中的描述能够唯一地标识博客文章。

对于一个帖子，我的 slug 可能是“intro-to-django-class-based-views”,
将导致一个完整的 url，看起来像这样:
【http://acoolblog.com/blog/intro-to-django-class-based-views】T2

这种方法很好，因为 url 是描述性的，告诉我们一些关于博客文章的信息，甚至不用阅读它。这种方法的缺点是它是一个非常长的 url。通过 twitter 或其他渠道分享时，越短越好。

一种常见的方法是使用第三方服务，如 bitly 或 tinyurl，将长链接转换为更紧凑的链接。这种方法没有错。然而，我想提高品牌知名度，所以我保持所有的网址通过同一个应用程序。既然相对容易做到，为什么不试一试呢？

我还会给出一些额外的警告。这种链接缩短方法不需要任何任意的 url 并将其缩短。它基于与每个 django 对象相关联的惟一 id。这意味着，如果你只是想要博客文章的短链接，这将非常有用。如果你想要图片、评论和其他页面的短链接，你需要修改这种方法。

我想提出的另一个警告是，这不适用于任何类型的安全性。运行这种算法并尝试“猜测”URL 是非常容易的。我假设这些链接都是公开的，任何试图猜测短网址的行为都不会对你的网站有害。

## 启动代码

这段代码在很大程度上基于 [django short-urls](https://github.com/bfirsh/django-shorturls) ,但为了便于集成到你的应用程序中，做了一些删减。

对于实际的缩短算法，我们将使用一个名为 [short_url](https://pypi.org/project/short_url/) 的 python 模块。

请确保使用 pip:
进行安装

```
pip install short_url 
```

值得一试，这样你就可以看到它做了什么:

```
import short_url
print(short_url.encode_url(2))

25t25

print(short_url.encode_url(3))

ghpzy 
```

在最简单的层面上，short_url 使用一种算法将任意数字转换成一系列字母。该算法是确定性的，这意味着每次对值 2 进行编码，都会得到相同的结果。

由于算法总是计算出相同的结果，我们也可以解码一个文本串，得到一个数:

```
print(short_url.decode_url('25t52'))

2

print(short_url.decode_url('ghpzy'))

3 
```

这个算法的另一个好的方面是，某些字母和数字被排除在外，这样人们就不会混淆它是 0 还是 0。

既然我们已经知道了创建唯一 id 的基础知识，我们可以将它整合到现有的 django 应用程序中，只需做一点额外的工作。

### 与姜戈结合

我将创建一个包含 urls.py、views.py 文件的`shorturl`应用程序。

下面是我的 shorturl 应用程序中的示例 urls.py 文件:

```
from django.urls import path

from .views import redirect_view

urlpatterns = [path('<slug:tiny>/', view=redirect_view, name='short_url_view')] 
```

这个文件基本上是告诉 django，当一个看起来像这样的 url 进来时:/25t52/调用 redirect_view 并把值 25t52
传递给视图函数。

我还在我的主 urls.py 文件中包含了这一行:

```
path('short/', include('shorturl.urls')) 
```

这确保了 url 看起来像这样:
[http://acoolblog.com/short/25t52/](http://acoolblog.com/short/25t52/)

现在我们已经设置好了 url 结构，让我们看看实际的
views.py 函数:

```
from django.apps import apps
from django.http import Http404
from django.shortcuts import get_object_or_404, redirect
import short_url

def redirect_view(request, tiny):
    """
    Redirect to a given object from a short URL.
    """

    model = apps.get_model('MYAPP', 'MYBLOG')
    try:
        id = short_url.decode_url(tiny)
    except ValueError:
        raise Http404('Bad encoded ID.')

    # Try to look up the object. If it's not a valid object, or if it doesn't
    # have an absolute url, bail again.
    obj = get_object_or_404(model, pk=id)
    return redirect(obj) 
```

在这个例子中，我有一个名为 MYAPP 的应用程序和一个名为 MYBLOG 的博客模型。请确保对此进行修改，以使用您自己的应用程序和模型。

该代码试图将小字符串转换成数字。如果此转换不成功，它将返回 HTTP 404 错误。

最后一步是通过使用
函数`get_object_or_404`计算出哪个对象对应于 ID。一旦找到对象，它将重定向到显示该对象的 url。

现在的问题是，它如何知道该对象使用什么 url？

通过在我们的模型中使用`get_absolute_url`函数。

在这种情况下，如果我们有一个 MYBLOG 模型，我们可以在模型定义中包含一个`get_absolute_url`函数，在我们的 models.py 文件中是这样的:

```
def get_absolute_url(self):
    return reverse_lazy('blog_page', args=[self.slug]) 
```

如果您对这段代码以及在哪里包含它感到不舒服，请参考 django 文档。

我们快完成了！

我们需要包含的最后一部分是生成 short_url 的方法。对于我的实现，我决定将它添加到 models.py
文件中，就在我的`get_absolute_url`函数:
下面

```
def get_short_url(self):
    tinyid = short_url.encode_url(self.pk)
    return reverse_lazy('short_url_view', kwargs={'tiny': tinyid}) 
```

这部分代码将获取对象的 id，并基于它创建一个 url，然后返回它。

现在我们知道如何生成一个短的 url 并把它变成一个更长的。最后一步是将信息包含在模板中。如果我们想要构建一个完整的可共享 url，我们可以在模板中包含这个信息:

```
<a href="{{ request.scheme }}://{{ request.get_host }}{{ MYBLOG.get_short_url }}"> 
```

这部分代码将返回完整的 url，这将适合于通过 twitter 与
分享:[http://acoolblog.com/short/25t52/](http://acoolblog.com/short/25t52/)

最后，不要忘记将这个`shorturl`应用添加到`DJANGO_APPS`列表中的 settings.py 文件中。

为了分享一个这方面的例子，我使用了 slideZing 来分享我最近演讲的幻灯片。短网址看起来像这样[https://slidezing.com/short/4xct4/](https://slidezing.com/short/4xct4/)然后重定向到[https://slidezing . com/landing/chrism/evangelizing-python-for-business/](https://slidezing.com/landing/chrism/evangelizing-python-for-business/)

希望这对大家有帮助。如果你对这种方法有任何疑问，请随时发表评论。