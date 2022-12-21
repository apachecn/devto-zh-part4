# 【解决】为什么我的 Django app 即使 Debug = False 也不显示 404 页面？

> 原文：<https://dev.to/highcenburg/why-doesn-t-my-django-app-display-the-404-page-even-when-debug-false-33fe>

在过去的两天里，我想我已经尝试了我在网上找到的所有东西，但是我找不到正确的设置来显示错误页面。

我的观点是

```
...
from django.shortcuts import render, render_to_response
from django.template import RequestContext

def handler404(request, exception, template_name="404.html"):
    response = render_to_response("404.html")
    response.status_code = 404
    return response

def handler500(request, exception, template_name="500.html"):
    response = render_to_response("500.html")
    response.status_code = 500
    return response

```

Settings.py

```
DEBUG = (os.environ.get('DEBUG_VALUE') == 'False')

ALLOWED_HOSTS = ['*', '127.0.0.1']

```

Main urls.py

```
handler404 = 'portfolio.views.handler500'
handler500 = 'portfolio.views.handler500'

```

我使用 Python 3.7 和 Django 2.2

有人知道这个吗？

## [这是解决方案的链接](https://dev.to/pedromendes96/comment/c143)