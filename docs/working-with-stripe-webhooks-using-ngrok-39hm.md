# 使用 Ngrok 与 Stripe Webhooks 的 API 集成

> 原文：<https://dev.to/spiredigitalco/working-with-stripe-webhooks-using-ngrok-39hm>

声明:ngrok 是一项付费服务。这篇博文不以任何方式由 ngrok 赞助或隶属于 ngrok。

什么是条纹网钩？最近，我参与了一个使用 Stripe 进行支付的项目。由于之前没有使用过 Stripe，我设想了一个相当简单的过程来设置对 Stripe 的 API 调用，以处理我们需要的东西。事实证明，虽然这是等式的一部分，Stripe 还提供了一个强大的功能，称为 webhooks。

Webhooks 只是 Stripe 对应用程序的 API 调用的一个别出心裁的名字。因此，现在我们有了一条双向沟通的街道:不只是我们的应用程序与 Stripe 对话，Stripe 也在回应。这正是我们的用例所需要的。

我们决定使用 Stripe 的结账功能，将用户发送到 Stripe 输入他们的支付信息，然后 Stripe 将他们重定向回我们的应用程序。这让我们跳过了在我们的网站上直接集成条纹支付表单的麻烦。支付完成后，Stripe 会向我们的应用程序发送一个 webhook 事件，以便我们处理订单。一旦完成，我们的应用程序将向 Stripe 返回 200 响应代码，我们的用户将被重定向到我们应用程序的订单成功页面。

现在，这一切听起来很棒，但当我们开发时，我们的本地服务器不能通过互联网接受请求。毕竟，服务器只存在于我们的本地机器上——它不在公共互联网上。那么我们能做什么呢？我们必须找到一种方法，让我们的本地开发服务器可以通过互联网访问。这就是 ngrok 来救援的地方。

在 Docker 开发环境中使用 Ngrok
Ngrok 是一种提供公共 URL 的服务，通过它可以访问本地开发服务器。当我们在开发机器上运行 ngrok 软件时，它会创建一个隧道，将传入的请求路由到我们本地服务器的公共 URL。在我们的例子中，我们的开发环境是用 Docker 设置的，所以需要多做一点配置工作。让我们开始吧。

下载 ngrok 客户端二进制文件
转到 ngrok 网站的下载页面。我们将在一个 Linux Docker 容器中运行 ngrok，所以下载 Linux 二进制文件，不要管你的机器是什么操作系统。对于本例，我们将把与 ngrok 相关的文件放在项目中名为 ngrok 的目录中。

配置 ngrok
使用以下内容创建一个名为 ngrok.conf 的配置文件:
web _ addr:0 . 0 . 0 . 0:4040
authtoken:
log:stdout 您的 ngrok 认证令牌可以在 ngrok 仪表板的“Auth”部分下找到。web_addr 行允许我们在浏览器中打开 ngrok web inspector。日志行允许我们在运行 docker-compose logs ngrok 时查看 ngrok 日志。
注意:ngrok 说使用 ngrok authtoken 命令来设置令牌。如果我们在我们的主机上运行 ngrok，这将很好地工作。因为我们在 Docker 容器中运行 ngrok，所以我们将在配置文件中设置令牌。

用 Docker Compose 为 ngrok 配置一个新的容器
我们使用 Docker Compose 来设置我们的开发环境，所以添加一个额外的容器来运行 ngrok 是轻而易举的事情。将以下几行添加到 docker-compose.yml 中的 services sectionngrok 下:
image: nginx
volumes:

*   ./ngrok:/code/ command: /code/ngrok http : --subdomain= -config=/code/ngrok.conf links:
*   php 端口:
*   4040:4040 在上面的配置中，应该替换为运行应用服务器的 Docker Compose 服务的名称。如果您有付费帐户，可以选择- subdomain 标志，它允许您保留一个或多个子域，与 ngrok.io 结合使用，创建您的公共 URL。如果没有这个选项，ngrok 将在每次运行时为您创建一个随机的 URL。(ngrok docs)举个例子，说我的服务叫 php，app 监听端口 8000，子域是 eriks-app。这一行看起来像这样:command:/code/ngrok http PHP:8000-subdomain = eriks-app config =/code/ngrok . conf

构建并运行 ngrok 容器
使用 docker-compose up ngrok 构建并运行这个新容器。要验证 ngrok 是否正在运行，请在浏览器中转至 localhost:4040，您应该会看到 ngrok web 检查器。恭喜你！ngrok 已经启动并运行，现在对您的公共 URL 的请求将被路由到您的本地应用服务器。您可以通过在浏览器中打开公共 URL 或使用 c URL 向您的应用程序发出 API 请求来进行测试。既然我们已经运行了 ngrok 和我们的公共 URL，我们所要做的就是配置提供 webhooks(在我们的例子中是 Stripe)的服务，以使用 ngrok 公共 URL 作为应用程序 URL。

使用这种设置，我不仅能够轻松地使用 Stripe Webhooks，而且，通过简单地共享新的 docker-compose.yml 和 ngrok 相关文件，我团队中的其他开发人员能够运行 docker-compose 并快速准备好 ngrok，而不需要下载二进制文件或自己处理任何配置。然后，我们都能够使用 webhooks 继续我们的条带集成工作。