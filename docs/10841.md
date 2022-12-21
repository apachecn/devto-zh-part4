# Django 无需在现有数据库上运行迁移即可建立关系模型

> 原文：<https://dev.to/idrisrampurawala/django-model-relationships-without-running-migration-on-existing-database-1haf>

在我之前的文章中，我们讨论了如何使用命令`inspectdb`创建现有数据库的 Django 模型。延伸到那篇文章，在这篇文章中，我们将尝试在现有表没有外键约束的模型之间创建外键关系。此外，由于它是遗留系统，我们的用例是不运行这些约束的迁移。

[![idrisrampurawala](img/dec12b5114e4f60ef09fea08f41a9cca.png)](/idrisrampurawala) [## 创建现有数据库的 Django 模型

### Idris Rampurawala 7 月 24 日 193 分钟读数

#python #django #backend #webdev](/idrisrampurawala/creating-django-models-of-an-existing-db-288m)

# 问题

*   我们已经为现有的数据库创建了 Django 模型
*   我们现有的数据库在任何表上都没有任何外键约束
*   我们想利用 Django-ORM 来查询相关的表
*   我们不希望在现有数据库中迁移/创建这些约束，因此我们的数据库将保持不变

# 解

为了实现这一点，让我们考虑以 Django 模型为例

```
class Manufacturer(models.Model):
    pass

class Car(models.Model):
    manufacturer_id = models.IntegerField() 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们的动机是改变模型，将`manufacturer_id` `IntegerField`变成`manufacturer` `ForeignKey`，即

```
class Manufacturer(models.Model):
    pass

class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE,
                                     related_name="manufacturer") 
```

Enter fullscreen mode Exit fullscreen mode

这可以通过两个步骤实现:

## 步骤 1 -修改 Django 模型中的字段

我们将把`IntegerField`字段修改为`ForeignKey`字段

```
class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE,
                                     related_name="manufacturer") 
```

Enter fullscreen mode Exit fullscreen mode

## 步骤 2 -添加虚拟迁移条目

我们将在`appName/migrations`
下手动创建一个迁移文件

```
# Generated manually 
from django.db import migrations, models

class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        # Car - Manufacturer Relation
        migrations.AlterField(
            model_name='Car',
            name='manufacturer_id',
            field=models.IntegerField(db_column='manufacturer_id')
        ),
        migrations.RenameField(
            model_name='Car',
            old_name='manufacturer_id',
            new_name='manufacturer',
        ),
        migrations.AlterField(
            model_name='Car',
            name='manufacturer',
            field=models.ForeignKey(
                to='appName.Manufacturer', on_delete=models.CASCADE),
        ),
    ]
# Note: Change appName to your app name consisting the models 
```

Enter fullscreen mode Exit fullscreen mode

一旦您创建并保存了这个迁移文件，您现在就可以访问相关的模型了。请记住，我们不是在数据库上运行迁移！这里的技巧是，我们只是让 Django 知道模型关系，但实际上它并不存在。

可能有多种方法可以达到这个效果，但是请放心，我也在产品中尝试过。

希望这个有用！