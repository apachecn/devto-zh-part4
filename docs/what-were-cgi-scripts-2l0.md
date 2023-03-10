# 什么是 CGI 脚本？

> 原文：<https://dev.to/rickcarlino/what-were-cgi-scripts-2l0>

# 一个假设的场景

现在是 1996 年。Ruby on Rails 还需要 10 年才能问世。Javascript 还只是一个想法，而且不是所有的浏览器都支持它。

你从当地拨号上网服务提供商提供给你的[外壳账户](https://en.wikipedia.org/wiki/Shell_account)的`public_www`子目录中运行一个个人网站。

拥有一个 shell 帐户可以让你提供静态 HTML 文件，但在那个时代，如果你想生成动态内容，比如记录网站访问的[点击计数器](https://en.wikipedia.org/wiki/Web_counter)或留言簿(过去世界网站的评论部分)，事情就没那么简单了。

在本文中，我们将了解 CGI 脚本，这是早期 web 的一项技术，它使软件修补者能够构建简单(但有用)的动态 web 应用程序。

# CGI 脚本

CGI 代表“公共网关接口”。在 90 年代，CGI 脚本为希望托管动态呈现的服务器端内容的个人提供了最可行的选择。如果你以前从未见过这样的例子，看看我写的这个[示例脚本](http://rickcarlino.com/cgi-bin/hello.cgi)。它向访问者动态显示当前时间和用户代理。示例脚本是用 Bash 编写的，尽管历史上大多数 CGI 脚本是用 Perl 编写的([示例](https://www.scriptarchive.com/))。

我的示例脚本的源代码如下。如果你从未手工编写过 HTTP 请求，可以看看我写的关于 [HTTP 请求剖析](http://rickcarlino.com/2014/07/31/the-secret-lives-of-http-requests-html.html) :
的文章

```
#!/usr/bin/bash
echo "Content-type: text/html"
echo ""
echo '<html>'
echo ' <body>'
echo ' <p>The time is: '
# Prints current date/time to STDOUT:
date
echo ' </p>'
echo ' <p>Your HTTP user agent is:'
# A CGI-enabled server will set this ENV var at runtime:
echo $HTTP_USER_AGENT
echo ' </p>'
echo ' </body>'
echo '</html>'

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面的例子中看到的，所有对 STDOUT 的写入都被转换为 HTTP 响应。有些信息(比如`$HTTP_USER_AGENT`或者`$QUERY_STRING`)是服务器在运行时提供的。通过提供简单的输入(ENV)和输出(STDOUT)方式，CGI 脚本对于早期的 web 开发人员，尤其是业余爱好者和小规模开发人员来说是一个非常强大的工具。你所需要的只是一种编程语言和一个支持 CGI 的网络服务器。

CGI 脚本通常放在一个`cgi-bin`目录中。当服务器收到对特定 CGI 脚本的请求时，它会*执行*该脚本，而不是提供其内容。

这种做法最终在 RFC 3875 中被正式化。RFC 的许多合作者本身就是 HTTP 服务器的作者。

# 像 1996 年一样，所有的事情都发生了

CGI 脚本毫无疑问是有用的，并且将继续对小规模 web 应用程序有用，例如开发人员实用程序、简单表单数据收集和本地内部网工具。

CGI 脚本并不是适合每项工作的工具，它们的过时是更好的选择的结果。CGI 脚本提供的“每个请求一个进程”模型有许多问题，最明显的是 I/O 性能损失。使用数据库的 CGI 应用程序必须为每个请求打开一次到数据库的连接，这会导致连接开销。此外，一个有 100 个用户的 CGI 脚本将不可避免地创建 100 个操作系统进程(如果它正在执行子命令，可能会更多)。

CGI 脚本消亡的另一个原因是无法处理大规模应用程序的复杂性。在 CGI 脚本之后不久出现的 web 开发框架可以更好地处理这些问题。

此外，由于服务器直接执行脚本，安全问题很容易出现(脚本共享 HTTP 服务器的权限)。

CGI 脚本的低效率最终导致了更强大的解决方案的产生，如 [FCGI](https://en.wikipedia.org/wiki/FastCGI) 、 [SCGI](https://en.wikipedia.org/wiki/Simple_Common_Gateway_Interface) ，众多的 web 框架，以及最近的“功能即服务”平台，这些解决了列出的许多问题，同时还增加了额外的功能。

# 链接和资源

*   万维网上的 CGI 编程，作者 Shishir Gundavaram，1996 年 3 月
*   [RFC 3875](https://tools.ietf.org/html/rfc3875)
*   [HTTP 请求解剖](http://rickcarlino.com/2014/07/31/the-secret-lives-of-http-requests-html.html)
*   [我的示例脚本(如上)](http://rickcarlino.com/cgi-bin/hello.cgi)
*   [简单网关通用接口](https://en.wikipedia.org/wiki/Simple_Common_Gateway_Interface)
*   [谷歌找到的最好的 CGI 脚本存档](https://www.scriptarchive.com/)