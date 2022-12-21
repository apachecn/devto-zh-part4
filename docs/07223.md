# 配置模板语言

> 原文：<https://dev.to/tttfifo/configuration-templating-language-51m6>

您是否遇到过需要手动编写多个配置文件的情况？我一直在管理多个路由器配置。路由器遵循相同的模式，但是每个路由器必须具有不同的开放/过滤端口、网络地址转换规则、不同的路由等。

这可以通过使用配置文件模板和一些脚本将变量数据推入模板来解决。虽然 jinja 模板和 python 脚本可能是一个相当好的解决方案，但我仍然在两个问题上挣扎:

*   是否存在一种更简单、更强大的模板语言来重写配置？
*   有没有简单的命令行工具用于模板化？

由于我无法找到我的问题的满意答案，我决定开始这个名为 [conftl -配置模板语言](https://github.com/ttt-fifo/conftl)的项目。让我举几个如何使用 conftl 的例子。

## 例 1:打开防火墙上的网络端口

我不想用所有的路由器细节来打扰您，所以我将只展示简单的 Linux iptables 防火墙示例。
创建模板文件 firewall.tmpl，内容如下:

```
# ... other iptables rules ...

{{for dport in dports:}}
-A INPUT -i eth0 -p tcp --dport {{=dport}} -m state --state NEW,ESTABLISHED -j ACCEPT
{{pass}}

# ... other iptables rules ... 
```

这个模板里有什么？我们看到我们可以嵌入 python 代码，用

`{{...}}`

包裹，那么这意味着什么呢？-每个对 Python 语法有所了解的人(Python 语法非常清晰)，都可以将 Python 结构嵌入到模板中。可能性是无穷的:条件表达式 if - else，while 和 for 循环，给变量赋值，甚至函数定义在模板中都是可能的。

我们在里面还看到了什么？

`{{=dport}}`

这个构造有点怪异。这就是我们将变量值打印到输出配置文件的方式。

另一件有趣的事情是特殊的关键字

`{{pass}}`

，它指示了我们的 for 循环块的结束位置。

一般来说，我们只是模板化了一个 Python for 循环，它在输出配置文件中为 dports 中的每个端口打印一个 iptables 规则。现在我们需要一种方法来呈现模板并指定打开哪些网络端口。这可以使用命令行实用程序“render”:

```
$ render -i firewall.tmpl -c "dports=[22, 80, 8080, 443]"

-i firewall.tmpl where to get the template from
-c "dports=[...]" gives the list with the ports to open 
```

输出将被打印到调试屏幕:

```
# ... other iptables rules ...

-A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -i eth0 -p tcp --dport 8080 -m state --state NEW,ESTABLISHED -j ACCEPT
-A INPUT -i eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT

# ... other iptables rules ... 
```

在实际情况下，您可能还想给出一个输出文件:

```
$ render -i firewall.tmpl -o firewall.conf -c "dports=[22, 80, 8080, 443]"

-o firewall.conf where to write the configuration file 
```

## 例 2: Nginx 代理

假设您需要动态配置反向代理。模板示例如下:

```
# ... other nginx configurations

<%for loc in locations:%>
<%server_port = locations[loc]%>
        location /<%=loc%> {
            rewrite ^/<%=loc%>(.*) /$1 break;
            proxy_pass http://<%=server_port%>;
        }
<%pass%>

# ... other nginx configurations 
```

这里你看不到

`{{...}}`

的图案。Nginx 配置文件使用

`{}`

括号来打开/关闭块，因此我们可以使用不同的分隔符

`"<% %>"`

来获得更好的可视性。

在本例中，我们不会在命令行中给出“locations”变量的值。我将创建另一个包含 json 上下文的文本文件。上下文意味着给模板变量赋值。让我们创建文件‘nginx . CTX’:

```
{"locations": {"blog": "192.168.43.15:8081",
               "mail": "192.168.43.15:8082"}} 
```

运行命令行工具后:

```
$ render -i nginx.tmpl -j nginx.ctx -d "<% %>"

-j tells render where to get json context from
-d "<% %>" instructs render for the delimiters we are currently using 
```

我们将收到输出:

```
# ... other nginx configurations

        location /blog {
            rewrite ^/blog(.*) /$1 break;
            proxy_pass http://192.168.43.15:8081;
        }
        location /mail {
            rewrite ^/mail(.*) /$1 break;
            proxy_pass http://192.168.43.15:8082;
        }

# ... other nginx configurations 
```

## 安装，高级用法，更多示例

我举了两个例子，只是为了体验一下什么是 conftl。更多信息和高级使用案例可以在项目资源库中找到:

[https://github . com/TTT-FIFO/conftl](https://github.com/ttt-fifo/conftl)

## 结论

我一直在寻找一个简单而强大的解决方案来模板化我的配置文件。我发现将 Python 代码嵌入模板的可能性令人愉快。我还发现有一个命令行工具来呈现模板很方便。如果其他人也喜欢这种方法并开始使用 [conftl](https://github.com/ttt-fifo/conftl) ，我会很高兴

喜欢听你的意见和评论。