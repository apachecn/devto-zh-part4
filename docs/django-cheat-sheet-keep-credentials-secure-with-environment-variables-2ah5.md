# Django 备忘单:用环境变量保证凭证安全

> 原文：<https://dev.to/jamestimmins/django-cheat-sheet-keep-credentials-secure-with-environment-variables-2ah5>

TL；硬编码配置值和凭证很方便，但是会降低代码的安全性和可移植性。使用环境变量使您的代码更安全，更易于在不同的环境中部署。

坏:

```
TWILIO_SECRET_KEY = "iamverysneaky"
twilio_client = Twilio(key=TWILIO_SECRET_KEY) 
```

问题:
如果有人可以访问你的代码，现在他们也可以访问你的 Twilio 账户！一个问题两个问题！

好:

```
from dotenv import load_dotenv

load_dotenv()

twilio_client = Twilio(key=os.getenv("TWILIO_SECRET_KEY")) 
```

如果有人获得了你的代码，至少你的 Twilio 账户(和用户数据！)还是安全的。

为了说明这是如何工作的，我们将把自动生成的`SECRET_KEY`值从`settings.py`中移出，放入一个环境变量中。

由此:

```
SECRET_KEY="thisismyunsecuredsecretkey" 
```

对此:

```
SECRET_KEY=os.getenv("DJANGO_SECRET_KEY") 
```

做这些事情:

1.  下载 dotenv 包。

    ```
    $ pip install python-dotenv 
    ```

2.  在与`settings.py`相同的目录下创建一个名为`.env`的文件。

    ```
    $ touch .env 
    ```

3.  将`.env`文件添加到您的`.gitignore`中。这是最重要的一步，它将`.env`，也就是你的秘密值，置于版本控制/Git 之外。

    ```
    $ echo .env >> .gitignore 
    ```

4.  将您的配置值和凭证添加到. env.

    ```
    $ echo 'DJANGO_SECRET_KEY="thisismyunsecuredsecretkey"' >> .env 
    ```

5.  将`os`和`dotenv`导入并加载到`settings.py`中。这使得这些值是可访问的。

    ```
    import os
    from dotenv import load_dotenv
    ...
    load_dotenv() 
    ```

6.  用环境变量查找替换原始的`SECRET_KEY`值。

    ```
    SECRET_KEY=os.getenv("DJANGO_SECRET_KEY") 
    ```

7.  利润！不让你的用户因为数据被盗而起诉你。GDPR 天哪！