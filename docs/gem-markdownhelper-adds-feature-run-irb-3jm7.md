# Gem markdown_helper 增加特性:运行 Irb

> 原文：<https://dev.to/burdettelamar/gem-markdownhelper-adds-feature-run-irb-3jm7>

我给`markdown_helper`添加了一个特性:在 Ruby 交互式 shell 中执行 markdown 嵌入的 Ruby 片段的能力，`irb`。

*   [用例](https://github.com/BurdetteLamar/markdown_helper/blob/master/markdown/use_cases/run_irb/run_irb/use_case.md#run-irb)
*   [项目文件](https://github.com/BurdetteLamar/markdown_helper#markdown-helper)
*   [宝石](https://rubygems.org/gems/markdown_helper)

提醒:`markdown_helper`可通过命令行获得，因此无需编写 Ruby 代码即可使用:

*   文件包含:`markdown_helper include [options] includer.md included.md`
*   运行 irb: `markdown_helper run_irb [options] template.md markdown.md`

和以前一样，也可以通过它的 Ruby API 获得。