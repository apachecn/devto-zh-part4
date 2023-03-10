# 创建现有数据库的 Django 模型

> 原文：<https://dev.to/idrisrampurawala/creating-django-models-of-an-existing-db-288m>

Django 以其 ORM 效率而闻名。更多的时候，我们会遇到这样一种情况，我们希望将现有的数据库集成到一个新的后端框架中。第一步通常是(ORM 支持框架)创建数据库模型。在本文中，我们将学习如何创建现有数据库的 Django 模型。

# 数据库设置

很有可能将 Django 集成到遗留数据库中。第一步是为 Django 建立数据库连接，以连接到`settings.py`文件中的现有数据库。

# Django inspectdb 命令

一旦在 Django 中建立了数据库连接，就可以自动生成模型。是的，你没听错！Django 提供了一个实用程序，通过`inspectdb`命令从现有数据库自动生成模型。

通过执行以下命令，您可以通过检查现有数据库来创建模型:

```
$ python manage.py inspectdb 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将把 Django 可以从现有数据库创建的所有模型输出到 stdout。您可以使用标准的 Unix 输出重定向将它保存为一个文件:

```
$ python manage.py inspectdb > models.py 
```

Enter fullscreen mode Exit fullscreen mode

输出文件将保存到您的当前目录。将该文件移动到正确的应用程序，您就有了进一步定制的良好起点。

记住:在运行 inspect db 之前，您需要运行 Django 项目

# inspectdb 的更多选项

`inspectdb`命令默认不带任何参数，输出数据库中的所有表格。如果您想要自省特定的表，您可以在命令:
之后将表名作为一个由空格分隔的参数来传递

```
$ python manage.py inspectdb table1 table2 
```

Enter fullscreen mode Exit fullscreen mode

更多信息可以参考 Django [文档](https://docs.djangoproject.com/en/2.2/ref/django-admin/#django-admin-inspectdb)。

# 点记

*   如果`inspectdb`不能将列的类型映射到模型字段类型，它将使用*文本字段*，并在生成的模型中的字段旁边插入 Python 注释`'This field type is a guess.'`。请留意这一点，并根据需要相应地更改字段类型。
*   如果数据库列名是 Python 保留字(如`pass`、`class`或`for`)，则`inspectdb`会将`'_field'`附加到属性名，并将`db_column`属性设置为真正的字段名(如`pass`、`class`或`for`)。
    例如，如果一个表有一个名为`for`的`INT`列，那么生成的模型将有一个这样的字段:

    `for_field = models.IntegerField(db_column='for')`

    `inspectdb`将在字段旁边插入 Python 注释`'Field renamed because it was a Python reserved word.'`。

*   如果您的数据库包含引用其他表的表(大多数数据库都是这样)，您可能需要重新安排生成的模型的顺序，以便引用其他模型的模型能够正确排序。

*   当在模型字段上指定默认值时，Django 不会创建数据库`defaults`。类似地，数据库默认值不会被转换为模型字段默认值，也不会被`inspectdb`以任何方式检测到。

*   默认情况下，`inspectdb`创建`unmanaged models`。也就是说，模型的`Meta`类中的`managed = False`告诉 Django 不要管理每个表的创建、修改和删除。如果您确实想让 Django 管理表的生命周期，您需要将`managed`选项改为`True`(或者简单地删除它，因为 True 是它的默认值)。

*   每个数据库表被转换成一个模型类(即，在数据库表和模型类之间存在一对一的映射)。这意味着您需要将任何多对多连接表的模型重构为`ManyToManyField`对象。

*   外键检测只适用于 PostgreSQL 和某些类型的 MySQL 表。在其他情况下，外键字段将生成为:

    `IntegerField's, assuming the foreign-key column was an` `INT`列。

这个特性是一种快捷方式，而不是最终的模型生成。在您运行它之后，您将希望亲自查看生成的模型，以便进行定制。