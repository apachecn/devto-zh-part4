# 使用外部认证自动登录 Django 应用程序

> 原文：<https://dev.to/ybri/automatic-login-in-a-django-application-using-external-authentication-4bcf>

Django 是最著名的 Python 框架之一。在本教程中，我将解释如何使用 LDAP 在其内置的管理界面中自动登录用户。

Django 提供了一个非常有用的管理界面，允许管理用户、显示和修改模型数据。但是你试过在大公司集成 Django 应用吗？

我有，而且大多数时候，他们已经有了一个认证系统来认证他们的用户。例如，未来 1000 家公司中有 95%使用微软的活动目录来管理他们的用户。

因此，用户在整个网络上都是经过认证的，没有必要添加带有自定义凭证的 Django Admin 登录页面。更不用说公司安全部门可能不会接受不同的凭证数据库。

**让我们用一个具体的例子**来看看如何让用户自动登录👇
T3】https://medium . com/si pios/automatic-log in-django-LDAP-authentic ation-tutorial-91 cbb 9 D5 BF 71