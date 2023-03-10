# 在 Heroku 上设置 Rails 6 多个数据库

> 原文：<https://dev.to/prathamesh/setting-up-rails-6-multiple-databases-on-heroku-j0k>

Rails 6 就在眼前。候选版本 2 最近发布了。这篇文章是**之路到 Rails 6** 系列的一部分，它将为你准备 Rails 6。

当我们创建一个全新的 Rails 6 应用程序时，它被配置为只使用一个数据库。要开始使用多个数据库，我们需要将数据库配置从两层配置切换到三层配置。

## 两层 v/s 三层数据库配置

为了理解它的含义，让我们看一下 database.yml 中的示例配置

```
production:
  database: cats_and_dogs_production
  adapter: postgresql 
```

Enter fullscreen mode Exit fullscreen mode

这是双层配置的一个例子。这两层是*环境*和*数据库配置*。

三层配置包括一个用于指定多个数据库的中间层。看起来是这样的。

```
production:
  database_1:
    database: cats_and_dogs_production
    adapter: postgresql
  database_2:
    database: insights_production
    adapter: mysql2 
```

Enter fullscreen mode Exit fullscreen mode

当 Rails 在`database.yml`中找到这种三层配置时，它会将您的 Rails 应用程序视为多数据库应用程序。

所以要使用多个数据库，我们需要在`database.yml`中将默认的两层配置改为三层配置。

我在本地创建了一个[示例 Rails 应用程序](https://github.com/prathamesh-sonpatki/mutli-db-heroku-example/commit/838da5d929d7be28ad2742194bba340b23895bbb#diff-e31bdf70b15c8f84344c332efe06900d),它具有以下用于开发环境的数据库配置。

```
default: &default
  adapter: sqlite3
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  timeout: 5000

development:
  <<: *default
  database: db/development.sqlite3 
```

Enter fullscreen mode Exit fullscreen mode

对于多个数据库，我们将把这种两层配置更改为三层配置。

```
development:
  primary:
    <<: *default
    database: db/development.sqlite3 
```

Enter fullscreen mode Exit fullscreen mode

`primary`表示应用程序将使用的主数据库。这与我们之前看到的单个数据库应用程序的数据库配置相同。唯一的区别是，现在是三层*而不是两层*。

> Rails 以一种特殊的方式对待`primary`键。这是你的主要数据库。

现在我们可以将第二个数据库`animals`添加到`database.yml`中。

```
development:
  primary:
    <<: *default
    database: db/development.sqlite3
  animals:
    <<: *default
    database: db/animals_development.sqlite3 
```

Enter fullscreen mode Exit fullscreen mode

> 主数据库必须排在第一位，所有其他数据库必须排在第二位。尽管没有在任何地方提到这一点，但如果顺序在本地和 Heroku 上被交换，我会遇到与动物迁移有关的随机问题。

到目前为止一切顺利！

## 创建模型和迁移

下一步是创建几个模型。默认情况下，Rails 应用程序中的所有模型都继承自`ApplicationRecord`并连接到单个数据库。但是现在我们希望我们的一些模型连接到`primary`数据库，一些连接到`animals`数据库。Rails 6 提供了一种从模型连接到数据库的方法，如下所示。

```
class Lion < ApplicationRecord
  connects_to database: { writing: :animals, reading: :animals }
end 
```

Enter fullscreen mode Exit fullscreen mode

`connects_to`行简单地告诉 Rails】模型将连接到`animals`数据库进行读写。这里的单词`animals`与我们更新的`database.yml`中的`animals`键相匹配。

> 在现实世界中，您可以使用读取副本进行读取操作，以减少主数据库的负载。

理想情况下，我们应该为所有的`animals`模型创建一个基类，而不是在每个需要连接到`animals`数据库的模型中重复这个配置。

```
class AnimalBase < ApplicationRecord
  connects_to database: { writing: :animals, reading: :animals }
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的模型被配置为连接到第二个数据库。让我们实际创建一些数据来开始。我们首先需要在动物数据库中创建表。

当我们创建迁移时，默认情况下，它们是为`db/migrate`目录中的`primary`数据库创建的。如果我们想为`animals`目录创建迁移，Rails 建议将它们存储在一个单独的目录中。为此，我们需要在`database.yml`中添加一个配置选项`migrations_paths`。

```
development:
  primary:
    <<: *default
    database: db/development.sqlite3
  animals:
    adapter: sqlite3
    database: db/animals_dev.sqlite3
    migrations_paths: db/animal_migrate 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过将数据库名称传递给迁移生成器命令来创建动物数据库的迁移。

```
rails generate migration AddLions country:string age:integer --db=animals
Running via Spring preloader in process 86684
      invoke active_record
      create db/animal_migrate/20190805164716_add_lions.rb 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们已经在`database.yml`中配置的`animal_migrate`目录中创建新的迁移。

> 如果我们忘记将`migrations_paths`键添加到`database.yml`中，那么 Rails 到目前为止不会引发任何错误，但是这意味着它不会为与`animals`数据库相关的迁移创建单独的目录。

当我们运行`rails db:migrate`时，它会为我们所有的数据库运行迁移，包括`animals`。我们还可以为特定的数据库运行迁移。

```
rails db:migrate:primary
rails db:migrate:animals 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们正在本地成功地使用多个数据库。该去 Heroku 生产了。

## 在 Heroku 上部署多数据库 app

我们需要添加`pg`宝石来部署在 Heroku 上，并更新`database.yml`的`production`部分。第一步是更改主数据库的三层配置。但是 Heroku 依赖于 D [数据库 URL 环境变量](https://devcenter.heroku.com/articles/rails-database-connection-behaviorhttps://devcenter.heroku.com/articles/rails-database-connection-behavior)，而不是从`datbase.yml`通过用户名和密码连接。

> Rails 6 RC2 中有一个与多个数据库和 DATABASE_URL 相关的 [bug](https://github.com/rails/rails/issues/36736) ，这个 bug[在 6-0-stable 分支中](https://github.com/rails/rails/pull/36756)已经修复。所以[用它](https://github.com/prathamesh-sonpatki/mutli-db-heroku-example/commit/2b754000aed079aeac5dd3ac13fc873ce5eca4d7)代替 RC2 来处理 Heroku 上的多个数据库。

Heroku 设置了`DATABASE_URL`环境变量，Rails 会自动使用它，所以我们不需要在配置中指定它。

```
production:
  primary:
    adapter: postgresql 
```

Enter fullscreen mode Exit fullscreen mode

现在要使用`animals`数据库，我们需要对它进行配置。如果你只是随便玩玩，你可以为此在你的应用中添加一个 PostgreSQL 爱好插件。

> Heroku 默认为你的应用提供一个 PostgreSQL 插件。因此，您需要为使用多个数据库再提供一个。

[![](img/5525d4c5b10e9bc12707acc935e78283.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PhSNC-So--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prathamesh.tech/conteimg/2019/08/multi-db---Resources---Heroku-2019-08-05-22-45-25.png)

Heroku PostgreSQL 插件生成一个环境变量，该变量包含新数据库的配置。在我的例子中，它是`HEROKU_POSTGRESQL_OLIVE_URL`，我们将使用它来连接到`animals`数据库。

```
production:
  primary:
    adapter: postgresql
  animals:
    migrations_paths: db/animal_migrate
    adapter: postgresql
    url: <%= ENV["HEROKU_POSTGRESQL_OLIVE_URL"] %> 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！现在我们的应用程序被配置为使用 Heroku 上的多个数据库。当我们部署并运行`rails db:migrate`时，一切都将设置好，我们可以开始在 Heroku 和 Rails 6 上使用多个数据库。

这篇文章的代码可以在这里找到。在下一篇文章中，我们将学习 Rails 6 中多数据库特性所支持的高级技术。

订阅我的[简讯](https://www.prathamesh.tech/mailing-list)要上 ***路轨 6*** 。