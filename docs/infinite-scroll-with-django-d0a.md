# 姜戈无限卷轴

> 原文：<https://dev.to/thepylot/infinite-scroll-with-django-d0a>

### 通过[反转 Python](https://reversepython.net)

### 嘿 **DEV** 网络！

今天我将向你展示如何用 Django 创建无限分页或无限滚动的快速教程

互联网上有许多与这个主题相关的资源，但大多数都没有得到充分的解释。所以，我会试着给你解释清楚，一步一步来。

好吧！让我们发射无限卷轴姜戈火箭吧！

我假设您已经创建了您的项目

#### 步骤 1:创建我们的模型

我们将创建一个非常简单的博客帖子。让我们在 **models.py** 中创建我们的 post 模型

```
 from django.db import model

   class Post(models.Model):
     title = models.CharField(max_length=250)
     description = models.TextField()
     image = models.FileField(null=True, blank=True)

     def __str__(self):
         return self.title 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记迁移您的模型:)

#### 步骤 2:创建我们的视图

在 **views.py** 中，我们将导入 **ListView** ，这是一个基于类的视图，它将包含对象列表。

```
 from django.shortcuts import render
  from .models import Post
  from django.views.generic.list import ListView

  class PostsView(ListView):
      model = Post 
      paginate_by = 2
      context_object_name = 'posts'
      template_name = 'posts.html'
      ordering = ['title'] 
```

Enter fullscreen mode Exit fullscreen mode

正如你看到的，我将通过使用 **paginate_by** 属性每次滚动加载 2 篇文章，但是你可以根据需要改变它的数量。 **context_object_name** 属性指定了要从模板中访问的变量的名称。提供一个有用的上下文对象名称总是一个好主意。设计模板的同事会感谢你的。此外，你可以通过标题或者发表日期来排序文章，但是现在让我们保留标题。

#### 步骤 3: URL 配置

是时候配置我们的 **urls.py** 了，所以让我们先看看代码。

```
 from django.contrib import admin
  from django.urls import path

  from posts.views import PostsView
  from django.conf import settings 
  from django.conf.urls.static import static 

  urlpatterns = [
      path('admin/', admin.site.urls),
      path('', PostsView.as_view(),  name="posts"),
  ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) 
```

Enter fullscreen mode Exit fullscreen mode

在基于类的视图中，您必须调用 as_view()函数，以便返回一个接受请求并返回响应的可调用视图。在通用视图的情况下，它是请求-响应周期中的主要入口点。

只是提醒**静态(设置。MEDIA_URL，document _ root =设置。**是为媒体文件服务的。

#### 步骤 4:航点 JS 和模板

Waypoints 是一个小的 jQuery 插件，当你滚动到一个元素时，可以很容易地执行一个函数。

[航路点文件](http://imakewebthings.com/waypoints/shortcuts/infinite-scroll/)

好吧！让我们先看看代码

```
 <div class="container">
                <div class="row infinite-container">
                    {% for post in posts %}
                        <div class="col-md-6 infinite-item">
                            <div class="card mb-4 shadow-sm">
                                <img class="img-thumbnail"  src="{{post.image.url}}"/>
                                <div class="card-body">
                                    <h5>{{post.title}}</h5>
                                    <p class="card-text">
                                        {{post.description|truncatewords:20}}
                                    </p>
                                </div>
                            </div>
                        </div>
                    {% endfor %}
                </div>
                {% if page_obj.has_next %}
                <a class="infinite-more-link" href="?page={{ page_obj.next_page_number }}"></a>
                {% endif %}
                <div class="d-flex justify-content-center" style="display:none;">
                    <div class="spinner-border" role="status">
                        <span class="sr-only">Loading...</span>
                    </div>
                </div>
            </div>

    <script src="/static/js/jquery-2.2.4.min.js"></script>
    <script src="/static/js/jquery.waypoints.min.js"></script>
    <script src="/static/js/infinite.min.js"></script>
    <script>
    var infinite = new Waypoint.Infinite({
        element: $('.infinite-container')[0],
        handler: function(direction) {

    },
    offset: 'bottom-in-view',

    onBeforePageLoad: function () {
    $('.spinner-border').show();
    },
    onAfterPageLoad: function () {
    $('.spinner-border').hide();
    }

    });

    </script> 
```

Enter fullscreen mode Exit fullscreen mode

这是教程的主要部分，我们首先包括 **jquery-2.2.4.min.js** 。最好包含 2.2.4 版本的 jQuery，否则会导致错误。然后添加 **jquery.waypoints.min.js** 和 **infinite.min.js**

你可以直接从 github repo of waypoints 获取脚本。

[jquery . waypoints . min . js](https://github.com/imakewebthings/waypoints/tree/master/lib)
T3】infinite . min . js

**元素**无限选项指的是将被用作航路点的所有无限物品周围的容器，并且提取的物品将被附加到该容器。

**。infinite-more-link** 匹配每一页上的“下一页”元素。

**。infinite-item** 是一个选择器字符串，它应该匹配将从每个页面中取出并追加到项目容器中的各个项目。

**偏置**是与常规航路点相同的偏置选项，除了现在默认为“视图底部”而不是 0。这意味着，默认情况下，当容器底部出现时，新的项目将被加载。

页面加载功能用于在文章加载时显示微调器

#### 任务完成！

[无限卷轴项目](https://github.com/raszidzie/infinite-scroll)

你今天刚刚学到了非常有用的东西，发射了你的无限卷轴 django 火箭！你可以从我的 git 库克隆或下载这个项目，别忘了在社交媒体上关注我，加入逆向宇航员社区！

#### **保持联系！**

## # [姜戈](https://dev.to/t/django) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:446,&quot;name&quot;:&quot;django&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: django" aria-pressed="false">跟随</button>

Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design. Built by experienced developers, it takes care of much of the hassle of Web development, so you can focus on writing your app without needing to reinvent the wheel. It’s free and open source.