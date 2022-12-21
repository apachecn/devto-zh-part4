# 姜戈回归+鹞鹰=魔法

> 原文：<https://dev.to/adamghill/django-reversion-wagtail-magic-4fmb>

# 凡事都有取舍(或者说我是如何不再担心，学会爱上 Wagtail CMS 的)

Wagtail 是一个构建在 Django 之上的 CMS 框架，它消除了从零开始创建 CMS 的繁琐。它有一个很好的可扩展界面供编辑交互，页面回复跟踪，漂亮的管理用户界面，简单的图片上传，以及[许多其他好东西](https://wagtail.io/features/)。

老实说，我来自 Django，在那里我很好地掌握了事情是如何工作的(如果需要的话，也知道在哪里解决问题),这是一个向 Wagtail 的艰难转变。理解它在内容方面的哲学确实花了一点时间。

但是，在这一点上，我已经开始欣赏 Wagtail 提供的所有好处...处理一些我不太喜欢的部分。我花了一段时间才明白的一件事是，Wagtail 使用术语*片段*来指代 Django 模型，这些模型不是明确的*页面*。来自 Django，我习惯于定义一个数据模型，并用多对多或外键关系将模型捆绑在一起。*片段*是存储那些可以与许多不同*页面*模型共享的信息片段的地方。然而， *snippets* 感觉它们没有完全集成到 Wagtail 体验中，并且它们失去了页面模型“免费”提供的许多好功能——一个明显的遗漏是缺少对 *snippets* 的修改。在 Wagtail 中有几个公开的问题(特别是[https://github.com/wagtail/wagtail/issues/4541](https://github.com/wagtail/wagtail/issues/4541)和[https://github.com/wagtail/wagtail/issues/2270](https://github.com/wagtail/wagtail/issues/2270))。

# Django 通过一次 0 美元的轻松付款进行审计

我在过去对一个普通 Django 应用程序非常欣赏的一个包是 [django-reversion](https://django-reversion.readthedocs.io/) ，它紧密集成到 Django admin 中，并自动保存对象的版本作为审计跟踪。Wagtail 为*页面*模型提供了一些非常相似的东西，但是 snippets 不会被邀请参加聚会，并且会感到被冷落。

# Ay，卡兰巴！自动审计 Wagtail 片段！

不过，在华尾巴国一切都很好！只要有一点坚持，wag tail*snippet*也可以得到自动修改，这并不是**太难。**

## 设置 django-反转

首先，按照正常安装步骤安装`django-reversion`。不幸的是，Wagtail 不会像`django-reversion`那样为 Django admin 使用自动魔法，但是它有一个你可以显式调用的一流 API。

## 用 django-reversion 注册片段模型

对于*片段*模型，您需要在`models.py`中的`@register_snippet`上方添加`@reversion.register()`装饰器。

```
from django.db import models
import reversion
from wagtail.core.models import ClusterableModel, Orderable
from wagtail.snippets.models import register_snippet

@reversion.register()
@register_snippet
class Book(Orderable, ClusterableModel):
    title = models.CharField(max_length=255) 
```

Enter fullscreen mode Exit fullscreen mode

## 凌驾于鹞鹰之上

接下来，您需要覆盖您想要在`wagtail_hooks.py`中审计的片段的 Wagtail 视图。

```
from reversion.revisions import add_to_revision, create_revision, set_comment, set_user
from wagtail.contrib.modeladmin.options import ModelAdmin
from wagtail.contrib.modeladmin.views import CreateView, EditView

from .models import Book

class RevisionEditView(EditView):
    def form_valid(self, form, *args, **kwargs):
        form_valid_return = super().form_valid(form, *args, **kwargs)

        with create_revision():
            set_comment(self.get_success_message(self.instance))
            add_to_revision(self.instance)
            set_user(self.request.user)

        return form_valid_return

class RevisionCreateView(CreateView):
    def form_valid(self, form, *args, **kwargs):
        form_valid_return = super().form_valid(form, *args, **kwargs)

        # Call form.save() explicitly to get access to the instance
        instance = form.save()

        with create_revision():
            set_comment(self.get_success_message(instance))
            add_to_revision(instance)
            set_user(self.request.user)

        return form_valid_return

class BookAdmin(ModelAdmin):
    model = Book
    edit_view_class = RevisionEditView
    create_view_class = RevisionCreateView 
```

Enter fullscreen mode Exit fullscreen mode

# 快乐小径

通过设置`edit_view_class`和`create_view_class`设置和`django-revision`来覆盖 ModelAdmin 类才是真正的神奇之处。但是，一定要注意`CreateView`中的怪异之处，以获得可用的`instance`。在测试中，它没有创建模型的多个实例，但是，如果因为实现而出现重复的数据库调用，我不会感到惊讶。

> #### [T2】使用以下软件包版本进行测试](#tested-with-the-following-package-versions)
> 
> *   Python 3。7 .四
> *   [Django](https://www.djangoproject.com/) = = 2。2 .四
> *   [摇尾巴](https://wagtail.io/) = = 2。6 .一
> *   [Django-reversion](https://django-reversion.readthedocs.io/) = = 3。0 .四

*最初发表于[adamghill.com](https://adamghill.com/django/python/wagtail/2019/09/19/django-reversion-wagtail-magic.html)。*