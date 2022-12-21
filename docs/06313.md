# Python Django 2 升级教程:常见问题

> 原文：<https://dev.to/alansolitar/python-django-2-upgrade-tutorial-common-problems-1ni2>

本教程将介绍在将 Python Django 应用程序升级到 Django 2 时会遇到的一些常见错误。

我最近将一个项目从 Django 1.11 升级到了 Django 2，升级后我遭到了大量不同错误的轰炸。我想我应该提供一份我遇到的错误以及我如何修复它们的记录。

### 1。没有名为“django.core.urlresolvers”的模块

在 Django 中，使用 reverse 函数从名称中获取 url 的路径是很常见的。

有问题的一行如下:

```
from django.core.urlresolvers import reverse 
```

Django 2 中的 **urlresolver** 模块已被移除，不能再使用。相反，您可以使用来自**URL**模块的**解析**函数:

```
from django.urls import reverse 
```

厉害！一个倒下了。我们继续。

### 2。缺少 1 个必需的位置参数:“on_delete”

Django 2 现在要求为模型中使用的外键字段指定 on_delete 关键字。

以前会默认为 models.CASCADE。

为了解决这个问题，您需要向每个外键字段添加 on_delete 关键字参数。您还需要为 **OneToOneField** 指定 on_delete。

on_delete 关键字参数的选项包括:

*   模特。串联
*   模特。保护
*   模特。SET_NULL
*   模特。设置默认值
*   模特。集合()
*   模特。什么都不做

您可以在 [Django 文档](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.ForeignKey.on_delete)中了解可以传递给 on_delete 的值。

下面是一个指定 on_delete 关键字的示例:

```
my_field = models.ForeignKey(MyModel, on_delete=models.CASCADE) 
```

好了，继续下一个错误。

### 3。在 include()中指定命名空间，而不提供 app_name

这个错误是由 Django 的 **include** 函数的 api 的变化引起的。我们需要确保**包含**函数被传递了正确的参数。

首先，您需要转到您试图包含的 urls.py 文件，并添加一个名为 **app_name** 的变量。您可以将它添加到文件中的任何位置，但是我建议您在导入之后将它放在顶部。

```
# my_app/urls.py  
#... your imports  

app_name="my_app"  

# ... rest of your code 
```

现在，转到包含另一个文件的 urls.py 文件。现在，您可以将指定的 **app_name** 作为第二个参数添加到 include 函数中。

这里有一个例子:

```
# urls.py  
re_path(r'^api/my_path', include('my_app.urls','my_app' )), 
```

### 4。“字段集[][]
中有重复的字段

在我的例子中，我的自定义 **ModelAdmin** 类有一个问题。Django 以前似乎允许重复字段，但现在不允许了。

只需从自定义管理类中删除重复的字段，就不会有任何问题。

### 5。JSONField 默认值应该是可调用的，而不是实例，这样它就不会在所有字段实例之间共享。

只有在使用 PostgreSQL 和 PostgreSQL JSONField 时，才会遇到这个问题。

基本上，Django 只希望您传递一个 callable 作为这个字段的默认值。这是为了避免所有对象共享默认设置，这可能会导致修改时出现问题。

如果你的默认值是 **{}** ，用**字典**替换它。

如果你的默认是 **[]** ，用**列表**替换。

### 6。request.user.is_authenticated()类型错误:“bool”对象不可调用

在 Django 2 中， **is_authenticated** 不能再作为函数调用。它是一个布尔属性，您需要像调用属性一样调用它，如下所示:

```
request.user.is_authenticated 
```

### 7。可选更改

您可能正在使用来自 **django.conf.urls** 的 **url** 函数。您的代码可能如下所示:

```
from django.conf.urls import url  
url(r"^books/$", views.BookList.as_view()), 
```

**url** 函数是 **django.urls.re_path()** 的别名，根据 [Django 文档](https://docs.djangoproject.com/en/2.2/ref/urls/#url)，它可能会被弃用。

我强烈建议改为 **import re_path** 以避免将来该函数被弃用时出现问题。

用 **re_path** 重写，上面的例子将变成:

```
from django.urls import re_path  
re_path(r"^books/$", views.BookList.as_view()), 
```

这大概就是总结了。希望这能帮助你升级到 Django 2。

如果你喜欢这个并且想看类似的内容，这里有一些我写的其他 [python 文章](https://remotedevdaily.com/category/python/)。也许你正在使用 heroku 来托管你的 Django 项目。如果是这样的话，看看我的关于如何[在 heroku](https://remotedevdaily.com/how-to-change-your-python-version-in-heroku/) 中改变你的 python 版本的文章。

另外，如果你有兴趣阅读更多关于成为远程软件开发人员的精彩文章，请考虑订阅我的[邮件列表](https://remotedevdaily.com/subscribe/)。

继续编码！

帖子 [Python Django 2 升级教程:常见问题](https://remotedevdaily.com/python-django-2-upgrade-tutorial-common-problems/)最早出现在[远程开发日报](https://remotedevdaily.com)上。