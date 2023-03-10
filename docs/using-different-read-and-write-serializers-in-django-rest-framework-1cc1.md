# 在 Django REST 框架中使用不同的读写序列化器

> 原文：<https://dev.to/revsys/using-different-read-and-write-serializers-in-django-rest-framework-1cc1>

**版本**:

*   Python 3.7
*   姜戈 2.2
*   Django REST 框架 3.10

在最近的一个项目中，我们需要对 Django REST 框架 API 的 GET 和 POST/PUT/PATCH 请求使用不同的序列化器。在我们的例子中，这是因为 GET 序列化程序包含了大量的嵌套数据；例如，它包含从其他序列化程序到外键关系的扩展字段。但是，通过 API 更新数据的请求不需要这些扩展字段。

我们对读取和更新操作使用不同序列化程序的第一种方法是在每个视图集上覆盖`get_serializer_class()`,根据请求中的操作决定返回哪个序列化程序。我们为`list`和`retrieve`动作返回了“读取”序列化程序，为其他所有动作返回了“更新”序列化程序。(API 动作的完整列表在 DRF 代码库中的[。)但是我们最终在几个视图集中重复了自己，所以我们写了一个 mixin 来替我们处理这些工作！](https://github.com/encode/django-rest-framework/blob/335054a5d36b352a58286b303b608b6bf48152f8/rest_framework/schemas/coreapi.py#L39)

mixin 是包含定制属性和方法的 Python 类([更多解释](https://easyaspython.com/mixins-for-fun-and-profit-cb9962760556?gi=3875fd6a6ff8))。它本身不是很有用，但是当它被继承到一个类中时，这个类就可以访问 mixin 的特殊属性和方法。

这是我们的 mixin:

```
class ReadWriteSerializerMixin(object):
    """
    Overrides get_serializer_class to choose the read serializer
    for GET requests and the write serializer for POST requests.

    Set read_serializer_class and write_serializer_class attributes on a
    viewset. 
    """

    read_serializer_class = None
    write_serializer_class = None

    def get_serializer_class(self):        
        if self.action in ["create", "update", "partial_update", "destroy"]:
            return self.get_write_serializer_class()
        return self.get_read_serializer_class()

    def get_read_serializer_class(self):
        assert self.read_serializer_class is not None, (
            "'%s' should either include a `read_serializer_class` attribute,"
            "or override the `get_read_serializer_class()` method."
            % self.__class__.__name__
        )
        return self.read_serializer_class

    def get_write_serializer_class(self):
        assert self.write_serializer_class is not None, (
            "'%s' should either include a `write_serializer_class` attribute,"
            "or override the `get_write_serializer_class()` method."
            % self.__class__.__name__
        )
        return self.write_serializer_class 
```

这个 mixin 定义了两个新属性，`read_serializer_class`和`write_serializer_class`。每个属性都有一个相应的方法来捕捉使用 mixin 时的错误，但是这些属性还没有被设置。如果您的视图集没有设置适当的属性或覆盖必要的方法，`get_*_serializer_class()`方法将引发一个`AssertionError`。

`get_serializer_class`方法最终决定使用哪个序列化器。对于 API 的“更新”动作，它返回`write_serializer_class`；否则它返回`read_serializer_class`。

mixin 在视图集中的用法如下:

```
from rest_framework import viewsets

from .mixins import ReadWriteSerializerMixin
from .models import MyModel
from .serializers import ModelReadSerializer, ModelWriteSerializer

class MyModelViewSet(ReadWriteSerializerMixin, viewsets.ModelViewSet):
    queryset = MyModel.objects.all() 
    read_serializer_class = ModelReadSerializer 
    write_serializer_class = ModelWriteSerializer 
```

现在视图集`MyModelViewSet`可以访问 mixin `ReadWriteSerializerMixin`中的属性和方法。这意味着当对使用`MyModelViewSet`的 API 进行调用时，来自`ReadWriteSerializerMixin`的`get_serializer_class()`方法将被自动调用，并根据 API 请求的类型决定使用哪个序列化程序。如果我们需要对返回的序列化器做出更细粒度的决定(也许我们希望对`list`请求使用更有限的序列化器，而在`retrieve`请求中使用更多数据的序列化器)，那么我们的视图集可以覆盖`get_write_serializer_class()`来添加该逻辑。

注意:自定义 DRF 动作将包含不属于 DRF 接受动作列表的动作(因为它们是您正在创建的自定义动作)，因此当您从 action 方法内部调用`get_serializer_class`时，它将返回您的“默认”序列化程序类。在上面的例子中,“默认”的序列化器是`read_serializer_class`,因为它是我们通过其他条件时返回的。

根据您的操作，您可能希望覆盖`get_serializer_class`来更改您的默认方法或显式说明您的自定义操作。

Mixins 是一种简单(不要重复)的方式来添加您最终需要跨多个视图集使用的功能。我们希望你能很快尝试使用它们！

感谢 Jeff Triplett 对这篇文章的帮助。