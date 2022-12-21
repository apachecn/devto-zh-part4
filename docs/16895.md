# 代客 vs .宅地 macOS 上的 php 开发

> 原文：<https://dev.to/daniel_werner/valet-vs-homestead-php-development-on-macos-ccp>

在 macOS 上设置 Laravel/php 开发环境有两种流行的方式:代客和家园。两者都有利于发展，但他们的方法不同。Valet 是一个极简、快速的开发环境，但是 Homestead 提供了一个完整的流浪者虚拟机——有它所有的优点和缺点。让我们深入探讨两者的优缺点。

## 选举

#### 好

*   易于设置
*   快速——基本上只是 nginx 和 dnsmasq
*   添加新项目时不需要任何配置，只需在 web 根目录下创建一个文件夹，就可以开始了
*   它会随着操作系统自动启动
*   不需要配置/启动/ssh

#### 不好

*   你必须手动安装所有其他必要的服务，如 php，mysql，mongodb 等，但在 mac 上用自制软件很容易做到
*   如果您的项目需要不同版本的 php，在 php 版本之间切换可能会很棘手

## 宅基地

#### 好

*   它提供了完整的 Ubuntu 虚拟机
*   所有必需的服务都已预安装
*   如果需要，很容易安装其他 Linux 服务和二进制文件
*   在 macOS、Windows 和 Linux 上工作，可以为每个团队成员设置相同的开发环境，而不管操作系统如何。

#### 不好

*   它是缓慢的。有时甚至更慢。
*   每个新项目都需要 Homestead.yaml 和/etc/hosts 中的新条目
*   Npm run dev 在 vagger 中运行非常慢，所以最好在外面直接在 mac 上运行，但是所有 php 命令(例如 artisan)和 composer 都应该在 Homestead 中运行，这有时会令人困惑
*   与代客相比，设置调试器需要更多的努力(特别是对于 php cli)

这两个系统都很适合 php 开发，正如文档中所述，它们面向不同的受众。这取决于你的品味和你的团队的需要，哪一个更适合你。我的选择是贴身男仆。

帖子[Valet vs . Homestead——MAC OS 上的 php 开发](https://42coders.com/valet-vs-homestead-php-development-on-macos/)最早出现在 [42 Coders](https://42coders.com) 上。