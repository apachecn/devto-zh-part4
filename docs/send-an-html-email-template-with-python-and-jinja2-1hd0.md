# 用 Python 和 Jinja2 发送 HTML 邮件模板

> 原文：<https://dev.to/carola99/send-an-html-email-template-with-python-and-jinja2-1hd0>

这是我在 dev.to.
的第一篇文章，作为一个初学者开发者，作为一个练习，我已经学会了如何使用 Python 和 Jinja2 发送 HTML 电子邮件模板。

一开始，我专注于发送简单的纯文本、图像和模板文件的电子邮件。在了解了电子邮件的逻辑之后，我开始学习模板引擎以及如何将我的模板文件与一些数据结合起来，首先是使用 Python 的内置字符串格式化程序，然后是使用 Jinja2。

在这篇文章中，我将探索:

*   如何设置本地 SMTP(简单邮件传输协议)并使用 Python 发送电子邮件。
*   模板引擎
*   加载模板
*   模板继承
*   控制结构

### 设置本地 SMTP(简单邮件传输协议)

Python 包含用于发送电子邮件的内置 smtplib 模块，您可以发送纯文本、图像、附件和 HTML。

我开始安装测试电子邮件功能所需的一切。

导入 SMTP

```
from smtplib import SMTP 
```

Enter fullscreen mode Exit fullscreen mode

从 Python 也提供的 email.mime 模块导入 MIMEtext 和 MIMEMultipart。MIMEText()对象将包含 HTML，而 MIMEMultipart("alternative ")将把这些部分组合成一条消息。

```
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart 
```

Enter fullscreen mode Exit fullscreen mode

### 设置 Jinja2

从 Jinja2 导入环境、文件系统加载器和操作系统。对于 Jinja2，您可以使用 pip 或 easy_install。

```
from jinja2 import Environment, FileSystemLoader
import os 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Jinja2 加载模板。

Jinja2 的 FileSystemLoader 帮助从文件系统或其他位置获取模板，环境将加载它。

```
env = Environment(
    loader=FileSystemLoader('%s/templates/' % os.path.dirname(__file__))) 
```

Enter fullscreen mode Exit fullscreen mode

### JSON 格式的数据

```
def get_data():
    data = []
    data.append(
        {
         "movies": [
             {         
                 "title": 'Terminator',
                 "description": 'One soldier is sent back to protect her from the killing machine. He must find Sarah before the Terminator can carry out its mission.'
             },
             {                 
                 "title": 'Seven Years in Tibet',
                 "description": 'Seven Years in Tibet is a 1997 American biographical war drama film based on the 1952 book of the same name written by Austrian mountaineer Heinrich Harrer on his experiences in Tibet.'
             },
             {               
                 "title": 'The Lion King',
                 "description": 'A young lion prince is born in Africa, thus making his uncle Scar the second in line to the throne. Scar plots with the hyenas to kill King Mufasa and Prince Simba, thus making himself King. The King is killed and Simba is led to believe by Scar that it was his fault, and so flees the kingdom in shame.'
             }
         ]
         })
    return data 
```

Enter fullscreen mode Exit fullscreen mode

### 发送带有 HTML 内容的邮件

安装完所有必要的东西后，我创建了一个发送电子邮件的方法，它包括发送者、接收者、主题、HTML 和本地服务器。在这种情况下，我使用的是 Gmail 账户。

```
def send_mail(bodyContent):
    to_email = 'to@gmail.com'
    from_email = 'from@gmail.com'
    subject = 'This is a email from Python with a movies list!'
    message = MIMEMultipart()
    message['Subject'] = subject
    message['From'] = from_email
    message['To'] = to_email

    message.attach(MIMEText(bodyContent, "html"))
    msgBody = message.as_string()

    server = SMTP('smtp.gmail.com', 587)
    server.starttls()
    server.login(from_email, 'your password')
    server.sendmail(from_email, to_email, msgBody)

    server.quit() 
```

Enter fullscreen mode Exit fullscreen mode

我创建了第二个方法来获取数据，从我之前使用的环境中加载模板，我还调用了 get_template()方法，该方法将返回加载的模板，并使用我调用的 render()方法来呈现带有数据的模板。

```
def send_movie_list(event, context):
    json_data = get_data()
    template = env.get_template('child.html')
    output = template.render(data=jsonData[0])
    send_mail(output)    
    return "Mail sent successfully." 
```

Enter fullscreen mode Exit fullscreen mode

### jinja 2 中的模板引擎和模板继承

Jinja2 是 Python 的模板引擎。模板引擎允许我们将模板文件与数据模型结合起来，并生成所需的内容类型。当我们使用模板引擎时，有不同的选项，其中之一是 Python 的内置字符串格式化程序，但有时我们需要更强大的东西，这就是我选择 Jinja2 的原因。Jinja2 是一种现代的 Python 模板语言。您可以在框架内使用它，也可以不使用它。在这种情况下，我没有使用任何框架。

Jinja2 包含了模板继承，它定义了一个 HTML 框架文档。block 标签定义了一个子模板可以填充的块。您可以定义所需的块数。在这种情况下，我只有一个。我把这个模板叫做“base.html”。

```
<!DOCTYPE html>
<html lang="en">
<head>   
    Movies  
</head>
<body>
    <div id="content">{% block content %}{% endblock %}</div>     
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Jinja2 控制结构

我创建了第二个模板，名为 child，“child.html”，包含一个 for 循环来显示我们的电影和一个条件。

```
{% extends "base.html" %}
{% block content %}
    <h1>Movies</h1>
    <p>
      {% for movie in data['movies'] %} {%if movie['title']!="Terminator" %}
      {{ movie['title'] }}
      {% endif %} {% endfor %}
    </p>
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

搞定了！

你可以在这里阅读更多关于 Jinja2 的信息。