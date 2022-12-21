# 了解 Django 用户认证系统

> 原文：<https://dev.to/easyaspython/learn-the-django-user-authentication-system-36pl>

## 简介

让用户能够创建他们可以登录的帐户是许多网站的常见功能。

用户可能需要一个帐户来参与评论线程、保存他们的个人信息或转账。无论用例是什么，您都需要为您的用户构建一个简单而安全的身份验证系统。

读完这篇文章后，您应该对 Django 如何看待身份验证有了很深的理解——从用户、组到权限。您还将看到 Django 如何在可能的情况下确保安全，以帮助您避免无意中将您的用户信息提供给“[when I was Pwned](https://haveibeenpwned.com/)”。

## 用户

对于大多数网站来说，认证的基本实体是用户。用户由一些唯一的字符串来标识，这些字符串通常是电子邮件地址或用户名。

为了证明某人是他们所说的人，他们必须在创建帐户时提供密码，并且在任何时候都要验证自己。这应该很熟悉:任何时候你注册 Twitter 或网飞这样的服务，都会经历这种工作流程。

Django 为创建和管理用户提供了一个用户模型。Django 用户有一个用户名和密码，但是也可以选择有一个电子邮件地址和一个姓和名:

```
from django.contrib.auth.models import User

rafaela = User('rafaela', password='$uper$ecretpassword')

# OR 
rafaela = User(
    'Rafaela',
    email='rafaela@example.com',
    password='$upser$ecretpassword',
    first_name='Rafaela',
    last_name='Lòpez',
) 
```

Enter fullscreen mode Exit fullscreen mode

如果您喜欢通过用户的电子邮件地址来识别用户，我建议在用户名中填入电子邮件地址，并在电子邮件字段中保留该地址。这将允许用户使用他们的电子邮件地址进行身份验证，同时也允许您继续使用 Django 处理电子邮件的内置特性。

Django 在密码方面提供了一定程度的安全性。它有一组内置的密码验证器，其中一些在新项目中默认启用。您可以编写自己的验证器来执行您可能需要的任何密码规则，但是要明智地选择——已经证明[许多密码规则会导致安全性降低](https://www.riskcontrolstrategies.com/2018/01/08/new-nist-guidelines-wrong/)！

除了密码验证之外，Django 默认安全地存储密码信息。Django 在创建用户时存储密码之前会对密码进行加盐和哈希处理，因此在初始注册请求的上下文之外或者当用户登录时，他们的明文密码不再可用。

以明文形式存储密码是业内令人惊讶的常见疏忽，所以让 Django 成为您的安全护栏吧！

像你在 Django 中使用的其他模型一样，用户对象可以被查询和过滤等等:

```
User.objects.filter(first_name='Rafaela') 
```

Enter fullscreen mode Exit fullscreen mode

用户对象还有其他几个字段、属性和方法，当您阅读涉及用户的 Django 特性时，它们在上下文中会有意义。

在[阅读更多关于组、权限和更多内容的完整文章](https://kite.com/blog/python/django-authentication/)！