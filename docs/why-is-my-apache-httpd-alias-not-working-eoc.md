# 为什么我的 Apache httpd 别名不起作用？

> 原文：<https://dev.to/pfreitag/why-is-my-apache-httpd-alias-not-working-eoc>

今天碰到这个，所以我想我应该把它写在博客上。

我在 httpd.conf 中有一个别名，比如:

```
Alias /virtual-directory /path/to/files 
```

我不得不更改别名以指向一个新的文件夹，所以我注释掉了旧的，并添加了一个新的，如下所示:

```
Alias /virtual-directory/ /different/path/to/files 
```

现在我的`Alias`坏了！我收到了一个 404 状态码响应。

当您更改目录时，别名可能失败的一个原因可能是,`Directory`指令不允许访问该文件夹，但这里不是这种情况，这可能会导致 403 响应而不是 404。

也许您已经发现了我的例子中的问题，但是我是通过查看 Apache 服务器错误日志发现问题的。我有一个类似于
的条目

```
File does not exist: /different/path/to/filesindex.html 
```

正如你现在看到的，我的第一个(工作别名)被设置为 URI `/virtual-directory`，但是我的第二个有一个尾随斜线`/virtual-directory/`

这个故事的寓意是确保后面的斜杠与别名及其路径匹配，否则您可能会遇到这个问题。