# Django 控制台提示:自动加载您的模型

> 原文：<https://dev.to/lpellis/django-console-tip-autoload-your-models-21k4>

# Django 控制台提示:自动加载你的模型

每天我都会打开 Django 控制台几次，检查我的模型中的一些值。我会键入如下内容:

```
Post.objects.filter(user=User.objects.last(), state='published').count() 
```

不料相迎:

```
Traceback (most recent call last):
  File "&lt;input&gt;", line 1, in &lt;module&gt;
NameError: name 'Post' is not defined 
```

当然我忘了导入需要的型号:

```
from users.models import User
from blog.models import Post
Post.objects.filter(user=User.objects.last(), state='published').count()
4 
```

当你打开控制台时，就像 rails 那样自动加载，这不是很好吗？幸运的是这很容易做到，所需要的只是添加几行在控制台启动时执行的代码:

```
from django.apps import apps

for _class in apps.get_models():
    globals()[_class.__name__] = _class 
```

如果你使用 PyCharm，那么你可以从`settings`->-`Build, Execution, Deployment`->-`Console`->-`Django Console`进行设置

<figure>[![](img/1b9837cb4a3fdcfc2769538c24e48cae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uyo68RnM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ximg/image/upload/w_732%2Ch_500%2Cc_limit/f_auto/whoami/4a595384/post/b5yt1Vlaj4LXkxIf.png) 

<figcaption>Django 控制台中设置 PyCharm</figcaption>

</figure>

瞧，从现在开始，当你开始工作时，你所有的模型都可以使用了。

```
Post.objects.filter(user=User.objects.last(), state='published').count()
4 
```

<small>最初发布于[https://loftie.com/post/django-console-tip](https://loftie.com/post/django-console-tip)T3】</small>