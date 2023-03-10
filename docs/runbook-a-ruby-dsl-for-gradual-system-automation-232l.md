# Runbook:用于渐进系统自动化的 Ruby DSL

> 原文：<https://dev.to/pblesi/runbook-a-ruby-dsl-for-gradual-system-automation-232l>

在 Braintree，我们喜欢编写工具来自动化我们的工作。我们最新的工具是 [Runbook](https://github.com/braintree/runbook) ，一个用于逐步自动化系统操作的 Ruby DSL。

我知道你在想什么:*为什么还要构建另一个工具来自动化工程师的工作？我们已经有 bash 脚本了！*

首先，任何尝试过在 bash 中编写 for 循环的人都会承认这并不直观(我不得不每隔时间就查阅*！).其次，即使在为常见的维护操作编写解决方案时，也经常需要设置、拆卸和验证步骤来确保操作成功运行。您有多少次遇到忘记执行维护脚本所需的设置或清理步骤的问题？你有多少次忘记验证一个操作是否成功了？*

我们通常可以通过良好的文档来缓解这类问题。正如我们所知，软件文档的问题是，如果维护人员忽视更新，它可能会随着时间的推移而过时。

您有多少次编写了维护操作的脚本，却发现它已经过时，并在六个月后中断？不可避免地，您需要打开编辑器并执行脚本修复，以便从失败状态中恢复过来。

Runbook 通过提供一个将操作的文档和代码紧密耦合的框架来解决这些类型的问题。它还允许您逐步自动化操作，在完全自动化和人工参与之间找到正确的平衡。

Runbook 的理念与 Dan Slimmon 的[无为脚本](https://blog.danslimmon.com/2019/07/15/do-nothing-scripting-the-key-to-gradual-automation/)和 Atul Gawande 的[清单宣言](http://atulgawande.com/book/the-checklist-manifesto/)非常一致。它旨在最大限度地减少[的辛劳](https://landing.google.com/sre/sre-book/chapters/eliminating-toil/)。

Runbook 并不打算取代更多的专用自动化解决方案，如配置管理解决方案(Puppet、Chef、Ansible、Salt)、部署解决方案(Capistrano、Kubernetes、Docker Swarm)、监控解决方案(Nagios、Datadog)或本地命令执行(Rake tasks、Make)。相反，当需要完成跨越这些领域的任务时，Runbook 最好用作粘合剂。

## 一本简单的操作手册

操作手册概述了执行操作所需的一系列步骤。

```
# restart_nginx.rb

Runbook.book "Restart Nginx" do
  description <<-DESC This is a simple runbook to restart nginx DESC

  section "Restart Nginx" do
    step "Stop Nginx"
    step "Wait for requests to drain"
    step "Start Nginx"
  end
end 
```

它可以被编译并用于生成一个降价清单，或者以交互方式执行。

```
# Restart Nginx

This is a simple runbook to restart nginx

## 1\. Restart Nginx
 1. [] Stop Nginx
 2. [] Wait for requests to drain
 3. [] Start Nginx 
```

[![An example of using Runbook as an interactive checklist](img/0b2bc2b9920b7a635f82d8fb7deb6568.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yct4f-pM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kfzjpyzqz1n0o3yw6nhe.gif)

## 添加自动化

越过这个最初的大纲，人们可以开始将自动化构建到他们的操作手册中。

```
# restart_nginx.rb

Runbook.book "Restart Nginx" do
  description <<-DESC This is a simple runbook to restart nginx and
verify it starts successfully DESC

  section "Restart Nginx" do
    server "app01.prod"
    user "root"

    step "Stop Nginx" do
      note "Stopping Nginx..."
      command "service nginx stop"
      assert %q{service nginx status | grep "not running"}
    end

    step { wait 30 }

    step "Start Nginx" do
      note "Starting Nginx..."
      command "service nginx start"
      assert %q{service nginx status | grep "is running"}
      confirm "Nginx is taking traffic?"
      notice "Make sure to report why you restarted nginx"
    end
  end
end 
```

请注意，该操作手册包括步骤`confirm "Nginx is taking traffic?"`。通过将更难自动化的脚本步骤委托给执行 runbook 的人，您可以轻松地推迟这些步骤。

## 特性

Runbook 的一些功能包括:

### SSH 整合

Runbook 使用 [SSHKit](https://github.com/capistrano/sshkit) 与 SSH 集成，为在远程服务器上执行命令、下载和上传文件以及捕获远程执行命令的输出提供支持。您可以控制执行的并行化策略，并行、串行或成组执行。

```
Runbook.book "Restart Nginx" do
  section "Restart Services" do
    servers (0..50).map { |n| "app#{n.to_s.rjust(2, "0")}.prod" }
    parallelization(strategy: :groups, limit: 5, wait: 2)

    step "Restart services" do
      command "service nginx restart" 
    end
  end
end 
```

上面的例子一次在五台服务器上跨`app{01..50}.prod`执行`service nginx restart`，每次执行之间等待 2 秒。

### 动态控制流程

我们将 Runbook 的[控制流](https://en.wikipedia.org/wiki/Control_flow)设计成动态的；您可以随时跳过步骤、跳到任何步骤(甚至是上一步)或退出。

[![A menu displaying Runbook's dynamic control flow options](img/533159cbeb23bd816d87ae611a980f5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--48BgFydA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43a8v42b4t0cry552puh.png)

Runbook 在 runbook 的每个步骤之间保存其状态，并且如果在执行 runbook 时发生错误，它可以从停止的地方重新开始。事实上，您可以在其执行过程中的任何时候恢复已停止的 runbook。

### Noop 和 auto 模式

Runbook 提供了 noop 和 auto 两种模式。Noop 模式允许您在执行 runbook 之前验证它将运行的操作。自动模式将执行你的操作手册，不需要人工干预。您添加到 runbook 中的任何提示都将使用提供的默认值，否则，如果存在没有默认值的提示，执行将会立即失败。

[![An example of Runbook running in noop mode](img/8b11abb7592fc5716c541c8e24c49b3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--US4-jy2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/41j4u0eq0kayrdusss1l.png)

### 执行生命周期挂钩

Runbook 提供了对执行前、执行前后挂钩的支持。您可以通过挂钩到 runbook 中实体和语句的执行来改变和增加您的 runbook 行为。钩子可以用来提供一组丰富的行为，例如对 runbook 或 runbook 整体步骤的执行进行计时，跟踪 runbook 的执行频率，以及在 runbook 完成时通知 Slack。

```
Runbook::Runs::SSHKit.register_hook(
  :notify_slack_of_execution_time,
  :around,
  Runbook::Entities::Book
) do |object, metadata, block|
  start = Time.now
  block.call(object, metadata)
  duration = Time.now - start
  unless metadata[:noop]
    message = "Runbook #{object.title}: took #{duration} seconds to execute!"
    notify_slack(message)
  end
end 
```

### 一流的 tmux 支持

在布伦特里，我们的日常饮食是 vim 和 [tmux](https://github.com/tmux/tmux) 。因此，Runbook 为在 tmux 中执行命令提供了一流的支持。指定 runbook 时，您可以定义一个 tmux 布局。这种灵活而直观的界面允许您按名称向窗格发送命令。

在单独的窗格中执行命令非常适合于监控、需要用户交互的命令或容易失败的命令。然后，您可以直接与该命令进行交互，在继续操作手册之前对问题进行故障诊断和解决。

```
Runbook.book "Restart Nginx" do
  layout [[
    [{name: :top_left, runbook_pane: true}, :top_right],
    :middle,
    {name: :bottom, directory: "/var/log", command: "tail -Fn 100 nginx.log"},
  ]]

  section "Setup monitoring" do
    step do
      tmux_command "watch 'service nginx status'", :top_right
      tmux_command "vim /etc/nginx/nginx.conf", :middle   
    end
  end
end 
```

Runbooks 会记住它们在执行之间的 tmux 布局。如果 runbook 意外停止，只要 tmux 窗格没有改变，它就会在恢复时连接到现有的 tmux 布局。此外，runbook 提供在 run book 完成执行时自动关闭其 tmux 窗格。

### Ruby 命令

Runbook 提供了一个`ruby_command`语句来动态定义 runbook 语句及其参数。例如，您可以点击 JSON 端点来检索服务器列表，然后在每台服务器上执行命令。因为您使用的是 Ruby，所以您可以使用它提供的所有解析和处理功能。

```
require 'json'

Runbook.book "Restart Old Services" do
  section "Restart week-old services" do  
    step do
      server "monitor01.prod"

      capture "curl localhost:9200/host_ages.json",  into: :host_ages

      ruby_command do |rb_cmd, metadata|
        one_week_ago = 1.week.ago
        old_hosts = JSON.parse(host_ages).select { |host| host["started"] < one_week_ago }
        old_host_names = old_hosts.map { |host| host["name"] }
        old_host_names.each do |name|
          command "shutdown -r now", ssh_config: {servers: [name], user: "root"}
        end
      end  
    end
  end
end 
```

### 发电机

Runbook 提供了类似于 Rails 的生成器，用于生成 runbook、runbook 扩展和以 run book 为中心的项目。您甚至可以定义自己的生成器，在生成的操作手册中包含团队特定的定制。

[![Runbook generator options](img/eec53cac8d755df1b6347a412c9cc547.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j6MEFcHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z1mo2z2tmhoao8g2tsch.png)

### 适应性

Runbook 旨在无缝集成到现有基础设施中。它可以用作 Ruby 库、命令行工具，或者创建可自我执行的 runbooks。Runbook 遵循命令行和 ssh 等通用接口。Runbooks 可以通过 cron 作业调用，并集成到 docker 容器中。

此外，Runbook 是可扩展的，因此您可以用自己的语句和功能来扩充 DSL。下面的例子在`Book` DSL 中将`section`别名为`s`。

```
module MyRunbook::Extensions
  module Aliases
    module DSL
      def s(title, &block)
        section(title, &block)
      end
    end
  end

  Runbook::Entities::Book::DSL.prepend(Aliases::DSL)
end 
```

这种灵活性允许您修改 Runbook 以满足您遇到的任何用例。

## 查看一下

在 Braintree，我们使用 Runbook 来自动化我们的应用部署预检清单、电话技术支持手册、系统维护操作、SDK 部署等。我们发现它有助于简化生产操作，减少人为错误，提高整体生活质量。

查看 Github 上的 [Runbook，了解更多关于如何使用 Runbook 来简化生产操作和提高开发人员满意度的信息！](https://github.com/braintree/runbook)

*本帖最初发表于[媒体](https://medium.com/braintree-product-technology/https-medium-com-braintree-product-technology-runbook-be6f072cfc0d)。*