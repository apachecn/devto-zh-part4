# Lefthook、Crystalball 和 git magic 带来流畅的开发体验

> 原文：<https://dev.to/evilmartians/lefthook-crystalball-and-git-magic-for-smooth-development-experience-33mc>

从这个循序渐进的教程中，您将学习如何设置 [Lefthook](https://github.com/Arkweid/lefthook) git hooks manager、 [Crystalball](https://github.com/toptal/crystalball) 测试选择库，以及如何在切换到特性分支并返回到`master`时自动安装缺失的 gems 并迁移数据库。

每个项目都应该安装一个配置项。但是 CI 构建有时会排队，您需要等待通知。无论如何，有没有一种方法可以减少“实现-测试-修复”周期的时间，节省击键和鼠标点击，但不要让坏代码进入项目存储库？是的，它很久以前就存在了，并且广为人知:git hooks。本地`.git/hooks/`目录中的普通可执行脚本。但是设置它们、更新它们是非常麻烦的。并且与您的合作者同步，因为您不能将他们提交到存储库本身。

## 设置左勾手

Lefthook 是我们自己用 Go 编写的 git hook 管理器。单一独立二进制。快速、可靠、功能丰富、语言无关。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [阿克维德](https://github.com/Arkweid) / [左勾拳](https://github.com/Arkweid/lefthook)

### 快速和强大的 Git 挂钩管理器，适用于任何类型的项目。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Build Status](img/098cd5a723d83d87bd365ca8153a3383.png)](https://camo.githubusercontent.com/ef6d3c7ccef1a40874c10f8a85b08b58639da288/68747470733a2f2f6170692e7472617669732d63692e6f72672f41726b776569642f6c656674686f6f6b2e7376673f6272616e63683d6d6173746572)

# 左钩拳

> 最快的多语言 Git hooks 经理

[![](img/4637d2bc81b39ee76f3152cb5bb20995.png "Lefthook logo")](https://raw.githubusercontent.com/Arkweid/lefthook/master/./logo_sign.svg)

快速和强大的 Git 钩子管理器，用于 Node.js、Ruby 或任何其他类型的项目。

*   **快。**是用围棋写的。可以并行运行命令。
*   **强大。**通过配置中的几行代码，你可以在`pre-push`钩子上只检查改变的文件。
*   **简单。**它是单一无依赖二进制，可以在任何环境下工作。

<g-emoji class="g-emoji" alias="book" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4d6.png">📖</g-emoji> [看了介绍帖](https://evilmartians.com/chronicles/lefthook-knock-your-teams-code-back-into-shape?utm_source=lefthook)

```
# On `git push` lefthook will run spelling and links check for all of the changed files
pre-push
  parallel: true
  commands
    spelling
      files: git diff --name-only HEAD @{push}
      glob: "*.md"
      run: npx yaspeller {files}
    check-links:
      files: git diff --name-only HEAD @{push}
      glob: "*.md"
      run: npx markdown-link-check {files}
```

[![Sponsored by Evil Martians](img/14ebd19001df8446f427d5e629d6a791.png)](https://evilmartians.com/?utm_source=lefthook)

## 使用

选择您的环境:

*   **[Node.js](https://raw.githubusercontent.com/Arkweid/lefthook/master/./docs/node.md)**
*   **[红宝石](https://raw.githubusercontent.com/Arkweid/lefthook/master/./docs/ruby.md)T3】**
*   [其他环境](https://raw.githubusercontent.com/Arkweid/lefthook/master/./docs/other.md)

然后你可以在[完整的](https://raw.githubusercontent.com/Arkweid/lefthook/master/./docs/full_guide.md)中找到所有的 Lefthook 特性…

</article>

[View on GitHub](https://github.com/Arkweid/lefthook)

1.  用[`gem`](https://github.com/Arkweid/lefthook/blob/master/docs/ruby.md)[`npm`](https://github.com/Arkweid/lefthook/blob/master/docs/node.md)或者[从源码或者你的 OS 包管理器](https://github.com/Arkweid/lefthook/blob/master/docs/other.md)安装。

2.  在配置文件`lefthook.yml`
    中定义你的钩子

    ```
    pre-push:
      parallel: true
      commands:
        rubocop:
          tags: backend
          run: bundle exec rubocop
        rspec:
          tags: rspec backend
          run: bundle exec rspec --fail-fast 
    ```

3.  并运行`lefthook install`。

瞧，从现在开始，rspec 和 rubocop 将在每个`git push`并行运行，如果它们发现任何问题，推送将被中止。

> 但是你为什么选择预推送而不是预提交呢？
> 
> 首先，有时候，在重构过程中，你想在本地做很多小的提交。大部分都过不了棉绒。如果你知道它不会通过，为什么要执行所有这些机制呢？之后，您将使用`git rebase --interactive`压缩并重新排序它们，并将干净的代码推送到回购。
> 
> 更重要的是，有些事情执行得并不快。等一下每一个`git commit`都是咩。你会失去速度。那么，为什么不将多头操作转移到更罕见的推送事件中呢？

然而，运行整个测试套件可能需要太长的时间(无论如何，我们有 CI 来做这件事)，所以我们需要一些方法来只运行我们可能会被我们的更改破坏的规范。

## 设置水晶球

[Crystalball](https://github.com/toptal/crystalball) 是 Toptal 的 Ruby 库，实现了[回归测试选择机制](https://tenderlovemaking.com/2015/02/13/predicting-test-failues.html)。它的主要目的是选择测试套件的一个最小子集，运行该子集是为了确保您的更改不会破坏任何东西。这在 Ruby 应用程序中是一个棘手的问题，尤其是在 Rails 应用程序中，因为 [Ruby on Rails 的持续自动加载机制](https://guides.rubyonrails.org/autoloading_and_reloading_constants.html)。

Crystalball 通过在测试套件运行时跟踪代码执行和文件之间的依赖关系来解决这个问题。使用这些分析数据，它可以判断哪些文件影响了哪些文件。看看 RubyKaigi 2019 演讲中关于 Crystalball 的[幻灯片。](https://speakerdeck.com/p0deje/crystalball-predicting-test-failures)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [顶层](https://github.com/toptal) / [水晶球](https://github.com/toptal/crystalball)

### RSpec 测试套件的回归测试选择库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 水晶球

Crystalball 是一个 Ruby 库，它实现了最初由 Aaron Patterson 发布的[回归测试选择机制](https://tenderlovemaking.com/2015/02/13/predicting-test-failues.html)。它的主要目的是选择测试套件的一个最小子集，该子集应该运行以确保您的更改不会破坏任何东西。

[![Build Status](img/3df9adbefcfafd7b120b58834e197ce8.png)](https://travis-ci.org/toptal/crystalball)[![Maintainability](img/a8aff037309af5b3dc833c531ca53a82.png)](https://codeclimate.com/github/toptal/crystalball/maintainability)[![Test Coverage](img/fa7656020e8affb6f5fc89d5366475e7.png)](https://codeclimate.com/github/toptal/crystalball/test_coverage)

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
group :test do
  gem 'crystalball'
end
```

然后执行:

```
$ bundle 
```

或者自己安装为:

```
$ gem install crystalball 
```

## 使用

请看我们的[官方文档](https://toptal.github.io/crystalball/)。

### 版本控制

对于我们的[版本](https://github.com/toptal/crystalball/releases)，我们使用[语义版本](https://semver.org/)。

## 发展

在签出 repo 之后，运行`bin/setup`来安装依赖项。然后，运行`rake spec`来运行测试。你也可以运行`bin/console`得到一个交互式提示，让你进行实验。

要将这个 gem 安装到您的本地机器上，运行`bundle exec rake install`。

## 贡献的

欢迎在 GitHub 上提交错误报告和请求，地址:[https://github.com/toptal/crystalball](https://github.com/toptal/crystalball)该项目旨在…

</article>

[View on GitHub](https://github.com/toptal/crystalball)

*   安装它

```
 # Gemfile
   gem "crystalball" 
```

*   为我们的预推送情况进行配置(默认情况下，crystalball 被配置为在预提交钩子中使用)

```
 # config/crystalball.yml
   ---
   map_expiration_period: 604800 # 1 week
   diff_from: origin/master 
```

*   设置您的测试套件以收集代码覆盖率信息:

```
 # spec/spec_helper.rb
   if ENV['CRYSTALBALL'] == 'true'
     require 'crystalball'
     require 'crystalball/rails'

     Crystalball::MapGenerator.start! do |config|
       config.register Crystalball::MapGenerator::CoverageStrategy.new
       config.register Crystalball::Rails::MapGenerator::I18nStrategy.new
       config.register Crystalball::MapGenerator::DescribedClassStrategy.new
     end
   end 
```

*   生成代码执行图:

```
 CRYSTALBALL=true bundle exec rspec 
```

*   在`lefthook.yml`中用 crystalball 替换 RSpec:

```
 -      run: bundle exec rspec --fail-fast
   +      run: bundle exec crystalball --fail-fast 
```

从现在开始，如果你的变化很小，每次推动都会大大加速。

但是 crystalball 需要最新的代码执行图才能正常工作。我们能自动刷新这些地图吗？当然，我们可以！

## 保持 Crystalball 最新

因此，git 的`post-checkout`钩子非常合适。我们可以通过更新 crystalball 数据逻辑来运行代码。“逻辑”意味着复杂性，因为没有单一的命令。为了应对这种情况，Lefthook 允许拥有独立的可执行脚本文件。我们可以把我们的逻辑放到`.lefthook/post-checkout/crystalball-update`文件中，使其可执行，并在 lefthook 配置中声明如下:

```
# lefthook.yml

post-checkout:
  scripts:
    crystalball-update:
      tags: rspec backend 
```

在那里，在`crystalball-update`脚本中，我们需要一点魔法。

首先，当一个开发者使用`git checkout -- path`命令拒绝工作树的一些变更时，我们不需要做任何事情。因为这不是在提交和存储库之间切换，所以存储库可能是“脏的”是的，Git CLI 有时会感觉奇怪，因为`checkout`命令用于两个不同的任务。

对于每个 git 文档，git 将总是传递给后检验挂钩三个参数:前一个头提交标识符(SHA1 总和)、当前头提交标识符和标志，它是否是分支之间的检验(`1`)或文件检验到另一个提交的状态(`0`)。Lefthook 将捕获这些参数，并小心地将它们传递给每个托管脚本。

```
#!/usr/bin/env ruby

_prev_head, _curr_head, branch_change, * = ARGV

exit if branch_change == "0" # Don't run on file checkouts 
```

接下来，我们希望按照 [Crystalball 文档](https://toptal.github.io/crystalball/)中的建议，只更新`master`分支上的 crystalball 分析数据。为此，我们需要询问 git 我们检查了哪个分支:

```
# Rails.root if we look from .lefthook/post-checkout dir
app_dir = File.expand_path("../..", __dir__)
ENV["BUNDLE_GEMFILE"] ||= File.join(app_dir, "Gemfile")
require "bundler/setup"

require "git"
exit unless Git.open(app_dir).current_branch == "master" 
```

`git` gem 是 Crystalball 的一个依赖，我们不用安装。

最后，我们需要做最重要的部分:询问 Crystalball，“您的分析数据是最新的吗？”

```
require "crystalball"
config = Crystalball::RSpec::Runner.config
prediction_builder = Crystalball::RSpec::Runner.prediction_builder

exit if File.exists?(config["execution_map_path"]) && !prediction_builder.expired_map? 
```

如果它不是新的，我们需要用特殊的环境变量集合运行整个测试套件:

```
puts "Crystalball Ruby code execution maps are out of date. Performing full test suite to update them…"

ENV["CRYSTALBALL"] = "true"
RSpec::Core::Runner.run([app_dir]) 
```

我们结束了。是吗？

## 自动化其他常规任务

但是运行规范要求我们:

a.已安装 gems 和
b .实际数据库状态。

在积极开发的应用程序中，gem 经常被更新、添加和删除，数据库模式有时会在不同的分支中一天改变几次。这是很典型的在早上拉新的 master 并获得更新的 gem 和新的数据库迁移。在这种情况下，RSpec 将会失败，Crystalball 执行路径映射将不会完整。因此，我们需要确保我们的规格总是能够提前运行。

### 在一个`git checkout`上安装丢失的宝石

这个任务非常简单，可以通过一个简单的 bash 脚本来完成。其中大部分将由检查组成，以避免在不需要时调用 bundler。Bundler 很重，因为它运行的时间很长。

这两个检查中的第一个是相同的，但只是重写为 shell:这是分支检查吗？我们真的在提交之间移动了吗？

```
#!/bin/bash

BRANCH_CHANGE=$3
[[ $BRANCH_CHANGE -eq 0 ]] && exit PREV_HEAD=$1
CURR_HEAD=$2
[ $PREV_HEAD == $CURR_HEAD ] && exit 
```

下一个更棘手:

```
# Don't run bundler if there were no changes in gems
git diff --quiet --exit-code $PREV_HEAD $CURR_HEAD -- Gemfile.lock && exit; 
```

我们在这里问，“一组必需的宝石在提交之间改变了吗？”如果`Gemfile.lock`被更改，我们需要通过调用 bundler 来检查我们是否安装了所有的 gem。

```
bundle check || bundle install 
```

同样，如果你有最新的宝石(在大多数检查中，它会如此)，只有`bundle check`会被执行。

### 自动回滚并应用数据库迁移

下一个任务更有趣。

当我们从分支 A 切换到分支 B 时，我们需要确保数据库模式实际上与我们的规范兼容。为此，我们必须回滚分支 A 中存在但不在分支 B 中的每个迁移，然后应用 B 中存在但仍未应用的每个迁移。回滚部分是必需的，因为删除或重命名列和表的迁移是不向后兼容的。

这里的问题是 git 中没有`pre-checkout`钩子，只有`post-checkout`一个。结帐后，没有更多的迁移文件只存在于我们从切换到*的分支中。如何回滚它们？*

但这是饭桶！文件就在外面。为什么不直接从 git 本身获取它们呢？

为了以编程方式做到这一点，让我们使用 gem `git`来访问我们的 git 存储库。Crystalball 已经在幕后使用了它，所以不会有新的依赖项，但是将其显式添加到`Gemfile`中是个好主意。

让我们从检查我们是否真的有任何迁移要运行(向上或向下)开始:

```
require "git"

# Rails.root if we look from .lefthook/post-checkout dir
app_dir = File.expand_path("../..", __dir__)

git = Git.open(app_dir)

# Don't run if there were no database changes between revisions
diff = git.diff(prev_head, curr_head).path("db/migrate")
exit if diff.size.zero? 
```

然后，为了能够使用迁移，我们需要加载我们的 rails 应用程序并连接到数据库:

```
require File.expand_path("config/boot", app_dir)
require File.expand_path("config/application", app_dir)
require "rake"
Rails.application.load_tasks

Rake::Task["db:load_config"].invoke 
```

然后我们可以把文件保存在某个地方:

```
# migrations added in prev_head (deleted in curr_head) that we need to rollback
rollback_migration_files = diff.select { |file| file.type == "deleted" }

if rollback_migration_files.any?
  require "tmpdir"
  MigrationFilenameRegexp = ActiveRecord::Migration::MigrationFilenameRegexp
  versions = []

  Dir.mktmpdir do |directory|
    rollback_migration_files.each do |diff_file|
      filename = File.basename(diff_file.path)
      contents = git.gblob("#{prev_head}:#{diff_file.path}").contents
      File.write(File.join(directory, filename), contents)
      version = filename.scan(MigrationFilenameRegexp).first&.first
      versions.push(version) if version
    end 
```

现在，当我们有需要回滚的迁移文件时，我们可以回滚它们:

```
begin
  old_migration_paths = ActiveRecord::Migrator.migrations_paths
  ActiveRecord::Migrator.migrations_paths.push(directory)

  versions.sort.reverse_each do |version|
    ENV["VERSION"] = version
    Rake::Task["db:migrate:down"].execute
  end
ensure
  ENV.delete("VERSION")
  ActiveRecord::Migrator.migrations_paths = old_migration_paths
end 
```

在这里，我们将我们的临时目录和另一个分支迁移添加到 ActiveRecord 的`migrations_paths`。这个设置从 Rails 5 开始就有了，但并不广为人知。现在 ActiveRecord 可以看到我们的 ghost 迁移文件，我们可以简单地为每个迁移调用`rake db:migrate:down VERSION=number`来回滚它。

之后，我们可以*迁移尚未应用的迁移:* 

```
Rake::Task["db:migrate"].invoke 
```

就是这样！

### 组合在一起

现在我们只需要按照正确的顺序调用这些脚本:安装 gems、运行迁移和运行规范(如果需要的话)。为此，我们需要按字母顺序命名文件，将它们放在`.lefthook/post-checkout`目录中，并在`lefthook.yml` :
中声明它们

```
post-checkout:
  piped: true
  scripts:
    01-bundle-checkinstall:
      tags: backend
    02-db-migrate:
      tags: backend
    03-crystalball-update:
      tags: rspec backend 
```

如果前面的命令失败，`piped`选项将中止其余的命令。例如，如果您忘记启动数据库服务器，第二步将会失败，lefthook 将会完全跳过第三步。

对运行 RSpec 不感兴趣的前端开发人员可以在他们的`lefthook.local.yml`中排除`rspec`标签，他们将只能得到一直安装的 gem 和迁移的数据库。自动地。

## 有什么问题吗？

从现在开始，您必须编写可逆迁移。查看示例应用程序，了解如何做到这一点。

## 结论

现在，我们不仅有检查来防止我们将损坏的代码推送到存储库(并且将非常快速地检查)，而且，作为副作用，我们将总是安装 gems，并且我们的数据库将处于迁移状态。你会忘记`bundle install`(除非你是更新宝石的人)。也不会再有“好吧，现在我需要先回滚 X 和 Y，然后再签回到 master。”

试用 GitHub 上发布的示例应用:[https://github.com/Envek/lefthook-crystalball-example](https://github.com/Envek/lefthook-crystalball-example)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [恩维克](https://github.com/Envek) / [左勾拳-水晶球-举例](https://github.com/Envek/lefthook-crystalball-example)

### 了解如何让 git 挂钩为您完成大多数日常任务:安装 gems、迁移数据库、运行测试和 linters。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Lefthook + Crystalball guide 的应用示例

请先阅读“ [Lefthook，Crystalball，和 git magic for smooth development experience](https://dev.to/evilmartians/lefthook-crystalball-and-git-magic-for-smooth-development-experience-33mc "Learn how to make git hooks to do most routine tasks for you: install gems, migrate the database, run tests, and linters.")”博文。

[![Sponsored by Evil Martians](img/14ebd19001df8446f427d5e629d6a791.png)](https://evilmartians.com/) 

## 装置

0.  确保您安装了 Ruby 2.6.3 和 SQLite(带有开发库)

1.  在本地派生和克隆此存储库:

    ```
    git clone https://github.com/[YOUR-NAME]/lefthook-crystalball-example
    ```

2.  用它转到目录:

    ```
    cd lefthook-crystalball-example
    ```

3.  从 Rubygems 显式安装 lefthook:

    ```
    gem install lefthook
    ```

4.  将丢失的挂钩安装到您的本地`.git/hooks/`目录:

    ```
    lefthook install
    ```

    每个开发人员只需要这样做一次。之后[左钩](https://github.com/Arkweid/lefthook "Git hooks manager")会在`lefthook.yml`改变后自动同步钩子。

5.  安装其他依赖项:

    ```
    bundle install
    yarn install --check-files
    ```

6.  准备数据库

    ```
    rails db:setup
    ```

7.  为 [Crystalball](https://github.com/toptal/crystalball "Regression Test Selection library for your RSpec test suite") 生成代码执行图:

    ```
    CRYSTALBALL=true bundle exec rspec
    ```

    这也只需要做一次。hook 会每周自动运行一次。

## 使用

1.  检查到`feature/multiaccount`分支，查看迁移是如何应用的:

    ```
    git checkout feature/multiaccount
    ```

2.  去`feature/variations`分店结账，看看新宝石如何…

</article>

[View on GitHub](https://github.com/Envek/lefthook-crystalball-example)

编码快乐！