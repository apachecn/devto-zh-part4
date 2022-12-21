# 使用 Capistrano 和 systemd 部署任何电缆

> 原文：<https://dev.to/jetrockets/deploy-anycable-with-capistrano-and-systemd-49j0>

# 注

首先，如果你仍然不知道什么是 AnyCable，那么你可能从未尝试过 Rails 中的 websockets。
“搞什么鬼？？?"你可能会说，你是对的。是的，从 Rails 5.x 开始我们就有了 ActionCable，但是你真的认为 Ruby 适合实时 web 吗？我有坏消息要告诉你。

# 简介

AnyCable 为 Ruby/Rails 中的 WebSockets 带来了性能、稳定性和可伸缩性。出于某种原因，它只缺少一样东西——部署文档。

我们主要使用 AWS 作为基础设施解决方案，并对此感到满意。Capistrano 为我们提供了运行部署脚本的强大工具，而 AWS 允许快速轻松地扩展(是的，我们不需要 Docker:)。我们每天都用定制的 Capistrano 配方管理 Unicorn、Puma、Sidekiq，没有任何问题。一切顺利，似乎是正确的。对于任何电缆，您都无法轻松使用 Capistrano，因为任何电缆都不能开箱即用。我会说，这是一个可靠的统一部署解决方案的起点。

#### AnyCable 遇上 systemd

经过一些考虑，我明白了 Unicorn 或 Sidekiq 或 Puma 只是和 Nginx 或 PG 一样的系统进程，它们应该被系统控制。你现在应该 facepalm me:)现代 Linux 发行版用什么来管理服务？当然是`systemd`。那么为什么不用它来管理任何电缆呢？

首先，让我们为`anycable`创建一个新的服务。

```
# /etc/systemd/system/your-project-anycable.service

[Unit]
Description=anycable for your-project
After=syslog.target network.target

[Service]
Type=simple
Environment=RAILS_ENV=staging
WorkingDirectory=/path-to-your-project/current/
ExecStart=/bin/bash -lc 'bundle exec anycable' #additional arguments can be added here
ExecStop=/bin/kill -TERM $MAINPID
User=www
Group=www
UMask=0002
MemoryHigh=2G
MemoryMax=3G
MemoryAccounting=true RestartSec=1
Restart=on-failure

[Install]
WantedBy=multi-user.target 
```

我很确定 3gb 对于 ruby 进程来说足够了:)添加新服务后，不要忘记重新加载`systemd`。

```
systemctl daemon-reload 
```

现在你可以控制你的任意电缆实例:

```
sudo systemctl stop|start|restart your-project-anycable 
```

并且不要忘记在启动时启用任何电缆:

```
systemctl enable your-project-anycable 
```

同样的方法也适用于任何电缆。添加新服务，重新加载`systemd`，在引导时启用它。

```
# /etc/systemd/system/your-anycable-go.service

[Unit]
Description=anycable-go for your-project
After=syslog.target network.target

[Service]
Type=simple
WorkingDirectory=/srv/www/go
ExecStart=/srv/www/go/bin/anycable-go -port 3334 --ssl_cert=/path/to/your/certificate.pem --ssl_key=/path/to/your/privkey.pem 
ExecStop=/bin/kill -TERM $MAINPID
UMask=0002
RestartSec=1
Restart=on-failure

[Install]
WantedBy=multi-user.target 
```

最后是 Capistrano，让我们创建一个新的配方并将其添加到部署场景中。

```
# config/deploy/recipes/anycable.rb

# frozen_string_literal: true

namespace :anycable do
  task :stop do
    on roles(:sidekiq) do
      execute :sudo, :systemctl, :stop, fetch(:anycable_systemctl_service_name)
    end
  end

  task :start do
    on roles(:sidekiq) do
      execute :sudo, :systemctl, :start, fetch(:anycable_systemctl_service_name)
    end
  end

  task :restart do
    on roles(:sidekiq) do
      execute :sudo, :systemctl, :restart, fetch(:anycable_systemctl_service_name)
    end
  end
end 
```

```
# config/deploy/production.rb
...
set :anycable_systemctl_service_name, 'your-project-anycable'

after 'deploy:publishing', 'anycable:restart'
... 
```

# 总之

为什么**你**要用`systemd`来管理 Ruby 服务，可以管理哪些服务？

正如我已经说过的，这里的 AnyCable 只是一个例子，我们也已经将 Unicorn 和 Sidekiq 迁移到了`systemd`。使用`systemd`,您的应用程序将获得:

*   以统一的方式启动、停止、重启它的每一部分
*   所有必需的组件都将在启动时加载
*   您可以快速了解流程的状态
*   集成不同的监控服务(例如 Nagios)，因为它们已经有了`systemd`集成。