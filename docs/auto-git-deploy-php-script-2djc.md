# 自动 git 部署 php 脚本

> 原文：<https://dev.to/katzueno/auto-git-deploy-php-script-2djc>

我制作了一个示例 PHP 脚本，使用 GitHub、Bitbucket 和其他服务提供的 webhook 特性自动部署 git。

请阅读 deployments.php 内部的选项和如何设置它的评论。

这个部署脚本需要一定水平的 git 和服务器知识。

该脚本适用于单个服务器。

请检查出来，并测试驱动器，并给我发送问题或公关，以改善脚本。

# GitHub 回购

[https://github . com/katzueno/git-web hooks-Auto-Deploy-PHP-Script](https://github.com/katzueno/git-Webhooks-Auto-Deploy-PHP-Script)

# 如何设置

## 1。设置 git

准备好你的 git 回购

## 2。设置服务器

强烈建议在同一台服务器中准备两个不同的域或子域。
设置你设置 git 部署脚本的地方和你实际部署 git 的公共区域。

您必须确保 git 部署脚本受到基本 auth 和 SSL 的保护。

获取所有必要的信息，如服务器路径。

## 3。Git 克隆到服务器

### 常规方式

*   通过 SSH 登录
*   cd 到路径
*   `git clone [GIT PATH]`
    *   确保您的 git clone 与 web 服务器的用户相同。如果你使用共享主机，你可能不用担心。
    *   例如)`sudo -u (nginx|apache) git clong [GIT PATH]`
*   确保/。git 目录对 web 不公开可见。
    *   Apache:将这一行添加到。htaccess: `RedirectMatch 404 /\.git`(需要修改重写)
    *   Nginx:将这一行添加到您的 Nginx 配置中`location ~ /\.git { return 404; }`

### 将 Git 目录和工作目录分开(更安全)

这是将 git 存储库放在公开可见 www 根目录之外的一种非常安全的方法。

*   通过 SSH 登录
*   cd 到路径
*   `git clone --mirror [GIT PATH]`到目录
*   `GIT_WORK_TREE=[www_path] git checkout -f [your desired branch]`

## 3。设置你的配置&上传文件

*   在 deployement.php 内部设置所有必要的配置设置
    *   仔细阅读第 1-45 行。你不需要在`Main Section`以下换车
*   将您的`deployment.php`上传到您准备脚本的地方
    *   确保在安全区域上传。
    *   您可以重命名文件名。

## 4。设置 webhook 并进行试驾

你的网页挂钩网址会是这样的。
将其设置为 GitHub、Bitbucket、Gitlab 或任何其他支持 webhook 的 git 服务的 webhook。

```
https://[Basic Auth ID]:[Basic Auth Pass]@example.com/deployments.php?key=YourSecretKeyHere 
```

Enter fullscreen mode Exit fullscreen mode

享受剩下的自动部署。