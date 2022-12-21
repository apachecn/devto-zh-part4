# 将您的 Django 生成的模型分割成单独的文件

> 原文：<https://dev.to/victorqribeiro/split-your-django-generated-models-into-separated-files-2ade>

# 分割 Django 模型

将 Django 生成的模型分割成单独的文件，并使用正确的名称、类和导入。

## 用法

生成您的模型:

```
python3 manage.py inspectdb > models.py 
```

运行脚本:

```
./split_django_models.sh models.py 
```

把它们都放在一个文件夹里，这样你就可以享受下面的步骤了:

该脚本还创建了一个 *__init__。py* 文件，这样你就可以一次全部导入:

```
from yourapp.models import * 
```

在这里获取脚本