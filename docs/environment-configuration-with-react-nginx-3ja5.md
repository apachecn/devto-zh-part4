# 使用 React & Nginx 进行环境配置

> 原文：<https://dev.to/dsample/environment-configuration-with-react-nginx-3ja5>

我已经用 React 工作了几个星期，我很快就想知道，如何用一个 Webpacked 应用程序最好地实现特定于环境的配置。

对于服务器端的 app，比如 Node.js，Ruby on Rails，甚至。Net 中，读取一个环境变量并选择正确的配置文件，甚至只是读取环境变量中的所有内容都很容易，但是当您的应用程序是一个静态的、缩小的 HTML、CSS 和 JavaScript 包时，就不那么简单了。

这里有几个选项，所以我将对每个选项进行简要描述，并描述我没有选择它们的缺点。首先，我将列出我的场景的各个部分:

*   React，使用 create-react-app 创建，然后“弹出”
*   Nginx 或潜在的其他静态主机(如 CDN)
*   码头工人
*   与单个远程后端 API 集成(目前)

* * *

### 使用 React

#### 部署时的 Webpack

记录的方法似乎是在 react 应用程序中使用`process.env.foo`，让 Webpack 的 dotenv 插件负责将值构建到精简代码中。[ [1](https://github.com/react-boilerplate/react-boilerplate/issues/1250)

从表面上看，这看起来很合理…您用环境变量配置 Docker 容器，然后在`CMD`参数中运行`npm run build`。

对我来说，这是完全不能接受的。变化的可能性太多了，因此在为每个环境“重新编译”代码的过程中会失败。我需要一种“一次构建，随处部署”的方法。

这个主题的另一个变化是在脚本中使用占位符构建应用程序，然后在部署/启动时使用 Sed 替换实际值。这一次，对我来说，搞砸的可能性太大了。

#### 主机检测

因为所有的环境都有不同的 FQDNs，所以我可以用它来切换不同的配置值。

我不相信这个有两个原因。首先，这意味着构建所有环境的配置，公开内部基础设施的存在和地址，并放弃一些开发过程。其次，如果浏览器以某种方式误解了主机名，它可能会失败。

本地测试与用户加载站点并保存到他们的机器上以便以后查看的冲突也有一点小麻烦，但是，这不是一个很大的问题，因为其他东西会损坏。

* * *

### 使用 Nginx 配置

由于 React 工具似乎没有合适的答案，我的下一个想法是使用 Nginx——web 服务器，如果用来托管构建的站点的话——来提供配置。

我看到了一个使用 [Nginx 的`sub_filter`模块](https://www.nginx.com/resources/wiki/modules/substitutions/)进行占位符替换的建议，但是我不喜欢这样，原因和上面一样。

#### 单独的配置文件

当然，下一步是使配置成为一个单独的文件，当它加载到浏览器中时，以某种方式(我将在最后谈到)将该文件带入应用程序。

服务器端代码要做的事情是将配置文件存储在公共文件目录之外，然后使用环境变量(例如`NODE_ENV`)来选择正确的配置文件，并将该文件作为通用的`/config.js`端点。那么我们能用 Nginx 做到这一点吗？

简而言之，没有。长时间的回答，是的，但你显然不应该这样做，所以他们很难。

> 开箱即用，Nginx 不支持在大多数配置块中使用环境变量。

你可以使用 Lua 或 Perl 模块，但是这需要安装那些模块，使你的 Docker 镜像更大，并且如果你必须使用运行时代码，你的站点就不再是“静态”的了。

#### 生成 nginx.conf

StackOverflow 上的几个回答建议在部署/启动时生成 nginx 配置。从 Sed 和`envsubst`，到 erb(嵌入式 Ruby)和其他脚本语言。

像 Sed 这样的字符串替换方法已经过时了，仅仅是因为在处理复杂的配置文件时可能会出现不一致。对于不同的变量需要运行多次，这也不容易。

像 erb 这样的脚本语言已经过时了，因为我必须在 Docker 镜像中安装任何一种语言才能使用它，这是不必要的膨胀(也增加了安全风险)。一些脚本语言还有一个问题，nginx.conf 中特殊字符的使用(例如`$`)可能会被解释，而不是作为文本字符串留在配置中。

#### 将应用程序的配置文件复制到位

也许最简单但容易被忽视的方法是在 Nginx 启动之前运行一个简单的 shell 脚本。该脚本读取环境变量，并将相关文件从特定于环境的目录复制到静态站点目录中的通用文件。

```
cp /config/$ENVIRONMENT.json /app/config.json 
```

Enter fullscreen mode Exit fullscreen mode

如果您传递的是实际的配置值，而不是选择一个预先存储的文件，那么还可以选择使用一个简单的 echo 命令
来生成配置文件

```
echo ‘{“foo”:”$FOO”}’ > /app/config.json 
```

Enter fullscreen mode Exit fullscreen mode

复制一个预先制作好的文件是我目前的首选方法。总是有出错的空间，但是这种方式不会涉及任何脆弱/复杂的服务器配置，并且让服务器在运行时尽可能的笨。如果你愿意，你甚至可以在 S3/类似网站上运行你的网站，而不是 Nginx。

整个设置最终看起来像这样:

```
#Dockerfile
FROM nginx:latest
COPY nginx.conf /etc/nginx/nginx.conf
COPY build /usr/share/nginx/html/
COPY config/client-side /usr/share/nginx/config/
COPY launch.sh ./
RUN chmod +x launch.sh
CMD ./launch.sh 
```

Enter fullscreen mode Exit fullscreen mode

```
#launch.sh
cp /usr/share/nginx/config/$ENVIRONMENT.json /usr/share/nginx/html/config.json
nginx -g "daemon off;" 
```

Enter fullscreen mode Exit fullscreen mode

```
#nginx.conf (snippet)
...
http {
...
    server {
    ...
        location / {
            root   /usr/share/nginx/html;
            index  index.html;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 包括站点中的 config

事情的另一面几乎是无法解释的，那就是如何让配置文件与主应用程序代码保持分离，但仍然被包含在内。

#### 代码拆分

我试图让 Webpack 将配置文件从包中去掉，并在运行时需要它，但是当它不明显时，我很快就放弃了。我敢肯定，如果我能找到的话，这里可能有一种优雅的方式。

#### 脚本标签

将`config.js`文件作为一个`<script>`标签放在 HTML 模板中是可行的，但是我必须依赖一个阻塞脚本加载来确保在应用程序代码之前加载配置。

#### 请求吧

我能想到的最简单的方法是在应用程序中创建一个模块，该模块将对相对路径`/config.json`发出 HTTP 请求，并向应用程序的其余部分提供响应。

请求它的额外好处是，如果我需要的话，我可以处理错误处理和加载微调器。

### 最终结果

在尝试了所有选项之后，最简单、也更有弹性的选项似乎是为每个环境保留单独的配置文件，并在第一次在前端使用它时请求它。