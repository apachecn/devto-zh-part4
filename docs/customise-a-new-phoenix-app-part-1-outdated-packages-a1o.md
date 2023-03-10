# 定制一个新的 Phoenix 应用程序(第 1 部分-过时的包)

> 原文：<https://dev.to/mutablestate/customise-a-new-phoenix-app-part-1-outdated-packages-a1o>

在 [Phoenix Up and Running Guide](https://hexdocs.pm/phoenix/up_and_running.html) 的结尾，它合乎逻辑地建议
`The next step is customising our application just a bit…`并带你去了解[添加页面](https://hexdocs.pm/phoenix/adding_pages.html)。我想推荐几个额外的定制步骤，以便在构建新的应用程序之前熟悉它们。

### 过期套餐

即使从最新的归档文件中生成了一个闪亮的新 Phoenix 项目，您的`hex`和`npm`依赖项也可能包含旧的或不安全的版本。安装后立即执行这些检查，并将其作为日常维护的一部分。

**查找并更新过时的十六进制依赖关系**

*   运行[mix hex . outsided](https://hexdocs.pm/hex/Mix.Tasks.Hex.Outdated.html)输出一个表格。

| 属国 | 目前的 | 最近的 | 可能更新 |
| --- | --- | --- | --- |
| ecto_sql | 3.1.5 | 3.1.5 |  |

*   如果您的`Update possible`列是空的或者只包含`Yes`，那么运行 [mix deps.update - all](https://hexdocs.pm/mix/master/Mix.Tasks.Deps.Update.html) 来更新所有依赖项并编译它们。

*   如果任何依赖项在`Update possible`列中有一个`No`，那么自动更新是不可能的。这个包很可能因为一个`major`版本的碰撞而破坏了它的 API 合同。您需要检查升级过程的软件包发行说明。

注意事项:

*   从文档中:`Upgrading a dependency often requires the projects it depends on to upgrade too.`在这种情况下，您可以[更新一个单独的依赖项，而不触及其子项](https://hexdocs.pm/mix/master/Mix.Tasks.Deps.Update.html#module-mix-deps-unlock-mix-deps-get)。

*   作为额外的预防措施，运行 [mix hex.audit](https://hexdocs.pm/hex/Mix.Tasks.Hex.Audit.html) 来显示所有被标记为失效的十六进制依赖项。

**查找并更新过时的 NPM 软件包**

*   从您的`assets`目录运行 [npm 过时的](https://docs.npmjs.com/cli/outdated.html)来输出一个表格。

| 包裹 | 目前的 | 受通缉的 | 最近的 | 位置 |
| --- | --- | --- | --- | --- |
| css 加载程序 | 2.1.1 | 2.1.1 | 3.0.0 |  |

*   为了一致性，并且在这些年被咬过几次之后，我建议直接将您的`package.json`文件中的版本号编辑到`Latest`。对于主要版本，请在升级前查看发行说明。

*   运行`npm install`在`/node_modules`中添加新的包，生成更新的`package.lock.json`，并从 [npm 审计](https://docs.npmjs.com/cli/audit.html)中输出安全报告摘要。

```
audited 14403 packages in 6.166s
found 4 vulnerabilities (2 low, 1 moderate, 1 high)
  run `npm audit fix` to fix them, or `npm audit` for details 
```

Enter fullscreen mode Exit fullscreen mode

*   如果您有任何漏洞，那么运行`npm audit`来获得每个漏洞的更多细节。我谨慎地自动化这个过程，更喜欢在`package.json`中手动升级软件包。在你的情况下，尤其是如果这是一个新项目，试着用`npm audit fix`更新包而不做重大改变，或者用`npm audit fix --force`安装重大改变。

注意:`npm outdated`的输出可能没有列出所有可能的更新。VSCode 用户的一个变通方法是将鼠标悬停在`package.json`中的每个包上，以获得关于该包最新版本的工具提示(其他编辑器可能需要扩展)。
T3![](img/07949ff062acb6f55271a114f24b67f2.png)T5】

*关注**定制新的凤凰 app(第二部分-风格)**即将推出。我将为你的新应用带来一些风格，用 [Tailwindcss](https://tailwindcss.com/) 替换默认的 css 框架[毫克](https://milligram.io/)，并分享一些避免意大利面 CSS 的技巧。*

> 在安装了新的 Phoenix 之后，你有什么定制的想法吗？