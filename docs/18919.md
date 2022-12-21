# 享受 Sparrow6 框架带来的乐趣

> 原文：<https://dev.to/melezhik/having-fun-with-sparrow6-framework-2fhm>

> 麻雀胶全部！

Sparrow6 是用 [Perl6](https://perl6.org) 编写的自动化系统，但是可以用许多语言扩展。

Sparrow6 很有趣，因为它旨在完成工作，而不是暗示严格的工作流程或某种范式。最酷的是，你可以把用多种语言编写的代码粘合到用 *Perl6* 编写的高级场景中。

Perl6 赋予你现代和富有表现力的语言，而支持多语言的插件系统允许你用最适合解决任务的语言编写一些代码。

下面是一个结合 Bash/Perl5 和 Perl6 语言的例子，
来自我的实际工作任务。

# 生成 Ado 管道代码的任务

我最近的一个任务是生成 Ado 管道，其中有很多来自 JSON 文件的变量，典型的代码只包含对这些变量的引用:

```
 - environment: "$(environment)"
- region: "$(region)"
- cluster_name: "$(cluster_name)"
- workernodes_amount: "$(workarounds_amount)"
# and so on 
```

那些变量是在 JSON 文件
的其他地方定义的

```
{  "variables":  {  "environment":  {"value":"dev"},  "region":  {"value":"southcentralus"},  "cluster_name":  {"value":"CHANGEME!!!"},  "workernodes_amount":  {"value":"4"}  }  } 
```

无论何时更新这个文件，我们都需要确保相应的 yaml 管道代码也得到更新。

这里的*额外的*问题是我们不能将 JSON 文件解析成相关的对象，然后遍历变量，因为在那种情况下，我们将失去 JSON 文件中变量的原始顺序(保持顺序只是管道代码本身的一个*外部*需求)，所以我们必须使用 Perl6 regexps 模式，让我在下一节展示如何做到这一点

# 解

首先，让我们定义解决方案所需的所有步骤:

*   解析 JSON 文件并从中提取变量部分
*   为 yaml 管道源代码定义模板
*   使用模板和提取的变量生成 yaml 管道

## 解析 JSON 文件，提取变量

我们[为此创建](https://github.com/melezhik/Sparrow6/blob/master/documentation/development.md)一个小的 Sparrow6 任务:

```
 tree .tom/tasks/parse-json/
.tom/tasks/parse-json/
├── task.bash
└── task.check 
```

`task.bash:`

```
cat $(config file) 
```

`task.check:`

```
between: { ^^ \s+ '{' } { ^^ \s+ '}' }
  regexp: ^^ \s+ '"' (\S+) '"'
end:

code: update_state({ list => [ map {$_->[0]} @{captures()}] }); 
```

对代码的注释:

*   `task.bash`任务只是将猫文件作为`file`参数传递，这是一个 *Bash* 脚本

*   `task.check`定义代码运行*在* `task.bash`执行后，用 Sparrow6 [任务检查语言](https://github.com/melezhik/Sparrow6/blob/master/documentation/taskchecks.md)编写的代码。当需要分析/解析脚本输出时，这很方便。

*   我们用`task.check`来解析来自`task.bash`的 STDOUT(JSON 文件内容)并捕获变量名称，见下文

*   `between:` expression 将搜索范围缩小到从行首开始的空格开始的`{`和`}`符号之间的任何内容，这对应于我们的“变量”部分。

*   `regexp:`表达式捕获出现在块之间的*内的变量名称。*

*   `code:`表达式将捕获的数据保存为**任务状态**，允许任务将数据返回给调用者。写在 *Perl5* 上的代码表达式。

## 定义模板

Sparrow6 为 Perl5 [TemplateToolkit](http://www.template-toolkit.org) 提供 DSL。所以我们要用它作为模板引擎。

`.tom/templates/pipeline.tmpl:`

```
[% FOREACH i IN list -%]
- [% i %]: "\$([% i %])"
[% END -%] 
```

## 生成管道

最后，让我们编写一个高层的 Perl6 场景，将所有的部分结合在一起:

`.tom/generate-pipeline.pl6:`

```
#!perl6

my %state = Sparrow6::Task::Runner::Api.new(
  name  => "extract variables",
  root  => ".tom/tasks/parse-json",
  parameters => %(
    file => "vars.json"
  )
).task-run;

template-create 'pipeline.yml', %(
  source => ( slurp '.tom/templates/pipeline.tmpl' ),
  variables => %(
    list => %state<list><>
  ),
); 
```

对代码的注释:

*   我们将 [Sparrow6 核心](https://github.com/melezhik/Sparrow6/blob/master/documentation/dsl.md)功能- `template-create`与自定义任务- `tasks/parse-json`相结合

*   正如我所说的，Sparrow6 在将不同的部分/语言粘合在一起时非常灵活

# 输出示例

让我们通过 [Tomtit](https://github.com/melezhik/Tomtit) 任务运行器运行场景，并查看输出:

`tom generate-pipeline`

```
19:24:16 06/12/2019 [repository] index updated from file:///home/melezhik/projects/repo/api/v1/index
19:24:19 06/12/2019 [extract variables] {
19:24:19 06/12/2019 [extract variables]   "variables":
19:24:19 06/12/2019 [extract variables]   {
19:24:19 06/12/2019 [extract variables]     "environment":           {"value":"dev"},
19:24:19 06/12/2019 [extract variables]     "region":                {"value":"southcentralus"},
19:24:19 06/12/2019 [extract variables]     "cluster_name":          {"value":"CHANGEME!!!"},
19:24:19 06/12/2019 [extract variables]     "workernodes_amount":    {"value":"4"}
19:24:19 06/12/2019 [extract variables]   }
19:24:19 06/12/2019 [extract variables] }
19:24:19 06/12/2019 [extract variables]
[task check] stdout match (r) <^^ \s+ '"' (\S+) '"'> True
19:24:21 06/12/2019 [create template pipeline.yml] content generated at /home/melezhik/.sparrow6/tmp/452443/content.tmp
[task check] stdout match <content generated> True
19:24:28 06/12/2019 [create template pipeline.yml] Files /home/melezhik/.sparrow6/tmp/452443/content.tmp and pipeline.yml differ
19:24:28 06/12/2019 [create template pipeline.yml] updating target pipeline.yml ...
19:24:28 06/12/2019 [create template pipeline.yml] outthentic_message: updated ok
19:24:28 06/12/2019 [create template pipeline.yml] --- /home/melezhik/.sparrow6/tmp/452443/content.tmp  2019-06-12 19:24:21.695451100 +0000
19:24:28 06/12/2019 [create template pipeline.yml] +++ pipeline.yml 2019-06-12 19:24:28.885209600 +0000
19:24:28 06/12/2019 [create template pipeline.yml] @@ -1,4 +0,0 @@
19:24:28 06/12/2019 [create template pipeline.yml] -- environment: "$(environment)"
19:24:28 06/12/2019 [create template pipeline.yml] -- region: "$(region)"
19:24:29 06/12/2019 [create template pipeline.yml] -- cluster_name: "$(cluster_name)"
19:24:29 06/12/2019 [create template pipeline.yml] -- workernodes_amount: "$(workernodes_amount)"
19:24:29 06/12/2019 [create template pipeline.yml] target pipeline.yml updated
19:24:29 06/12/2019 [create template pipeline.yml] set target mode to 644
[task check] stdout match <target \s+ \S+ \s+ updated> True 
```

* * *

就是这样。感谢您的阅读。欢迎评论和反馈。*