# 通过 Capistrano 部署静态站点

> 原文：<https://dev.to/sushant12/deploy-static-site-via-capistrano-1ejp>

工作的时候，我在做一个 PTE 的项目。前端在 vuejs 中，后端在 rails api 中。像往常一样，我们用 capistrano 部署了后端。

对于前端，我的老板希望它像 capistrano 那样部署，有一个发布文件夹、共享文件夹、回滚等。他还希望只有编译文件夹在服务器上，而不是整个回购。

我研究了一些部署静态站点的工具，但是我无法理解。

因为我知道 capistrano 并在我所有的项目中使用它，所以我决定用它来部署前端。我创建了一个名为 [Static](https://github.com/Grasruts/static) 的 gem，它是 capistrano 的一个 SCM 插件。

# 怎么用？

在项目的文件夹中创建一个 gemfile。

```
# Gemfile.rb

group :development do
 gem "capistrano", "~> 3.10", require: false
 gem 'capistrano-scm-static', '~> 0.0.2', require: false
end 
```

然后运行`bundle exec cap install`。它将在您的项目中安装所有必需的文件和文件夹。

```
# Capfile

require 'capistrano/scm/static'
install_plugin Capistrano::SCM::Static

# deploy.rb
set :dist, 'path to your static template compilation folder' 
```

假设您在前端使用 reactjs，那么您的编译文件夹将是`build`，所以只需设置

`set :dist, 'build' # in case for reactjs`

如果你和 vuejs 一起工作

`set :dist, 'dist' # in case for vuejs`

要部署它，运行`cap production deploy`，或者如果您已经设置了其他环境，那么运行`cap <environemnt> deploy`