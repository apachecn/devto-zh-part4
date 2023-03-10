# 登录最佳实践

> 原文：<https://dev.to/tmblog/login-best-practices-3hpc>

想得到一些关于使用 php 和 mysql 登录的建议。

一般流程是在验证数据等之后:

`SELECT username, password FROM users WHERE username = $_POST['username'];`

然后使用`password_verify()`来检查哈希是否匹配。

问题是先查询用户名是否更好

`SELECT username FROM users WHERE username = $_POST['username'];`

如果得出结果，则:

`SELECT password FROM users WHERE username = $_POST['username'];`

其次是:

`password_verify($_POST['password'], $passwordFromDatabase);`

这是一个更好更安全的方法吗？至少从表面上看，如果用户名不正确，密码不会暴露。任何关于最佳实践的建议以及哪种方法更好/不同，我们将不胜感激！