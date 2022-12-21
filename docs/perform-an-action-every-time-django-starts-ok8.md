# 每当姜戈开始时执行一个动作

> 原文：<https://dev.to/connorbode/perform-an-action-every-time-django-starts-ok8>

最近在建 [`django-single-instance-model`](https://matix.io/django-single-instance-model/) 。这个包确保了在任何时候都只有一个模型实例。

构建这个包的任务之一是确保模型的一个实例存在。

一旦建立了数据库连接，我想尽可能早地运行这段代码。

我是怎么做到的？

在主 app 的`__init__.py`里，我钩入了`connection_created`信号。下面是方法:

```
from django.dispatch import receiver
from django.db.backends.signals import connection_created

@receiver(connection_created)
def my_receiver(connection, **kwargs):
    with connection.cursor() as cursor:
            pass # your startup code here 
```

* * *

希望这对你以后有所帮助！关注我以获得更多 Django / Python 技巧！