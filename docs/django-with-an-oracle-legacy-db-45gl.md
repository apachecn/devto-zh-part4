# Django 使用 Oracle 遗留数据库

> 原文：<https://dev.to/rpost/django-with-an-oracle-legacy-db-45gl>

# 问题

我正在做一个需要将 Oracle 遗留数据库连接到 Django 项目的项目。我慢慢地发现了这些步骤，而且是在各种不同的地方，所以我把它们放在一起，以防将来有人遇到类似的问题！

# 基础知识

Django 中的数据库连接入门在文档中有详细介绍。在这里，您可以找到如何在 settings.py 文件中设置数据库信息。**请记住，您的密码不应出现在您提交给 repo 的 settings.py 文件中。**存储密码有几个选项，我用的是 local_settings.py 文件。在我的`settings.py`文件中，我的数据库看起来像这样:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': 'xe',
        'USER': 'a_user',
        'PASSWORD': '',
        'HOST': '',
        'PORT': '',
    }
} 
```

请注意，密码是一个空字符串。然后我的`local_settings.py`包括密码:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': 'xe',
        'USER': 'a_user',
        'PASSWORD': 'a_password',
        'HOST': '',
        'PORT': '',
    }
} 
```

Django 文档提到了`tnsnames.ora`，这是我连接到遗留数据库的方式，因为它允许您使用服务名。然而，文档并没有说明`tnsnames.ora`在项目结构中的位置。使用 Django 1.4 以来的标准结构，我把我的放在这里:

```
my_project
├── manage.py
├── my_app
│   ├── [...]
└── my_project
    ├── __init__.py
    ├── _batch_settings.py
    ├── settings.py
    ├── urls.py
    └── oracle           <~~~ Create this folder!
        └── tnsnames.ora <~~~ Create this file! 
```

我听说 Oracle 对行尾非常挑剔，所以我在虚拟机中创建了 tnsnames.ora 文件。我被宠坏了，因为我的组织有一个标准的虚拟机使用，所以我很抱歉，如果你不能很容易地做到这一点，并有行尾的麻烦。

现在你已经创建了新的文件夹和文件，告诉 Django 它在哪里。在您的`settings.py`文件中，您应该将您的`TNS_ADMIN`环境变量设置为指向在您的项目文件夹中创建的 oracle 目录:
`os.environ['TNS_ADMIN'] = os.path.join(BASE_DIR, '<project_name>', 'oracle')`

# 识别 DB

好了，那么基础准备好了！作为一个从未使用遗留数据库启动过 Django 应用程序的人，我立刻又陷入了困境。但是接下来的步骤对于任何数据库都是一样的。由于我不想建立一个新的数据库，我不确定我是否需要这样做，但这是必要的。转到终端/命令行并:

```
$ python manage.py makemigrations <appname>
$ python manage.py migrate <appname> 
```

执行完这些命令后,“终端”窗口会显示每个步骤的进度。

现在，如果幸运的话，您的模式中有您需要的表或视图，您可以使用`inspectdb`命令让您的模型有一个良好的开端。事实上，这是[推荐的在文档中集成遗留数据库的方式。](https://docs.djangoproject.com/en/2.2/howto/legacy-databases/)如果`inspectdb`适合您，请直接跳到测试。但是，如果您的模式**不**包含任何表或视图，并且只允许您访问另一个模式中的表，那么我们的公主就在另一个城堡里了。我们去找她。

# 下一座城堡

如果没有`inspectdb`的帮助，你需要写一个你自己的小模型，然后你才能确定你连接正确。如果您想从小的东西开始测试，这可能会很短。我建议至少包含两个字段:一个用于查询，另一个用于证明你得到了你想要的。您还需要包含`class Meta`来告诉 Django ORM 您想要查询哪个模式和表。一款基本款可以很短:

```
class Student(models.Model):
    id = models.IntegerField(primary_key=True)
    name = models.CharField(max_length=50, blank=True, null=True)

    class Meta:
        managed = False
        db_table = '"<SCHEMA_NAME>"."<TABLE_NAME>"' 
```

注意 db_table 的所有单引号和双引号——Oracle 非常笨，只有以完全正确的方式告诉它，它才能找到模式和表。现在你有了一个新模型，重新运行你的`python manage.py makemigrations <appname>`。这将确保姜戈知道你的新型号。像以前一样用`python manage.py migrate <appname>`跟进。是的，你两者都需要做。

# 测试我们的连接是否可以获得数据

好吧！我们有我们的`DATABASES`条目，我们的`tnsnames.ora`文件和一些在`models.py`的东西。我们去贝壳吧！

```
$ python manage.py shell 
```

我们现在处于充满 django 的神奇外壳环境中。您需要告诉 shell 在哪里可以找到您的模型，然后在其中检索一些数据。为了确保检索到某些内容，请在数据中查找已知的行。

```
In[1]: from <appname>.models import *
In[2]: test = Student.objects.get(id=<id that really exists>)
In[3]: test.name
     <you should see the correct related field info here!> 
```

如果您看到的不是漂亮的数据，而是`NameError: name 'Students' is not defined`，那么您可能 1)键入了您的模型的类名，或者 2)没有运行上面的迁移命令。

# 勇往直前，征服

庆祝你的成功！既然您已经证明了 Django 项目连接到了 Oracle 遗留数据库，那么就去获取数据并做您真正需要做的事情吧！如果您必须编写最初的测试模型，您将需要手动编写其余的模型。不好玩，而且更容易出错，但是我还没有找到一个解决方法。不过，我希望我是错的，所以如果你有解决方法，请告诉我！

如果这是您第一次通过 Django 模型提取数据，请花些时间熟悉一下 [Django ORM(对象关系映射层)](https://docs.djangoproject.com/en/2.2/topics/db/)。这是一种说“学习如何使用你刚刚生成或编写的酷模型”的奇特方式那是我接下来要去的地方。