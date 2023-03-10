# 如何在遗留应用程序中开始使用 ruby 语法分析？

> 原文：<https://dev.to/douglaslise/how-to-start-using-ruby-syntatic-analysis-in-a-legacy-application-22j0>

正如 ruby 社区所知， [Rubocop](https://docs.rubocop.org/) 是静态代码分析最常用的工具。

它附带了许多默认规则，这对新项目非常有用，你可以遵循大多数默认规则，只需定制其中的一些。

但是如何在遗留的或者更大的项目中进行，这些项目已经写了很多文件，并且可能有很多问题？

Rubocop gem 带有一个自动格式化程序，可以自动修复我们代码中的许多问题。

此外，它可以创建一个 TODO 配置文件，逐个文件地忽略已经存在的所有问题。所以我们可以开始使用 rubocop 来处理新文件，或者现有文件中的新问题，然后一个问题一个问题地解决。

建议自动修复所有可能的问题，并忽略其他问题，在未来的步骤中进行修复。

您可以从将 gem 添加到 Gemfile 开始(或者简单地安装它，但建议将其保存在 Gemfile 中)。

```
$> bundle add rubocop 
```

有了它，你只需运行`rubocop`，所有问题都会显示出来。比如这个例子:

```
$> rubocop
...
test/test_helper.rb:5:7: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
class ActiveSupport::TestCase
      ^^^^^^^^^^^^^^^^^^^^^^^
test/test_helper.rb:6:81: C: Metrics/LineLength: Line is too long. [82/80]
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
                                                                                ^^

135 files inspected, 1659 offenses detected 
```

现在有两种策略可供选择。

## 1。一次性修复所有问题

第一种策略是一次性解决所有问题。为此，只需运行`rubocop --auto-correct`，所有*可修复的*问题都已修复，并显示为“[已纠正]”状态。

```
$> rubocop --auto-correct
...
ENV['RAILS_ENV'] ||= 'test'
^
test/application_system_test_case.rb:1:1: C: [Corrected] Style/FrozenStringLiteralComment: Missing magic comment # frozen_string_literal: true.
require "test_helper"
^

135 files inspected, 1848 offenses detected, 1485 offenses corrected 
```

在下一次运行中，只显示剩余的问题。

```
$> rubocop
...
135 files inspected, 363 offenses detected 
```

我们现在可以忽略它们都使用`--auto-gen-config`选项运行。这将创建两个文件:

*   `.rubocop_todo.yml`:此文件包含所有当前问题的忽略规则。
*   `.rubocop.yml`:主配置文件。最初只是继承/包含 TODO 文件。

```
$> rubocop --auto-gen-config
...
135 files inspected, 133 offenses detected
Created .rubocop_todo.yml. 
```

下一次运行应该不会返回问题，因为它们在`.rubocop_todo.yml` :
中都被忽略了

```
$> rubocop
...
135 files inspected, no offenses detected 
```

现在，您只需要提交所有的更改，并可选地在 CI 管道中添加一个步骤来运行 rubocop 以检查您的源文件。

## 2。逐个缔约方会议解决问题

另一个策略是使用一个脚本，在不同的 git 提交中分离每一个 cop 修复。这有助于将来识别变更以及变更发生的原因。为此，只需运行这个脚本，它将在一个单独的提交中应用并提交每个问题。

在 git 忽略的路径中创建这个脚本很重要，比如`tmp/script.rb`。创建完脚本文件后，就叫它运行`ruby tmp/script.rb`。

```
# tmp/script.rb
require "yaml"
require "rubocop"

puts("Generating config...")
system("rubocop --auto-gen-config")
system("rm .rubocop.yml") # OR git checkout .rubocop.yml if you want to start with some config

puts("Reading TODO config to get all possible issues...")
todo = YAML.load_file(".rubocop_todo.yml")
system("rm .rubocop_todo.yml")

todo.each_with_index do |(cop, _cop_settings), index|
  puts("Fixing #{cop} (#{index + 1}/#{todo.count})...")
  if eval("RuboCop::Cop::#{cop.gsub("/", "::")}").new.support_autocorrect?
    files = `rubocop --auto-correct --force-default-config --only #{cop} --format files`.split("\n")
    system("git add #{files.join(" ")}")
    system("git commit -m \"chore: Correct source files with rubocop #{cop} cop\" \
            --author \"Rubocop Auto Correct <rubocop@rubocop>\"")
  else
    puts("  Does not support auto-correct")
  end
end

puts("Generating final TODO file with _uncorrectable_ issues...")
system("rubocop --auto-gen-config")

puts("Done") 
```

根据源文件的数量和大小，可能需要几分钟的时间来运行。

Rubocop 的设计是安全的，但是一些修复可能会导致不希望的行为，所以拥有一个大的测试覆盖率是很重要的。您可以在这个脚本的每次迭代中运行您的单元测试，或者只是在过程的最后运行它们。在这种情况下，如果出现错误，您可以使用`git bisect`来查找是哪个问题导致了错误。

最后，脚本生成如下提交:

```
$> git log --oneline
fad06ac (HEAD -> rubocop) chore: Correct source files with rubocop Style/WordArray cop
22aca61 chore: Correct source files with rubocop Style/ClassCheck cop
06b12a2 chore: Correct source files with rubocop Style/BracesAroundHashParameters cop
6dcace8 chore: Correct source files with rubocop Style/BlockDelimiters cop
a504890 chore: Correct source files with rubocop Style/BlockComments cop
1fae73b chore: Correct source files with rubocop Naming/RescuedExceptionsVariableName cop
10802ca chore: Correct source files with rubocop Lint/UnusedMethodArgument cop
bd11ffc chore: Correct source files with rubocop Lint/UnusedBlockArgument cop
b8e098d chore: Correct source files with rubocop Layout/TrailingBlankLines cop
4cabbd7 chore: Correct source files with rubocop Layout/Tab cop
f29e766 chore: Correct source files with rubocop Layout/SpaceInsidePercentLiteralDelimiters cop
b451890 chore: Correct source files with rubocop Layout/SpaceInsideHashLiteralBraces cop
ee9eae6 chore: Correct source files with rubocop Layout/SpaceInsideBlockBraces cop
f6611fd chore: Correct source files with rubocop Layout/SpaceInsideArrayLiteralBrackets cop
f858545 chore: Correct source files with rubocop Layout/SpaceInLambdaLiteral cop
3aeed2e chore: Correct source files with rubocop Layout/SpaceBeforeComment cop
8b40371 chore: Correct source files with rubocop Layout/SpaceBeforeBlockBraces cop
444f7ae chore: Correct source files with rubocop Layout/SpaceAroundEqualsInParameterDefault cop
42d4e69 chore: Correct source files with rubocop Layout/SpaceAfterComma cop
1acc003 chore: Correct source files with rubocop Layout/SpaceAfterColon cop
28ed714 chore: Correct source files with rubocop Layout/MultilineOperationIndentation cop
3e08ad7 chore: Correct source files with rubocop Layout/MultilineMethodCallIndentation cop
f4611e5 chore: Correct source files with rubocop Layout/MultilineMethodCallBraceLayout cop
6cc68b6 chore: Correct source files with rubocop Layout/MultilineHashBraceLayout cop
9f66fe9 chore: Correct source files with rubocop Layout/LeadingCommentSpace cop
... 
```

无论使用哪种策略，建议解决 TODO 文件中的所有问题，并逐步将真正需要忽略的所有忽略内容移到主`.rubocop.yml`文件中。这将确保您不断改进您的代码，或者至少保持它的质量。

最后，rubocop 是一个很棒的工具，可以帮助我们保持和提高代码的质量。非常推荐在我们所有的 ruby 项目中使用它。