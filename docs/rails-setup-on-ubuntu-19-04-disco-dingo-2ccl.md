# Ubuntu 19.04 上的 Rails 设置

> 原文：<https://dev.to/sebsanzdesant/rails-setup-on-ubuntu-19-04-disco-dingo-2ccl>

大约在去年的这个时候，我们发布了第一个版本的 [Rivet](https://rivet.es/) ，这是一个旨在寻找和激活创造性实践发展资源的市场。

我请我们的开发人员，令人惊叹的 Francis Tseng(T1)教我如何设置本地开发环境，这样我就可以 *a)* 对我们的视图模板进行复制编辑， *b)* 了解系统如何工作，有一天我可以维护产品并构建新功能(在当时)这个想法很牵强。

第一个挑战是获得基于 Unix 的操作系统。这意味着我要么必须在我的 Windows 机器上通过一个虚拟机安装一个 Linux 发行版，要么只是擦除硬盘并运行完整的 Linux。

因为我喜欢用困难的方式做事，所以我选择了后者，在安装了许多(我指的是许多)不同的 linux 发行版之后，我暂时决定用旧的 Ubuntu。

因此，今天我想分享如何在运行 Ubuntu 19.04 Disco Dingo 的 Linux 机器上设置 Ruby on Rails 本地开发环境:

## 第一步:安装最新版本的 Ruby。

根据你的项目，可能需要使用旧版本的 Ruby 语言，不用担心，你可以在使用 rbenv 完成初始设置后添加它们。

首先，我们需要确保您拥有 Ruby 和 Rails 所需的所有依赖项。

在您的终端中，运行:

```
sudo apt install curl
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update
sudo apt-get install git-core zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev nodejs yarn 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来:

使用名为 [rbenv](https://github.com/rbenv/rbenv) 的版本管理工具安装 Ruby。

**注意*你也可以使用另一个叫做 [rvm](https://rvm.io/) 的经理，但我个人对 rbenv 更有经验。*

在您的终端中，输入:

```
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.6.3
rbenv global 2.6.3
ruby -v 
```

Enter fullscreen mode Exit fullscreen mode

这里最后 3 行很重要，因为一旦安装了 rbenv，就可以安装其他版本的 ruby，并根据开发需要在它们之间切换。

以下示例将安装 ruby 2.5.1，并将其设置为您可能正在使用的特定应用程序的本地版本。确保你在应用程序的工作文件夹中。

```
rbenv install 2.5.1
rbenv local 2.5.1 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，安装 Bundler，这样你就可以随心所欲地打包 ruby gems 了。

```
gem install bundler 
```

Enter fullscreen mode Exit fullscreen mode

rbenv 用户安装 bundler 后需要在你的终端运行`rbenv rehash`。

## 步骤 2 -配置 Git

为了对你的代码进行版本控制，我们必须用你的账户信息来设置 Git(在本教程中，我们使用的是 Github 账户):

```
git config --global color.ui true
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.com"
ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com" 
```

Enter fullscreen mode Exit fullscreen mode

最后一步为您和您的机器生成了一个新的 SSH 密钥。从以下命令复制您的密钥的输出:

```
cat ~/.ssh/id_rsa.pub 
```

Enter fullscreen mode Exit fullscreen mode

并粘贴到[你的 Github 设置这里](https://github.com/settings/ssh)

然后回到你的终端看看它是否工作:

```
ssh -T git@github.com 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到:

```
Hi (username)! You've successfully authenticated, but GitHub does not provide shell access. 
```

Enter fullscreen mode Exit fullscreen mode

## 安装导轨

`gem install rails -v 6.0.0.rc1`

因为我们正在使用`rbenv`，所以运行以下命令使`rails`可执行文件可用:

`rbenv rehash`

安装好 Rails 后，您可以运行`rails -v`来检查安装的版本是否安装正确:

`rails -v`
T1】

## 设置 PostgreSQL

Rails 自带 SQLlite 开箱即用，非常实用..但是如果你想把你的应用程序部署到 Heroku 这样的服务上，你就需要使用 PostgreSQL。

要设置 PostgreSQL，首先我们需要向您的机器添加一个存储库:

```
sudo apt install postgresql-11 libpq-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要设置一个拥有在本地创建数据库权限的用户。

```
sudo -u postgres createuser username_of_ur_choice -s 
```

Enter fullscreen mode Exit fullscreen mode

您也可以(不要求)为该用户设置密码，然后:

```
sudo -u postgres psql
postgres=# \password username_of_ur_choice 
```

Enter fullscreen mode Exit fullscreen mode

## 现在，擂鼓...

让我们创建您的第一个 Rails 应用程序！

```
## Using SQLite
rails new myapp

## Using Postgres
rails new myapp -d postgresql

# Move into the application directory
cd myapp

# Note - If your Postgres is installed with a username/password, modify the
# config/database.yml file in your ruby app folder to include the username/password you defined when setting up postgresql

# Create the database
rake db:create

# Moment of truth
rails server 
```

Enter fullscreen mode Exit fullscreen mode

在 Disco Dingo 安装的任何浏览器上进入 [http://localhost:3000](http://localhost:3000) 查看本地部署的 Ruby on Rails 安装！

您应该看到:

[![Yay! You're on Rails](img/3fe8bd980fc5c59982bb66888c4cfb74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xlRaWcwC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://edgeguides.rubyonrails.oimg/getting_started/rails_welcome.png)

现在您已经启动并运行了，是时候开始构建您的令人敬畏的应用程序了。