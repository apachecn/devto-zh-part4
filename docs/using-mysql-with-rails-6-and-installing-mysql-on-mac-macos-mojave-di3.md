# 在 Rails 6 上使用 MySQL 并在 Mac 上安装 MySQL(Mac OS Mojave)

> 原文：<https://dev.to/morinoko/using-mysql-with-rails-6-and-installing-mysql-on-mac-macos-mojave-di3>

在创建了一个 Rails 应用程序后，我最近不得不将数据库从 Sqlite 切换到 MySQL。我还必须在我的 Mac 上安装 MySQL。

这篇文章讲述了我是如何做到这一点的，并修复了我在试图安装`mysql` gem 时遇到的一个奇怪的操作系统相关错误。

# 在 Mac 上安装 MySQL(带家酿)

如果使用家酿进行包管理，可以在命令行使用以下命令安装 MySQL:

```
brew install mysql 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，请务必仔细阅读家酿软件给你的说明:

```
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`mysql_secure_installation`命令设置密码。

您可以使用`brew services start mysql`命令立即启动 MySQL。

接下来，让我们设置 Rails 来使用 MySQL！

# 使用 MySQL 和全新的 Rails 应用程序

如果您还没有创建您的应用程序，您可以运行带有标志的`rails new`命令，将您的数据库配置为使用 MySQL 而不是默认的 Sqlite:

```
rails new appname -d mysql 
```

Enter fullscreen mode Exit fullscreen mode

这将设置 Gemfile 来使用`mysql2` gem，并用一些默认的 MySQL 配置创建一个`config/database.yml`。

# 在现有的 Rails 6 应用程序中切换数据库

Rails 6 添加了方便的`rails db:system:change`命令，该命令将自动创建切换数据库所需的代码，包括编辑`config/database.yml`文件和将`mysql2`宝石添加到`Gemfile`。

在 Rails 应用程序目录中使用以下命令(也可以切换到其他数据库。见【此处】([https://go rails . com/episodes/rails-6-d b-system-change-command]:](https://gorails.com/episodes/rails-6-db-system-change-command%5D:)

```
rails db:system:change --to=mysql 
```

Enter fullscreen mode Exit fullscreen mode

它会询问您是否要覆盖`config/database.yml`文件。键入`y`并按回车键，您将看到 Rails 已经更新了以下文件:

```
force  config/database.yml
 gsub  Gemfile
 gsub  Gemfile 
```

Enter fullscreen mode Exit fullscreen mode

这将把`mysql`宝石添加到宝石文件中。在安装之前，打开 gem 文件，查找以`gem mysql`开头的行，并检查它使用的是哪个版本的 gem。这可能是一个旧版本，所以你可以查看 [Ruby Gems 目录](https://rubygems.org/gems/mysql2/)中的最新版本，如果你喜欢的话也可以用那个来代替。

运行`bundle install`安装`mysql2`宝石。如果一切顺利，您需要做的就是创建数据库(跳到下面的“创建数据库”一节)。我遇到了一个错误，我将在下面讨论它。

## 为 MySQL 手动配置 Rails

要手动设置 MySQL，首先，从 Gemfile 中删除`gem sqlite3`行，并在它的位置添加`gem mysql2`。

如下配置`config/database.yml`。这些只是 Rails 提供的基本配置(为了节省空间，删除了一些注释)，但可以根据需要进行调整:

```
# config/database.yml

# MySQL. Versions 5.5.8 and up are supported.
#
# Install the MySQL driver
#   gem install mysql2
#
# Ensure the MySQL gem is defined in your Gemfile
#   gem 'mysql2'
#
# And be sure to use new-style password hashing:
#   https://dev.mysql.com/doc/refman/5.7/en/password-hashing.html
#
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock

development:
  <<: *default
  database: myapp_development

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp_production
  username: myapp
  password: <%= ENV['PACHISLO_DATABASE_PASSWORD'] %> 
```

Enter fullscreen mode Exit fullscreen mode

# 安装 mysql2 gem 时出错

在尝试安装`mysql` gem 时，我得到了一个类似这样的疯狂长错误:

```
ERROR:  Error installing mysql2:
    ERROR: Failed to build gem native extension.

    current directory: /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/mysql2-0.5.2/ext/mysql2
/Users/morinoko/.rbenv/versions/2.6.3/bin/ruby -I /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/2.6.0 -r ./siteconf20190926-66829-1k4yip5.rb extconf.rb
checking for rb_absint_size()... yes
checking for rb_absint_singlebit_p()... yes
checking for rb_wait_for_single_fd()... yes
-----
Using mysql_config at /usr/local/bin/mysql_config
-----
checking for mysql.h... yes
checking for errmsg.h... yes
checking for SSL_MODE_DISABLED in mysql.h... yes
checking for SSL_MODE_PREFERRED in mysql.h... yes
checking for SSL_MODE_REQUIRED in mysql.h... yes
checking for SSL_MODE_VERIFY_CA in mysql.h... yes
checking for SSL_MODE_VERIFY_IDENTITY in mysql.h... yes
checking for MYSQL.net.vio in mysql.h... yes
checking for MYSQL.net.pvio in mysql.h... no
checking for MYSQL_ENABLE_CLEARTEXT_PLUGIN in mysql.h... yes
checking for SERVER_QUERY_NO_GOOD_INDEX_USED in mysql.h... yes
checking for SERVER_QUERY_NO_INDEX_USED in mysql.h... yes
checking for SERVER_QUERY_WAS_SLOW in mysql.h... yes
checking for MYSQL_OPTION_MULTI_STATEMENTS_ON in mysql.h... yes
checking for MYSQL_OPTION_MULTI_STATEMENTS_OFF in mysql.h... yes
checking for my_bool in mysql.h... no
-----
Don't know how to set rpath on your system, if MySQL libraries are not in path mysql2 may not load
-----
-----
Setting libpath to /usr/local/Cellar/mysql/8.0.17_1/lib
-----
creating Makefile

current directory: /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/mysql2-0.5.2/ext/mysql2
make "DESTDIR=" clean

current directory: /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/mysql2-0.5.2/ext/mysql2
make "DESTDIR="
compiling client.c
compiling infile.c
compiling mysql2_ext.c
compiling result.c
compiling statement.c
linking shared-object mysql2/mysql2.bundle
ld: library not found for -lssl
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [mysql2.bundle] Error 1

make failed, exit code 2

Gem files will remain installed in /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/gems/mysql2-0.5.2 for inspection.
Results logged to /Users/morinoko/.rbenv/versions/2.6.3/lib/ruby/gems/2.6.0/extensions/x86_64-darwin-18/2.6.0-static/mysql2-0.5.2/gem_make.out 
```

Enter fullscreen mode Exit fullscreen mode

经过一些谷歌搜索，如果你没有安装命令行工具(或者在升级 mac OS 时被删除)，这个错误似乎有时会出现。

尝试用
安装它们

```
xcode-select --install 
```

Enter fullscreen mode Exit fullscreen mode

如果它安装了，可能已经解决了您的问题。再次运行`bundle install`应该可以了。

然而，对我来说，我收到了一个通知，告诉我命令行工具已经安装好了:

```
xcode-select: error: command line tools are already installed, use "Software Update" to install updates 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，请使用
运行安装程序

```
sudo open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target / 
```

Enter fullscreen mode Exit fullscreen mode

(我使用了第一个命令)

原来这个 headers 包并没有包含在内(缺失？)在 Xcode 的最新版本中，所以上面的命令允许你手动安装。

现在试着再次运行`bundle install`，希望它能成功(如果不能，你将不得不继续搜索)！

# 创建数据库

您将不得不从头开始创建数据库，并重新运行任何迁移(如果您已经有一些迁移的话)。

首先，因为 Rails 覆盖了`config/database.yml`文件，所以请确保打开它并进行必要的修改，比如编辑数据库名称。

当一切配置正确后，创建数据库:

```
# Create the database
rails db:create

#=> Created database 'mydatabase_development'
#=> Created database 'mydatabase_pachislo_test'

# Run migrations
rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

现在你应该可以用`rails server`启动你的应用程序了！

## 参考文献

*   [mysql2 Ruby Gems 页面](https://rubygems.org/gems/mysql2/)
*   [文章&视频](https://gorails.com/episodes/rails-6-db-system-change-command)上了 Rails 6 的新`rails db:system:change`命令
*   Ruby on Rails 指南:[配置 MySQL 或 MariaDB 数据库](https://guides.rubyonrails.org/configuring.html#configuring-a-mysql-or-mariadb-database)
*   [关于如何修复 macOS 相关问题的日本文章](https://qiita.com/gonzaemon111/items/ea181ba3ff582a124213)