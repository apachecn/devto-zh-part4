# 动态属性，感谢神奇的方法。

> 原文：<https://dev.to/courtjus/dynamic-attribute-thanks-to-super-methods-3igl>

# 场景

为了优化我们的应用程序，我在 QuerySet 上添加了一个注释，以便能够过滤这个动态属性并执行批量操作，而无需迭代 QuerySet 中的所有对象:

所以我可以替换它(简化的例子):

```
class MyModel(models.Model):
    attr1 = models.DateTimeField()
    attr2 = models.IntegerField()

    def attr3(self):
        return self.attr1 + timedelta(days=attr2 * 3)

def my_function():
    for instance in MyModel.objects.all():
        # do some work involving attr3 
```

由那(不完整的例子):

```
class MyManager(models.Manager):
    def get_queryset(self):
        return self.custom_annotation(super(MyManager, self).get_queryset())

    def custom_annotation(self, qs):
        return qs.annotate(
            attr3=  # Details of annotation outside the scope of this post
        )

class MyModel(models.Model):
    attr1 = models.DateTimeField()
    attr2 = models.IntegerField()

    objects = MyManager()

def my_function():
    MyModel.objects.filter(
        attr3=...  # Details of filter outside the scope of this post
    ).update(
        # Details of operation outside the scope of this post
    )

def my_other_function():
    instance = MyModel.objects.get(pk=42)
    instance.attr3  # the instance is annotated thanks to the QuerySet annotation 
```

好吧，到目前为止还不错，但这有一个缺陷。当你直接访问你的模型时(通过一个`QuerySet`或者一个实例)，它是被注释的。当你通过`ManyToMany`访问它时，它也会被注释，但当它被用作`ForeignKey` :
时就不会了

```
class MyModel2(models.Model):
    foreign = models.ForeignKey(MyModel)

def my_function():
    instance2 = MyModel2.objects.get(pk=57)
    instance2.foreign.attr3  # AttributeError 
```

# 解

所以我想让`MyModel`动态地行动，并在需要时获取`attr3`属性，如果必要的话，代价是一个额外的 SQL 查询。

这可以通过像这样覆盖`__getattr__`来实现:

```
class MyModel(models.Model):
    attr1 = models.DateTimeField()
    attr2 = models.IntegerField()

    objects = MyManager()

    def __getattr__(self, attrname):
        if attrname == "attr3":
            try:
                return object.__getattribute__(self, attrname)
            except AttributeError:
                value = Screen.objects.values_list("attr3", flat=True).get(pk=self.pk)
                setattr(self, attrname, value)
                return value
        return object.__getattribute__(self, attrname) 
```

重写`__getattr__`时要小心，你很容易陷入无限循环。

另外，需要注意的是`__getattr__`只在访问一个丢失的属性时被调用，而`__getattribute__`在每次访问一个属性时被调用。

我使用`object.__getattribute__`是为了避免递归。

# 链接

*   Python 文档:[https://docs.python.org/3/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)
*   关于这两种神奇方法的区别的讨论:[https://stack overflow . com/questions/3278077/difference-between-getattr-vs-getattribute](https://stackoverflow.com/questions/3278077/difference-between-getattr-vs-getattribute)

> Clem Onojeghuo 在 Unsplash 上拍摄的照片