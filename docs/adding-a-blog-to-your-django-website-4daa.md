# 在 Django 网站上添加博客

> 原文：<https://dev.to/clarity89/adding-a-blog-to-your-django-website-4daa>

如今，有大量的解决方案可以轻松创建和托管博客。然而，有时你已经有了一个网站，只是想给它添加一个博客，除了手头现有的工具之外，没有使用其他工具。在这篇文章中，我们将介绍用 Django 创建博客的过程，看看它有多简单明了。这篇文章假设你已经有了一个 Django 应用程序/站点。如果没有，可以通过遵循[官方指令](https://docs.djangoproject.com/en/2.2/intro/tutorial01/)轻松创建一个。使用的 Django 版本是 2.2。

首先，我们开始运行一个方便的`startapp`命令，它将搭建我们的博客应用程序。这应该从`manage.py`所在的同一个文件夹中运行:

```
$ python manage.py startapp blog 
```

这将添加一个结构如下的`blog`目录:

```
blog/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py 
```

第一步是将新创建的应用程序添加到`settings.py` :
中的`INSTALLED_APPS`列表

```
INSTALLED_APPS = [
     # other apps ...
    'mywebsite.blog'
] 
```

现在我们可以开始建立博客了。我们首先打开`models.py`并在那里添加一个`BlogPost`模型。

```
from datetime import datetime

from django.db import models
from django.urls import reverse
from django.utils.translation import ugettext_lazy as _

from autoslug.fields import AutoSlugField
from ckeditor.fields import RichTextField

class BlogPost(models.Model):
    title = models.CharField(_('title'), max_length=255)
    slug = AutoSlugField(_('slug'), populate_from='title', unique=True)
    image = models.ImageField(_('image'), blank=True, null=True, upload_to='blog')
    text = RichTextField(_('text'))
    description = models.TextField(_('description'), blank=True, null=True)
    published = models.BooleanField(_('published'), default=False)

    created = models.DateTimeField(_('created'), auto_now_add=True)
    modified = models.DateTimeField(_('modified'), auto_now=True)
    pub_date = models.DateTimeField(_('publish date'), blank=True, null=True)

    class Meta:
        verbose_name = _('blog post')
        verbose_name_plural = _('blog posts')
        ordering = ['pub_date'] 
```

除了预期的`title`、`image`、`description`和`text`，我们还有几个额外的字段，它们将在发布和导航到博客帖子时使用:

*   `slug` -是 bog 帖子 URL 的唯一可识别部分。我们正在使用 [django-autoslug](https://django-autoslug.readthedocs.io/en/latest/) 包，这将使保留字段的唯一性和自动填充变得轻而易举。在这种情况下，我们通过使用`populate_from`参数从标题填充 slug。
*   `published` -指示帖子何时应该在网站上可见。
*   `pub_date` -文章发表的日期，也用于文章排序。

到目前为止一切顺利。然而，当 post 被设置为`published`时，自动设置`pub_date`是有意义的。可以通过扩展 Django 模型的`save`方法:
来实现

```
 def save(self, *args, **kwargs):
        """
        Set publish date to the date when post's published status is switched to True, 
        reset the date if post is unpublished
        """
        if self.published and self.pub_date is None:
            self.pub_date = datetime.now()
        elif not self.published and self.pub_date is not None:
            self.pub_date = None
        super().save(*args, **kwargs) 
```

为了完成模型设置，我们将向它添加两个实用方法:`__str__()`，它将用于在整个应用程序中表示模型(特别是在管理中)，以及`get_absolute_url()`，它将使在视图中检索博客文章的 URL 更加容易。

```
 def __str__(self):
     return self.title

 def get_absolute_url(self):
     return reverse('blog:detail', kwargs={'slug': self.slug}) 
```

模型出来后，我们可以继续创建迁移并运行它们。

```
# Create migrations
$ python manage.py makemigrations blog

# Apply them
$ python manage.py migrate blog 
```

此时，将新创建的迁移添加到版本控制中(我似乎总是忘记这一点)并提交/推动进度将是一个好主意。

现在我们已经有了一个合适的、正常工作的模型，是时候把它添加到 Django 的 admin 中了。考虑到管理员是多么强大，启用一个新的模型有一个相当快速的工作:

```
from django.contrib import admin
from .models import BlogPost

@admin.register(BlogPost)
class BlogPostAdmin(admin.ModelAdmin):
    list_display = ('title', 'image', 'text', 'published', 'pub_date') 
```

这就是管理设置！添加了这几行代码后，我们现在可以导航到[http://localhost:8000/admin/blog/](http://localhost:8000/admin/blog/)(默认情况下，Django 在端口 8000 上运行开发服务器)并开始创建博客文章。

通过 admin 添加一些帖子后，下一步自然是在视图中显示它们。我们的博客将有一个按时间顺序排列的所有文章的分页列表视图，以及每个文章的单独详细视图。我们将使用[基于类的视图](https://docs.djangoproject.com/en/2.2/topics/class-based-views/)，因为它们允许很好的代码重用，并且能够用最少的代码编写最常见的视图。我们可以像这样在`views.py`中设置它们:

```
from django.views.generic import ListView, DetailView

from .models import BlogPost

class BlogPostListView(ListView):
    model = BlogPost
    queryset = BlogPost.objects.filter(published=True)
    template_name = 'blog/list.html'
    context_object_name = 'blog_posts'
    paginate_by = 15  # that is all it takes to add pagination in a Class Based View 

class BlogPostDetailView(DetailView):
    model = BlogPost
    queryset = BlogPost.objects.filter(published=True)
    template_name = 'blog/detail.html'
    context_object_name = 'blog_post' 
```

很简单。注意，在视图中启用分页只需要一行代码。这将为我们提供`is_paginated`和一些其他有用的上下文变量来呈现模板中的分页。

请注意，我们在两个地方对 queryset 应用了相同的过滤器。这在这里没什么大不了的，但是在某些情况下，我们希望将这种功能抽象出来。在目前的情况下，我们可以向我们的模型添加一个带有定制`QuerySet`方法的[管理器](https://docs.djangoproject.com/en/2.2/topics/db/managers/#calling-custom-queryset-methods-from-the-manager)的实例。

```
# models.py class BlogPostQueryset(models.QuerySet):

    def published(self):
        return self.filter(published=True)

    def draft(self):
        return self.filter(published=False)

class BlogPost(models.Model):
    title = models.CharField(_('title'), max_length=255)
    ...
    objects = BlogPostQueryset.as_manager() 
```

现在在视图中，我们可以使用`BlogPost.objects.published()`来获取所有发布的博客文章。在这一点上，我们已经得到了大部分的功能，它只需要一堆文件中的几行代码。

最后，我们可以设置列表和详细信息模板。按照 Django 惯例，我们将把它们放入`mywebsite/blog/templates/blog/`。首先，我们将扩展主基础模板，还将添加一个可重用的导航栏模板，将“博客”设置为活动标签:

```
{% extends "base.html" %}{% load i18n staticfiles %}

{% block header %}
    {% include "includes/navbar.html" with active="blog" %}
{% endblock %} 
```

作为参考，`navbar.html`可以是这样的:

```
{% load i18n staticfiles %}
<nav class="navbar">
    <div class="navbar__header">
        <div class="navbar--left">
            <a class="no-underline {% if active == 'home' %}active{% endif %}" href="/">{% trans "Home" %}</a>
        </div>
    </div>

    <div class="navbar__inner">
        <ul class="navbar--right">
            <li class="navbar__tab">
                <a class="no-underline {% if active == 'portfolio' %}active{% endif %}"
                   href="{% url 'portfolio:list' %}">
                    {% trans "Portfolio" %}
                </a>
            </li>
            <li class="navbar__tab">
                <a class="no-underline {% if active == 'blog' %}active{% endif %}"
                   href="{% url 'blog:list' %}">
                    {% trans "Blog" %}
                </a>
            </li>
            <li class="navbar__tab">
                <a class="no-underline {% if active == 'about' %}active{% endif %}" href="{% url 'about' %}">
                    {% trans "About" %}
                </a>
            </li>
        </ul>
    </div>
</nav> 
```

因为 HTML 结构和它的样式不是这篇文章的主要目的，我们将简单的讨论一下。可以用这个 HTML:
建立一个简单的帖子列表

```
<!-- Assuming you have overridable 'content' block in you master.html, otherwise leave this out-->
{% block content %}
    <div class="blog__container">
        <h3 class="blog__header">{% trans "Latest posts" %}</h3>
        <ul class="blog__list">
            {% for post in  blog_posts %}
                <li class="blog__item">
                    <div class="blog__inner">
                        {% if post.image %}
                            <div class="blog__image" style="background-image: url({{ post.image.url }})"></div>
                        {% endif %}
                        <div class="blog__info">
                            <a href="{{ post.get_absolute_url }}" class="blog__title">{{ post.title }}</a>
                            <p class="blog__description">{{ post.description }}</p>
                            <p class="blog__footer">{{ post.pub_date|date }}</p>
                        </div>
                    </div>
                </li>
            {% endfor %}
        </ul>
    </div>
     {% if is_paginated %}
        <div class="blog__pagination">
            {% if page_obj.has_previous %}
                <a href="{% url 'blog:list' %}?page={{ page_obj.previous_page_number }}"><<</a>
            {% endif %}
                <span class="page-current">
                    {% blocktrans with num=page_obj.number num_pages=page_obj.paginator.num_pages %}
                        Page {{ num }} of {{ num_pages }}
                    {% endblocktrans %}
                </span>
            {% if page_obj.has_next %}
                <a href="{% url 'blog:list' %}?page={{ page_obj.next_page_number }}">>></a>
            {% endif %}
        </div>
    {% endif %}
{% endblock %} 
```

这里有些东西一文不值:

*   用`background-image`代替`<img/>`是因为在大多数情况下更容易做出反应。
*   我们在文章的标题`href`中使用添加到模型中的`get_absolute_url`来轻松地重定向到文章的详细视图，而不需要在模板中显式传递文章的 id。
*   如前所述，在视图中启用分页会在 template 中暴露一堆有用的上下文变量，即用于检查是否必须呈现分页的`is_paginated`和包含页码信息的`page_obj`。

说到 URL，如果我们试图导航到我们的博客文章列表页面，我们将会收到一个`NoReverseMatch`错误，因为我们还没有为博客设置 URL。为了解决这个问题，让我们将`urls.py`添加到我们的博客应用程序中(与`models.py`和`views.py`相同的级别)并启用必要的路线。注意，Django 从版本 2 开始引入了一个新的用于声明 URL 的`path`函数。

```
from django.urls import path

from .views import BlogPostDetailView, BlogPostListView

urlpatterns = [
    path('', BlogPostListView.as_view(), name='list'),
    path('<slug>', BlogPostDetailView.as_view(), name='detail'),
] 
```

之后，我们需要转到根目录`urls.py`并在那里添加博客 URL:

```
from django.urls import include, path

urlpatterns = [
    # .... other urls
    path('blog/', include(('clarityv2.blog.urls', 'blog'), namespace='blog')),
] 
```

这样，我们就可以导航到 localhost:8000/blog，查看我们创建的博客列表。单击文章标题应该会将我们重定向到文章详细信息页面，但是由于我们没有设置它，我们会看到一个空白页面。为了解决这个问题，让我们添加一些简单的 HTML 来显示帖子:

```
{% extends "base.html" %}{% load i18n staticfiles %}

{% block header %}
    {% include "includes/navbar.html" with active="blog" %}
{% endblock %}

{% block content %}
    <article class="blog__container">
        <section class="blog__header">
            <h3 class="blog__title blog__title--large">{{ blog_post.title }}</h3>
            <p class="blog__footer">
                {{ blog_post.pub_date|date }}
            </p>
        </section>
        <section class="blog__text">{{ blog_post.text|safe }}</section>
    </article>
{% endblock %} 
```

这里我们使用`safe`模板过滤器来避免编辑器中的 HTML markdown。

差不多就是这样！现在我们有了一个功能齐全的个人博客，虽然很简单，但包含了所有必要的 CRUD 功能。有几种方法可以对它进行样式化和扩展，但这留给读者作为练习:)