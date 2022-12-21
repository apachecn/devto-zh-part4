# 在 Elixir 中加强代码质量

> 原文：<https://dev.to/leandrocp/enforcing-code-quality-in-elixir-4on9>

强制你的仙丹代码格式良好，没有警告，希望没有错误。我说的是一个`mix alias`，它将检查代码的质量，可以在本地开发期间使用，也可以在 CI/CD 管道上使用，以强制团队(或您自己)保持一个干净的代码。但是请记住，没有魔法，您仍然有责任创建一个组织良好、高性能、没有安全问题、易于阅读的代码。

## TL；速度三角形定位法(dead reckoning)

完整的例子在文章的最后。

## 工具

### 信条和透析器

Credo 负责检查代码是否符合由社区建立的通用良好代码实践。[透析器](http://erlang.org/doc/man/dialyzer.html)是一个静态分析工具，它可以识别软件差异，如明确的类型错误、由于编程错误而失效或不可访问的代码(解释官方文档)，但我们将使用 [dialyxir](https://github.com/jeremyjh/dialyxir) ，它是用 Elixir 编写的透析器包装器，简化了透析器的使用。

### Sobelow

Sobelow 是一个面向 Phoenix 框架的安全静态分析工具。如果您没有使用 Phoenix，只需将它从本文描述的 deps、configs 和 alias 中删除即可。

### 混合格式

[混合格式](https://hexdocs.pm/mix/Mix.Tasks.Format.html)任务是[在 Elixir v1.6](https://elixir-lang.org/blog/2018/01/17/elixir-v1-6-0-released/) 中引入的，它用于自动格式化您的代码。使用它的主要好处之一是避免无聊和无休止的讨论，如“我们应该如何格式化代码？”，“线路长度应该是多少？”，等等。 [Elixir 也用它](https://github.com/elixir-lang/elixir/blob/v1.8/Makefile#L215-L216)来执行标准格式。

### 警告为错误

这不是一个工具，实际上，这是一个 [Elixir 编译属性](https://hexdocs.pm/mix/Mix.Tasks.Compile.Elixir.html)，它将当前项目中的警告视为错误，并返回一个非零退出代码，这意味着如果有任何警告，您的项目将不会编译。

### 我们继续之前先说明一下

让所有这些工具和配置都打开*可能会让*很烦。一个没有 doc 的模块、一个错误的规范甚至一个未使用的变量都会阻止你编译和发布代码。实施的级别取决于您，因此您应该根据项目的需要(以及您的压力水平)来调整工具和配置。但是做长期计划是个好主意。

## 实现

好的，让我们更改文件以实施质量检查。让我们一点一点地做，或者更好地说，一个功能一个功能地做。

### mix.exs

首先，让我们安装 dep。在 *deps 列表*中增加以下内容:

```
{:credo, "~> 1.0", only: [:dev, :test], runtime: false},
{:dialyxir, "~> 1.0.0-rc.6", only: [:dev, :test], runtime: false},
{:sobelow, "~> 0.7", only: [:dev, :test], runtime: false} 
```

让我们更改项目的配置。将此添加到*项目功能* :

```
elixirc_options: [warnings_as_errors: true],
aliases: aliases(),
dialyzer: [
  plt_file: {:no_warn, "priv/plts/dialyzer.plt"},
  ignore_warnings: ".dialyzer_ignore.exs"
] 
```

这将是什么样子:

```
defmodule YourProject.MixProject do
  use Mix.Project

  def project do
    [
      # current configs...

      elixirc_options: [warnings_as_errors: true],
      aliases: aliases(),
      dialyzer: [
        plt_file: {:no_warn, "priv/plts/dialyzer.plt"},
        ignore_warnings: ".dialyzer_ignore.exs"
      ]
    ]
  end

  defp deps do
    [
      # current deps...

      # dev, test
      {:credo, "~> 1.0", only: [:dev, :test], runtime: false},
      {:dialyxir, "~> 1.0.0-rc.6", only: [:dev, :test], runtime: false},
      {:sobelow, "~> 0.7", only: [:dev, :test], runtime: false}
    ]
  end
end 
```

我推荐阅读 [dialyxir 文档](https://hexdocs.pm/dialyxir/0.4.1/readme.html)来了解更多关于它的配置，特别是[持续集成](https://github.com/jeremyjh/dialyxir#continuous-integration)如果你想在你的 CI/CD 管道上执行它。

继续，让我们在你的 mix.exs 文件中创建一个别名函数，*如果你还没有创建这个函数*，并添加两个别名:

```
defp aliases do
  [
    # current aliases...

    quality: ["format", "credo --strict", "sobelow --verbose", "dialyzer", "test"],
    "quality.ci": [
      "test",
      "format --check-formatted",
      "credo --strict",
      "sobelow --exit",
      "dialyzer --halt-exit-status"
    ]
  ]
end 
```

我喜欢使用一个专用于本地开发的别名(quality)和一个专用于 CI/CD 的别名(quality.ci ),因为我在任务及其参数的顺序上有更多的自由。比如在本地开发上我不想检查代码是否格式化，我只是直接格式化代码；我喜欢在最后看到测试结果。想怎么适应就怎么适应。

### [T1。透析器 _ignore.exs](#dialyzerignoreexs)

创建文件*。项目根目录
下的透析器 _ignore.exs*

```
[
  {":0:unknown_function Function ExUnit.Callbacks. __merge__ /3 does not exist."},
  {":0:unknown_function Function ExUnit.CaseTemplate. __proxy__ /2 does not exist."}
] 
```

还记得我说过那些工具可能很烦人吗？特别是透析器。不要误会，透析器是惊人的，我相信你应该使用它，但有时你需要忽略一两个警告，这是你可以这样做的文件。

关于这个文件的内容:我们将在测试环境中运行我们的别名(MIX_ENV=test ),透析器声明这些函数是错误的，但是这没什么大不了的，让我们忽略它。

### 信条

您不需要为了让 credo 工作而改变任何东西，但是默认的规则可能不适合您的项目。你可以使用一个 [.credo.exs](https://github.com/rrrene/credo#configuration-via-credoexs) 文件或者使用特殊注释的[来改变它。](https://github.com/rrrene/credo#inline-configuration-via-config-comments)

### 。吉蒂尔

添加到。gitignore:

```
# Dialyzer
/priv/plts/*.plt
/priv/plts/*.plt.hash

# Sobelow
.sobelow 
```

这些文件是环境相关的。

## 正在执行

终于，你准备好了！😅

首先，让我们创建透析器将存储 plt 文件的目录:

`mkdir -p priv/plts`

然后在您的终端中执行:

`MIX_ENV=test mix quality`

休息一会儿，喝杯咖啡，第一次执行将需要一些时间来建立所有透析器人工制品，但这些文件将被缓存，不要担心。

并将 CI/CD 管道更改为执行:

`MIX_ENV=test mix quality.ci`

每当该命令在您的代码中发现问题时，CI/CD 管道将停止并返回一个失败。

## 完整的示例文件

*mix.exs*

```
defmodule YourProject.MixProject do
  use Mix.Project

  def project do
    [
      app: :your_project,
      version: "0.1.0",
      elixir: "~> 1.6",
      elixirc_paths: elixirc_paths(Mix.env()),
      elixirc_options: [warnings_as_errors: true],
      compilers: [:phoenix, :gettext] ++ Mix.compilers(),
      start_permanent: Mix.env() == :prod,
      aliases: aliases(),
      deps: deps(),
      dialyzer: [
        plt_file: {:no_warn, "priv/plts/dialyzer.plt"},
        ignore_warnings: ".dialyzer_ignore.exs"
      ]
    ]
  end

  # Configuration for the OTP application.
  #
  # Type `mix help compile.app` for more information.
  def application do
    [
      mod: {YourProject.Application, []},
      extra_applications: [:logger, :runtime_tools]
    ]
  end

  # Specifies which paths to compile per environment.
  defp elixirc_paths(:test), do: ["lib", "test/support"]
  defp elixirc_paths(_), do: ["lib"]

  # Specifies your project dependencies.
  #
  # Type `mix help deps` for examples and options.
  defp deps do
    [
      {:phoenix, "~> 1.4.3"},
      {:phoenix_pubsub, "~> 1.1"},
      {:phoenix_ecto, "~> 4.0"},
      {:ecto_sql, "~> 3.0"},
      {:postgrex, ">= 0.0.0"},
      {:phoenix_html, "~> 2.11"},
      {:phoenix_live_reload, "~> 1.2", only: :dev},
      {:gettext, "~> 0.11"},

      # dev, test
      {:credo, "~> 1.0", only: [:dev, :test], runtime: false},
      {:dialyxir, "~> 1.0.0-rc.6", only: [:dev, :test], runtime: false},
      {:sobelow, "~> 0.7", only: [:dev, :test], runtime: false}
    ]
  end

  # Aliases are shortcuts or tasks specific to the current project.
  # For example, to create, migrate and run the seeds file at once:
  #
  # $ mix ecto.setup
  #
  # See the documentation for `Mix` for more info on aliases.
  defp aliases do
    [
      quality: ["format", "credo --strict", "sobelow --verbose", "dialyzer", "test"],
      "quality.ci": [
        "test",
        "format --check-formatted",
        "credo --strict",
        "sobelow --exit",
        "dialyzer --halt-exit-status"
      ],
      "ecto.setup": ["ecto.create", "ecto.migrate", "run priv/repo/seeds.exs"],
      "ecto.reset": ["ecto.drop", "ecto.setup"],
      test: ["ecto.create --quiet", "ecto.migrate", "test"]
    ]
  end
end 
```

*。透析器 _ignore.exs*

```
[
  {":0:unknown_function Function ExUnit.Callbacks. __merge__ /3 does not exist."},
  {":0:unknown_function Function ExUnit.CaseTemplate. __proxy__ /2 does not exist."}
  ] 
```

*。gitignore*

```
# The directory Mix will write compiled artifacts to.
/_build/

# If you run "mix test --cover", coverage assets end up here.
/cover/

# The directory Mix downloads your dependencies sources to.
/deps/

# Where 3rd-party dependencies like ExDoc output generated docs.
/doc/

# Ignore .fetch files in case you like to edit your project deps locally.
/.fetch

# If the VM crashes, it generates a dump, let's ignore it too.
erl_crash.dump

# Also ignore archive artifacts (built via "mix archive.build").
*.ez

# Ignore package tarball (built via "mix hex.build").
your_project-*.tar

# If NPM crashes, it generates a log, let's ignore it too.
npm-debug.log

# The directory NPM downloads your dependencies sources to.
/assets/node_modules/

# Since we are building assets from assets/,
# we ignore priv/static. You may want to comment
# this depending on your deployment strategy.
/priv/static/

# Files matching config/*.secret.exs pattern contain sensitive
# data and you should not commit them into version control.
#
# Alternatively, you may comment the line below and commit the
# secrets files as long as you replace their contents by environment
# variables.
/config/*.secret.exs

.elixir_ls

# Dialyzer
/priv/plts/*.plt
/priv/plts/*.plt.hash%

# Sobelow
.sobelow 
```

## 参考文献

*   [采用灵丹妙药——从概念到生产](https://pragprog.com/book/tvmelixir/adopting-elixir)

*   [https://blog.dnsimple.com/2018/10/elixir-dialyzer-in-ci/](https://blog.dnsimple.com/2018/10/elixir-dialyzer-in-ci/)