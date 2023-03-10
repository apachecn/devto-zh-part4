# 编写 Django 自定义命令

> 原文：<https://dev.to/arsho/writing-django-custom-command-1dl0>

当使用 Django 框架开发应用程序时，我们会大量使用各种 Django 命令。我们经常使用的 Django 命令有:

*   `python manage.py runserver`
*   `python manage.py makemigrations`
*   `python manage.py migrate`

这些命令内置于 Django 中。

我们也可以编写自定义 Django 管理命令。今天我将展示一个编写自定义 Django 管理命令的例子。

可以使用`manage.py COMMAND_NAME`调用这些自定义管理命令。

要添加自定义 Django 管理命令，首先我们需要添加一个目录`management/commands`到任何一个 apps 文件夹。

假设，我们需要编写一个定制的 Django admin 命令，将 CSV 文件中的一些数据插入到现有的模型中。

我们把这个命令命名为`insert_upazila_office_reports.py`。

我们有以下 CSV 文件:

*   `acland_offices_rank.csv`
*   `uno_offices_rank.csv`

将文件放入各自的目录后，目录结构可能如下:

```
APPLICATION_ROOT
├── manage.py
├── PROJECT_ROOT
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── APP1
│   ├── admin.py
│   ├── apps.py
│   ├── fixtures
│   │   ├── fixture_1.json
│   │   └── fixture_2.json
│   ├── __init__.py
│   ├── management
│   │   └── commands
│   │       ├── insert_upazila_office_reports.py
│   │       ├── acland_offices_rank.csv
│   │       └── uno_offices_rank.csv
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   └── __init__.py
│   ├── models.py
│   ├── templates
│   │   ├── base_generic.html
│   │   └── index.html
│   ├── tests.py
│   ├── urls.py
│   └── views.py
└── requirements.txt 
```

CSV 文件的名称将作为参数传递给 Django 命令。

我们命令的预期功能是将数据从传递的 CSV 文件插入到现有模型中。

该命令将 CSV 文件中的数据插入到`UNOOfficeReport`模型中，假设 CSV 文件名已传递。
此外，如果`--acland`可选参数通过，它将向`ACLandOfficeReport`模型插入数据。

让我们创建`insert_upazila_office_reports.py`。

```
import csv
import os
from django.apps import apps
from django.core.management.base import BaseCommand, CommandError
from reports.models import UNOOfficeReport, ACLandOfficeReport

class Command(BaseCommand):
    help = "Insert Upazila office reports from a CSV file. " \
           "CSV file name(s) should be passed. " \
           "If no optional argument (e.g.: --acland) is passed, " \
           "this command will insert UNO office reports."

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.model_name = UNOOfficeReport

    def insert_upazila_report_to_db(self, data):
        try:
            self.model_name.objects.create(
                upazila=data["upazila"],
                rank=data["rank"],
                office_name=data["office_name"]
            )
        except Exception as e:
            raise CommandError("Error in inserting {}: {}".format(
                self.model_name, str(e)))

    def get_current_app_path(self):
        return apps.get_app_config('reports').path

    def get_csv_file(self, filename):
        app_path = self.get_current_app_path()
        file_path = os.path.join(app_path, "management",
                                 "commands", filename)
        return file_path

    def add_arguments(self, parser):
        parser.add_argument('filenames',
                            nargs='+',
                            type=str,
                            help="Inserts Upazila Office reports from CSV file")
        # Named (optional) arguments
        parser.add_argument(
            '--acland',
            action='store_true',
            help='Insert AC land office reports rather than UNO office',
        )

    def handle(self, *args, **options):
        if options['acland']:
            self.model_name = ACLandOfficeReport

        for filename in options['filenames']:
            self.stdout.write(self.style.SUCCESS('Reading:{}'.format(filename)))
            file_path = self.get_csv_file(filename)
            try:
                with open(file_path) as csv_file:
                    csv_reader = csv.reader(csv_file, delimiter=',')
                    for row in csv_reader:
                        if row != "":
                            words = [word.strip() for word in row]
                            upazila_name = words[0]
                            office_name = words[1]
                            rank = int(words[2])
                            data = {}
                            data["upazila"] = upazila_name
                            data["office_name"] = office_name
                            data["rank"] = rank
                            self.insert_upazila_report_to_db(data)
                            self.stdout.write(
                                self.style.SUCCESS('{}_{}: {}'.format(
                                        upazila_name, office_name,                                        
                                        rank
                                        )
                                )
                            )

            except FileNotFoundError:
                raise CommandError("File {} does not exist".format(
                    file_path)) 
```

我们可以调用如下命令:

```
python manage.py insert_upazila_office_reports uno_offices_rank.csv 
```

或者

```
python manage.py insert_upazila_office_reports --acland acland_offices_rank.csv 
```

### 关于编写自定义 Django 管理命令的重要事实

*   该命令应该是从`django.core.management.base`扩展`BaseCommand`类的 Python 类
*   命令文件应该放在`management/commands`目录下。
*   如果你在`BaseCommand`的子类中实现`__init__`，你必须调用`BaseCommand`的`__init__`:

```
 class Command(BaseCommand):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # ... 
```

获取自定义 Django 管理命令细节的图:

```
(venv) ➜  nothi git:(master) python manage.py insert_upazila_office_reports --help
usage: manage.py insert_upazila_office_reports [-h] [--acland] [--version]
                                               [-v {0,1,2,3}]
                                               [--settings SETTINGS]
                                               [--pythonpath PYTHONPATH]
                                               [--traceback] [--no-color]
                                               [--force-color]
                                               filenames [filenames ...]

Insert Upazila office reports from a CSV file. CSV file name(s) should be
passed. If no optional argument (e.g.: --acland) is passed, this command will
insert UNO office reports.

positional arguments:
  filenames             Inserts Upazila Office reports from CSV file

optional arguments:
  -h, --help            show this help message and exit
  --acland              Insert AC land office reports rather than UNO office
  --version             show program's version number and exit
  -v {0,1,2,3}, --verbosity {0,1,2,3}
                        Verbosity level; 0=minimal output, 1=normal output,
                        2=verbose output, 3=very verbose output
  --settings SETTINGS   The Python path to a settings module, e.g.
                        "myproject.settings.main". If this isn't provided, the
                        DJANGO_SETTINGS_MODULE environment variable will be
                        used.
  --pythonpath PYTHONPATH
                        A directory to add to the Python path, e.g.
                        "/home/djangoprojects/myproject".
  --traceback           Raise on CommandError exceptions
  --no-color            Don't colorize the command output.
  --force-color         Force colorization of the command output. 
```

### 引用:

*   关于编写自定义 django-admin 命令的 Django 文档

这篇文章最初发表在[https://arshovon.com/blog/django-custom-command/](https://arshovon.com/blog/django-custom-command/)

**更新**

将 Django 自定义命令添加到 Github 代码库中的 *Django Docs 示例投票应用程序*中:【https://github.com/arsho/django-custom-command】T2