# 用 Python、Django 和 Django Rest 框架开发 Restful APIs

> 原文：<https://dev.to/joshuamdeguzman/definitive-guide-developing-restful-apis-using-python-django-and-drf-2h7e>

# 简介

对于想要使用 Python、Django 和 Django Rest 框架开发 RESTful APIs 项目的初学者来说，本文是一个权威指南。

*   **Django** 是一个用 **Python** 编写的 web 框架

*   Python 是一种用于通用编程的解释型高级编程语言

*   API 或应用程序编程接口是一组规则和机制，一个应用程序或组件通过它们与其他应用程序或组件进行交互

*   **REST** 或表述性状态转移是一种软件架构

## 休息 API

正如罗伊·菲尔丁在一篇论文中所描述的，

> REST 是一种“架构风格”,它基本上利用了 web 的现有技术和协议。

简单地说，它是以适合客户机的格式为客户机提供的数据表示。

因此，RESTful + API 是实现这种架构和约束的常用术语(例如在 web 服务中)。

下面是一个从 GitHub 的 API
获取请求的例子

```
$  curl  https://api.github.com/users/joshuadeguzman 
```

Enter fullscreen mode Exit fullscreen mode

您将看到类似于
的输出

```
{  "login":  "joshuadeguzman",  "id":  20706361,  "node_id":  "MDQ6VXNlcjIwNzA2MzYx",  "avatar_url":  "https://avatars1.githubusercontent.com/u/20706361?v=4",  "gravatar_id":  "",  "url":  "https://api.github.com/users/joshuadeguzman",  "html_url":  "https://github.com/joshuadeguzman",  "followers_url":  "https://api.github.com/users/joshuadeguzman/followers",  "following_url":  "https://api.github.com/users/joshuadeguzman/following{/other_user}",  "gists_url":  "https://api.github.com/users/joshuadeguzman/gists{/gist_id}",  "starred_url":  "https://api.github.com/users/joshuadeguzman/starred{/owner}{/repo}",  "subscriptions_url":  "https://api.github.com/users/joshuadeguzman/subscriptions",  "organizations_url":  "https://api.github.com/users/joshuadeguzman/orgs",  "repos_url":  "https://api.github.com/users/joshuadeguzman/repos",  "events_url":  "https://api.github.com/users/joshuadeguzman/events{/privacy}",  "received_events_url":  "https://api.github.com/users/joshuadeguzman/received_events",  "type":  "User",  "site_admin":  false,  "name":  "Joshua de Guzman",  "company":  "@freelancer",  "blog":  "https://joshuadeguzman.me",  "location":  "Manila, PH",  "email":  null,  "hireable":  true,  "bio":  "Android Engineer at @freelancer. Building tools for humans.",  "public_repos":  75,  "public_gists":  2,  "followers":  38,  "following":  10,  "created_at":  "2016-07-28T15:19:54Z",  "updated_at":  "2019-06-16T10:26:39Z"  } 
```

Enter fullscreen mode Exit fullscreen mode

上面显示的是一个 **JSON** 格式的数据集。

> **JSON** 或 **JavaScript Object Notation** 是一种开放标准的文件格式，它使用人类可读的文本来传输由属性值对和数组数据类型组成的数据对象。

其他格式包括 XML、INI、CSV 等。但是今天，JSON 被广泛使用，因为它结构直观，无论使用什么编程语言，都可以轻松地阅读和映射领域对象。

## Python 和 Django

**Python** ，据其创造者吉多·范·罗苏姆称，是一个

> 高级编程语言，其核心设计理念是关于代码可读性和语法，它允许程序员用几行代码表达概念。

Python 使用类似英语的单词表示(例如，对于方法，保留关键字和控制流),这使得任何初学者都可以更容易地直接进入它。它还具有动态类型系统的特点，这意味着它在运行时验证程序的类型安全。它还能自动管理内存。

```
print(5 + 5) # This will result to 10 
```

Enter fullscreen mode Exit fullscreen mode

Django 是一个高级 Python Web 框架，它使开发人员能够以简洁实用的设计按时交付项目。

它的旗舰特性包括快速开发设计、安全和可扩展的产品。

## 快速 Django 概述

Django 传播数据库模式变化的方式是通过它的**迁移**模块。

样品`User`型号

```
from django.db import models

class User(models.Model):
    first_name = models.CharField(max_length=50)
    middle_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)

    def __str__(self):
        return self.name 
```

Enter fullscreen mode Exit fullscreen mode

如果对您的模型进行了任何更改，运行`makemigrations`

```
$  python  manage.py  makemigrations 
```

Enter fullscreen mode Exit fullscreen mode

最后，您可以将数据库与一组模型和迁移同步

```
$  python  manage.py  migrate 
```

Enter fullscreen mode Exit fullscreen mode

# 带有 Django Rest 框架的 REST APIs

DRF 或 Django REST 框架是一个强大而灵活的构建 Web APIs 的工具包。它有助于开发人员不要自己从头开始推出复杂而可靠的 REST API，从而重新发明轮子。因为当你的项目变得越来越复杂时，你很快就会意识到需要使用 DRF 或者其他有用的 rest 框架。

### 1。安装&项目设置

创建**项目**目录

```
$  mkdir  djangoapi 
```

Enter fullscreen mode Exit fullscreen mode

通过 [pip](https://pip.pypa.io/en/stable/installing/) 安装 **virtualenv**

一个**虚拟环境**允许一个项目在它的环境中有额外的库或者包的改变，而不会干扰其他环境的全局或者库。

> `pip`是一个软件包管理系统，用于安装和管理用 Python 编写的软件包。

```
$  pip  install  virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

在您的项目目录中创建一个环境文件夹

```
$  cd  djangoapi  $  virtualenv  venv 
```

Enter fullscreen mode Exit fullscreen mode

激活环境

```
$  source  venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

要撤销对路径的这些更改，只需运行`deactivate`。更多关于 [virtualenv](https://virtualenv.pypa.io/en/stable/userguide/) 。

安装 **django** ， **djangorestframework**

```
$  pip  install  django  $  pip  install  djangorestframework 
```

Enter fullscreen mode Exit fullscreen mode

创建一个 **django 项目**

```
$  django-admin  startproject  blog 
```

Enter fullscreen mode Exit fullscreen mode

运行您的**项目**

```
$  python  manage.py  runserver  System  check  identified  no  issues  (0  silenced).  You  have  15  unapplied  migration(s).  Your  project  may  not  work  properly  until  you  apply  the  migrations  for  app(s):  admin,  auth,  contenttypes,  sessions.  Run  'python manage.py migrate'  to  apply  them.  August  16,  2018  -  09:58:36  Django  version  2.1,  using  settings  'blog.settings'  Starting  development  server  at  http://127.0.0.1:8000/  Quit  the  server  with  CONTROL-C. 
```

Enter fullscreen mode Exit fullscreen mode

*未应用的迁移*指的是启动 django 项目时包含的默认迁移文件。

要同步这些迁移文件，只需运行`migrate`

```
$  python  manage.py  migrate  Running  migrations:  Applying  contenttypes.0001_initial...  OK  Applying  auth.0001_initial...  OK  Applying  admin.0001_initial...  OK  Applying  admin.0002_logentry_remove_auto_add...  OK  Applying  admin.0003_logentry_add_action_flag_choices...  OK  Applying  contenttypes.0002_remove_content_type_name...  OK  Applying  auth.0002_alter_permission_name_max_length...  OK  Applying  auth.0003_alter_user_email_max_length...  OK  Applying  auth.0004_alter_user_username_opts...  OK  Applying  auth.0005_alter_user_last_login_null...  OK  Applying  auth.0006_require_contenttypes_0002...  OK  Applying  auth.0007_alter_validators_add_error_messages...  OK  Applying  auth.0008_alter_user_username_max_length...  OK  Applying  auth.0009_alter_user_last_name_max_length...  OK  Applying  sessions.0001_initial...  OK 
```

Enter fullscreen mode Exit fullscreen mode

我们项目中的默认数据库当前设置为名为`db.sqlite3`的 SQLite。

创建一个 **django 项目的 app**

```
$  cd  blog  $  python  manage.py  startapp  posts 
```

Enter fullscreen mode Exit fullscreen mode

项目结构应该类似于

```
$  find  .  ./posts  ./posts/migrations  ./posts/migrations/__init__.py  ./posts/models.py  ./posts/__init__.py  ./posts/apps.py  ./posts/admin.py  ./posts/tests.py  ./posts/views.py  ./db.sqlite3  ./blog  ./blog/__init__.py  ./blog/__pycache__  ./blog/__pycache__/settings.cpython-36.pyc  ./blog/__pycache__/wsgi.cpython-36.pyc  ./blog/__pycache__/__init__.cpython-36.pyc  ./blog/__pycache__/urls.cpython-36.pyc  ./blog/settings.py  ./blog/urls.py  ./blog/wsgi.py  ./manage.py 
```

Enter fullscreen mode Exit fullscreen mode

### 2。模型

每个模型实例都是数据信息的权威来源。通常，每个模型都与数据库中的一个表相关。

```
# djangoapi/blog/posts/models.py from django.db import models

# Create your models here. 
class Post(models.Model):
    title = models.CharField(max_length=255)
    content = models.TextField()
    is_featured = models.BooleanField(default=False)

    def __str__(self):
        return self.name 
```

Enter fullscreen mode Exit fullscreen mode

> `__str__`由`str()`内置函数和 print 语句调用，以计算对象的“非正式”字符串表示。

如果您尝试运行`makemigrations`，django 还看不到这些变化。

```
$  No  changes  detected 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，请将您的`posts`应用程序添加到您项目的已安装应用程序中。

```
# djangoapi/blog/blog/settings.py 
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts' # Add it here ] 
```

Enter fullscreen mode Exit fullscreen mode

继续迁移模型

```
$  python  manage.py  makemigrations  Migrations  for  'posts':  posts/migrations/0001_initial.py  -  Create  model  Post  $  python  manage.py  migrate  Operations  to  perform:  Apply  all  migrations:  admin,  auth,  contenttypes,  posts,  sessions  Running  migrations:  Applying  posts.0001_initial...  OK 
```

Enter fullscreen mode Exit fullscreen mode

### 3。序列化

序列化程序允许将数据结构或对象状态转换成可以存储或传输的格式，并在以后重新构造。

创建 API 的`serializers.py`和`views.py`文件，并像这样隔离它们

```
# posts/api  posts/api/serializers.py  posts/api/views.py  # posts/migrations  posts/migrations/  # posts  posts/admin.py  posts/apps.py  posts/models.py  posts/tests.py  posts/views.py 
```

Enter fullscreen mode Exit fullscreen mode

```
# posts/api/serializers.py 
from ..models import Post
from rest_framework import serializers

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ('title', 'content', 'is_featured') # if not declared, all fields of the model will be shown 
```

Enter fullscreen mode Exit fullscreen mode

在本教程中，我们已经使用了`ModelSerializer`，更多关于[这个](http://www.django-rest-framework.org/api-guide/serializers/)。

### 4。视图

视图函数(或简称为 view)是一个 Python 函数，它接受 Web 请求并返回 Web 响应。

```
# posts/api/views.py 
from ..models import Post
from . import serializers
from rest_framework import generics, status
from rest_framework.response import Response

class PostListView(generics.ListAPIView):
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，`ListAPIView`用于只读端点来表示模型实例的集合。

在这段代码中，我们使用来自`rest_framework`的`generics`视图方法，更多关于[这个](http://www.django-rest-framework.org/api-guide/generic-views/)。

###### 5\. URLs

这是我们设置到指定视图的路由或 URL 路径的地方，我们期望每个视图都有特定的响应。

```
# posts/urls.py 
from django.urls import path
from . import views
from .api import views

urlpatterns = [
    path('', views.PostListView.as_view(), name=None)
] 
```

Enter fullscreen mode Exit fullscreen mode

### 6。正在完成设置

确保将`rest_framework`添加到我们项目的应用程序中。

```
# djangoapi/blog/blog/settings.py 
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework', # Add it here
    'posts'
] 
```

Enter fullscreen mode Exit fullscreen mode

### 7。Django 管理

由于我们还没有设置我们的`POST`请求，我们将通过 django 的管理面板填充数据库。

为此，创建一个密码为`1234password`的超级用户账户`admin`。

```
$  python  manage.py  createsuperuser  --email  admin@example.com  --username  admin  Password:  Password  (again):  This  password  is  too  common.  Bypass  password  validation  and  create  user  anyway?  [y/N]:  y  Superuser  created  successfully. 
```

Enter fullscreen mode Exit fullscreen mode

在管理面板中注册模型。

```
# posts/admin.py 
from django.contrib import admin
from .models import Post

# Register your models here. admin.site.register(Post) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。访问[管理面板](http://127.0.0.1:8000/admin/)并更新`posts`模型的记录。更多关于[这个](https://docs.djangoproject.com/en/2.1/ref/contrib/admin/)。

### 8。测试我们的 API

```
$  python  manage.py  runserver 
```

Enter fullscreen mode Exit fullscreen mode

```
GET  /api/v1/posts/ 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP  200  OK  Allow:  GET,  HEAD,  OPTIONS  Content-Type:  application/json  Vary:  Accept  [  {  "title":  "Example Post #1",  "content":  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",  "is_featured":  false  },  {  "title":  "Example Post #2",  "content":  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",  "is_featured":  true  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在是时候更新我们的观点并完成标准的 CRUD 操作了。

### 9。添加更多视图

`POST`是一种用于在数据库中创建(有时更新)资源的方法。

```
# posts/api/views.py 
from ..models import Post
from . import serializers
from rest_framework import generics, status
from rest_framework.response import Response

class PostCreateView(generics.CreateAPIView):
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer

    def create(self, request, *args, **kwargs):
        super(PostCreateView, self).create(request, args, kwargs)
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully created",
                    "result": request.data}
        return Response(response) 
```

Enter fullscreen mode Exit fullscreen mode

最常见的情况是，当我们想要公开一个数据集列表时，我们分离`List`和`Create`视图类，而*很容易阻止*对`POST`的某个请求，或者在数据库中为那个特定的`List`视图创建一个资源。

用例总是因应用程序而异，您可以选择使用[listcreateapiew](http://www.django-rest-framework.org/api-guide/generic-views/#listcreateapiview)甚至[视图集](http://www.django-rest-framework.org/api-guide/viewsets/)来组合一组相关视图的逻辑。

*可选的*:因为我们希望以更系统的方式显示数据，所以我们覆盖了`create`方法并映射了我们的内联定制响应处理程序。

使用方法`GET`、`PATCH`、`DELETE`添加更多视图，以处理特定的博客文章细节。

```
class PostDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = serializers.PostSerializer

    def retrieve(self, request, *args, **kwargs):
        super(PostDetailView, self).retrieve(request, args, kwargs)
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        data = serializer.data
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully retrieved",
                    "result": data}
        return Response(response)

    def patch(self, request, *args, **kwargs):
        super(PostDetailView, self).patch(request, args, kwargs)
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        data = serializer.data
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully updated",
                    "result": data}
        return Response(response)

    def delete(self, request, *args, **kwargs):
        super(PostDetailView, self).delete(request, args, kwargs)
        response = {"status_code": status.HTTP_200_OK,
                    "message": "Successfully deleted"}
        return Response(response) 
```

Enter fullscreen mode Exit fullscreen mode

### 10。更新 URL

```
# posts/urls.py 
from django.urls import path
from . import views
from .api import views

urlpatterns = [
    path('', views.PostListView.as_view(), name=None),
    path('create/', views.PostCreateView.as_view(), name=None),
    path('<int:pk>/', views.PostDetailView.as_view(), name=None)
] 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以通过[邮递员](https://www.getpostman.com/)，你的应用程序发送请求到你的 API，或者从你的浏览器做一个`GET`请求，例子:

```
POST  /api/v1/posts/create/ 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP  200  OK  Allow:  POST,  OPTIONS  Content-Type:  application/json  Vary:  Accept  {  "status_code":  200,  "message":  "Successfully created",  "result":  {  "csrfmiddlewaretoken":  "rnSUN3XOIghnXA0yKghnQgxg0do39xhorYene5ALw3gWGThK5MjG6YjL8VUb7v2h",  "title":  "Creating a resource",  "content":  "Howdy mate!"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

```
GET  /api/v1/posts/1/ 
```

Enter fullscreen mode Exit fullscreen mode

```
HTTP  200  OK  Allow:  GET,  PUT,  PATCH,  DELETE,  HEAD,  OPTIONS  Content-Type:  application/json  Vary:  Accept  {  "status_code":  200,  "message":  "Successfully retrieved",  "result":  {  "title":  "Sample Post",  "content":  "Sample Post Content",  "is_featured":  false  }  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。你已经成功地用 DRF 开发了 RESTful APIs！干杯！

# 源代码

在 [GitHub](https://github.com/joshuadeguzman/djangoapi-demo) 上可用。

# 奖金

我将在另一篇文章中介绍使用 [JSON Web 令牌](https://jwt.io/)进行 RESTful 认证的可测试 RESTful API，并在这里加入一个链接，敬请关注！

如有疑问或建议，欢迎随时评论或联系 [me](//twitter.com/devjdg) 。