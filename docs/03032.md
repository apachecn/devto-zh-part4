# 与机械战警交朋友

> 原文：<https://dev.to/prathamesh/making-friends-with-rubocop-2011>

在 Memory.ai，我们开始大量使用 Rubocop。这是一个关于我们如何将 Rubocop 整合到现有应用中的故事。

> 这不是一篇介绍 Rubocop 的文章。在深入我们的体验报告之前，先看看 Rubocop 是什么。

我们从`rubocop`、`rubocop-performance`、`rubocop-rails`和`rubocop-rspec`宝石开始。我们默认启用所有警察，这是我们在`rubocop.yml`的初始配置

```
require:
  - rubocop-rspec
  - rubocop-rails
  - rubocop-performance

AllCops:
  EnabledByDefault: true
  TargetRubyVersion: 2.6.3
  Exclude:
    - 'app/views/**/*'
    - 'db/**/*'
    - 'bin/**/*'
    - 'csv/**/*'
    - 'slate/**/*'
    - 'vendor/bundle/**/*'
    - 'node_modules/**/*' 
```

Enter fullscreen mode Exit fullscreen mode

如果你在一个现有的项目中启用了 Rubocop 的所有警察，你将会收到大量的警告和违规。添加上述配置后，当我们运行`rubocop`时，这一点非常明显。

### 工作进行中

对于现有的项目，有一个更好的集成 Rubocop 的方法。Rubocop 提供了一个文件`.rubocop_todo.yml`,记录了我们代码库中的所有违规行为。当我们接触特定的代码时，我们可以一个接一个地修正这些错误。所以我们不必一开始就解决所有问题。

要开始使用`.rubocop_todo.yml`，请执行以下步骤。

```
bundle exec rubocop --auto-gen-config
Added inheritance from `.rubocop_todo.yml` in `.rubocop.yml`.
Phase 1 of 2: run Metrics/LineLength cop
Inspecting 38 files
CC....C..C..C...C..CCC..CC....CC..C..C

38 files inspected, 40 offenses detected
Created .rubocop_todo.yml.
Phase 2 of 2: run all cops
Inspecting 38 files
CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC

38 files inspected, 104 offenses detected
Created .rubocop_todo.yml. 
```

Enter fullscreen mode Exit fullscreen mode

您将会看到类似的输出，但是根据您的代码库的状态，违例的数量可能会非常大或非常小:)

这个命令做了三件事。

*   从`.rubocop_todo.yml`继承了`.rubocop.yml`(我们稍后将回到它)
*   运行我们所有的代码
*   生成了一个新文件`.rubocop_todo.yml`

让我们看看`.rubocop_todo.yml`的[内容](https://gist.github.com/prathamesh-sonpatki/147e4299572235762bac6abdae555574)。它记录了我们法典中所有的违法行为。为了更好的理解，让我们看一个警察的例子。

```
# Offense count: 1
# Cop supports --auto-correct.
# Configuration parameters: TreatCommentsAsGroupSeparators, Include.
# Include: **/*.gemfile,** /Gemfile, **/gems.rb
Bundler/OrderedGems:
  Exclude:
    - 'Gemfile' 
```

Enter fullscreen mode Exit fullscreen mode

这一小段代码告诉我们，我们的代码库有一个对`Bundler/OrderedGems` cop 的攻击，以及我们如何修复它。但更重要的是，它标记了存在这种攻击的文件，因为**从运行 Rubocop 的文件列表中排除了**。

这是什么意思？现在让我们试着在整个项目上运行`rubocop`。

```
▶ bundle exec rubocop
Inspecting 38 files
......................................

38 files inspected, no offenses detected 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。我们的代码是干净的，它通过所有的警察，党的时间！

不完全是。这就是评论*从`.rubocop_todo.yml`* 继承`.rubocop.yml`的地方。

```
▶ cat .rubocop.yml
inherit_from: .rubocop_todo.yml 
```

Enter fullscreen mode Exit fullscreen mode

`.rubocop_todo.yml`记录我们代码库的所有违规行为，并从将运行`rubocop`的列表中排除那些文件。

`rubocop.yml`继承自`.rubocop_todo.yml`，所以它也将那些文件从运行`rubocop`的列表中排除。

当我们运行`bundle exec rubocop`时，它从`.rubocop.yml`获取配置，然后从`.rubocop_todo.yml`获取配置，后者已经排除了所有有问题的文件。所有这些导致`bundle exec rubocop`命令的绿色输出。

所以我们仍然有 Rubocop 不喜欢的攻击性代码，但是我们有一个策略来逐步修复它，而不是一次全部修复。

> 专业提示:当你将 Rubocop 集成到一个现有的项目中时，一定要生成`.rubocop_todo.yml`。

### Git 工作流程

下一步自然是对`.rubocop_todo.yml`文件采取行动，在我们接触现有攻击性代码时修复攻击，并确保我们编写的新代码遵循规则。

我们决定添加一个 git 挂钩，它将在阶段性变更时运行 Rubocop。Rubocop 提供了一个选项`--safe-autocorrect`,它根据某个 cop 是否被认为可以安全地自动更正来自动更正某些代码。我们在 git 提交钩子中利用了这一点，这样开发人员就不必担心手动修复所有的问题。当机器可以做到的时候，为什么不可以呢？

我们使用了`husky`和`lint-staged` npm 包来实现这一点。

```
npm install --save-dev lint-staged husky 
```

Enter fullscreen mode Exit fullscreen mode

在`package.json`中增加以下内容

```
{
  "scripts": {
    "precommit": "lint-staged"
  },
  "lint-staged": {
    {app,spec}/**/*.rb": [
      "bundle exec rubocop --safe-autocorrect",
      "git add"
    ]
  },
  "devDependencies": {
    "husky": "^0.13.4",
    "lint-staged": "^3.6.0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种配置确保了每当开发者试图提交代码时，Rubocop 和支持[自动纠正机制](http://docs.rubocop.org/en/stable/auto_correct/)的 cops 认为安全的代码中的错误已经被修复。在这之后，我们的开发者不需要做任何额外的事情，除了手动修复 Rubocop 认为不安全的错误。

### 进行中的工作仍在继续

一旦自动更正的 git 挂钩起作用，我们的犯罪清单开始下降。我们不得不重新生成中间的`.rubocop_todo.yml`来获得准确的犯罪列表。我们决定不在 Git hook 中添加`.rubocop_todo.yml`的再生，因为在我们的例子中它很慢。

> `.rubocop_todo.yml`可以从生成它的同一个命令重新生成。

### 幸福还是痛苦？

在所有这些之后，我们期望攻击性的代码减少，代码质量一天天提高，没有任何错误。但是我们面临的挑战很少。

在这个过程中，自动更正挂钩对我们的代码做了一些意想不到的更改。

我们有一段代码，它有一个方法`update_attributes`。`rubocop-rails` gem 有一个 cop `ActiveRecord/Aliases`用`update`把召唤改成`update_attributes`。尽管这种改变应该只发生在活动记录模型上，但是 cop 并不检查该方法是否在活动记录模型上被调用。所以在我们的例子中，它将对 custom `update_attributes`的调用更改为`update`，但是没有更改方法定义。它保持了`update_attributes`。这导致了错误。由于我们已经启用了`safe-autocorrect` Git hook，开发人员只有在 CI 中构建失败时才知道这一点。

我们还面临着其他一些警察的问题，比如`Rails/SaveBang`和`Style/StringHashKeys`，他们修改了本不应该修改的代码。

最终我们决定去掉`safe autocorrect`钩子，依靠人工修复攻击性代码。

### 投稿回来

当我们面对一些对`safe autocorrect`选项不安全的 cop 相关问题时，我们试图通过向 Rubocop 提交补丁来修复它们。

[https://github.com/rubocop-hq/rubocop-rails/pull/101](https://github.com/rubocop-hq/rubocop-rails/pull/101)

[https://github.com/rubocop-hq/rubocop-rails/pull/98](https://github.com/rubocop-hq/rubocop-rails/pull/98)

[https://github.com/rubocop-hq/rubocop/pull/7312](https://github.com/rubocop-hq/rubocop/pull/7312)

我会鼓励每个人都这样做，因为这让 Rubocop 对每个人都更好。

最后，我将列出我们从这次收养中学到的东西。

*   逐步采用是关键。
*   自动更正可能很棘手，取决于您的测试覆盖范围和许多其他因素，所以如果没有必要就不要使用它。
*   禁用你的团队不同意的警察。我们残疾警察如`RSpec/AnyInstance`、`RSpec/ExpectInHook`、`RSpec/AlignRightLetBrace`
*   将您的发现和代码修复回馈给社区，让 Rubocop 这样的关键工具变得对每个人都更好。

想知道我们在 Memory.ai 是怎么做 Ruby 和 Rails 的，在这里订阅我的时事通讯[。](https://prathamesh.tech/mailing-list)