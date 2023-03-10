# ORM(对象关系映射)如何使用描述符协议？

> 原文：<https://dev.to/sroy8091/how-orm-object-relational-mapping-uses-the-descriptor-protocol-1kgn>

# 重述

*(怀旧)*
从我之前的帖子中，我们了解了什么是描述符协议，以及在很多情况下如何使用它。如何修改**。**(点)操作符并在设定值时改变行为。

# 开门见山

你可能听说过 Django，也听说过 ORM 有多酷。你可以定义类，就这样。它将处理后端数据库的所有复杂性。

您可以将表声明为类，将列声明为类属性，将行声明为该类的对象

```
# of course you need to import models of Django. class User(models.Model):
    email = models.CharField(max_length=50)
    mobile = models.IntegerField()
... 
```

Enter fullscreen mode Exit fullscreen mode

所以这个类将被视为用户表，这个表中的条目基本上是这个类的对象。为了处理获取、更新和过滤查询，Django 使用了管理器。Django 提供了一个默认的管理器，但是你可以修改它。

```
class UserManager(models.Manager):
    def get_queryset(self):
...
# this will modify _filter_ behaviour. 
```

Enter fullscreen mode Exit fullscreen mode

所以 Djnago 在它的 ORM 中提供了所有这些很酷的特性，还有更多。

# 一些熟悉的实例

如果你使用过 Django ORM 或者其他类似的东西，你可能会遇到类似这样的事情

```
...
class Order(models.Model):
    user = models.ForeignKey(User)
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们想通过订单实例更改用户的任何属性 _(比如名字)_。所以我们将不得不做这样的事情

```
...
o.user.first_name = "Sumit"
... 
```

Enter fullscreen mode Exit fullscreen mode

这里 Django 使用他们著名的 ForwardManyToOneDescriptor 来处理引用。如果你想了解更多，你可以去他们的[源代码](https://github.com/django/django/blob/4122d9d3f1983eea612f236e941d937bd8589a0d/django/db/models/fields/related_descriptors.py#L81)

这里有几行

```
if value is not None and not isinstance(value, self.field.remote_field.model._meta.concrete_model):
            raise ValueError(
                'Cannot assign "%r": "%s.%s" must be a "%s" instance.' % (
                    value,
                    instance._meta.object_name,
                    self.field.name,
                    self.field.remote_field.model._meta.object_name,
                )
            ) 
```

Enter fullscreen mode Exit fullscreen mode

它检查值的类型，看它是否匹配相关类的实例。

# 结论

所以基本上，即使我们在日常编码中不使用描述符协议，但这是构建框架的人知道的一个秘密，而且这是一个很好的工具。

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)](https://ko-fi.com/A0A5WBC1) |