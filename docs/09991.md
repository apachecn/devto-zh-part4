# 如何在节点中导入/导出 ES6 模块

> 原文：<https://dev.to/saranshk/how-to-import-export-es6-modules-in-node-4dce>

最初发表于 2018 年 6 月 19 日[www.wisdomgeek.com](https://www.wisdomgeek.com/development/web-development/how-to-import-export-es6-modules-in-node/)。

如果您关注 javascript 生态系统已经有一段时间了，那么您应该已经知道，尽管 ES6 模块是一个东西，但是到今天为止，您仍然不能在 node 中导入/导出 ES6 模块。

# 节点 10 ESM 模块的实验标志

虽然 Node 10 增加了一个实验标志(–experimental-modules)，允许您使用这个功能，但是在使用这个功能后，您很快就会意识到它并没有那么有用。您需要在名为"的文件中编写您的导入/导出语句。mjs”而不是 js 文件。虽然这是因为 ES6 模块的实现方式是 T1，但是作为一名 javascript 开发者，我应该能够继续使用我一直在使用的东西，并且模块应该能够正常工作。

即使我跳槽并开始为我正在创建的新项目使用实验标志，在 node 中使用 ES6 模块仍然不是那么容易。一旦你引用了一个 npm 包，你会得到一个错误，说被引用的代码不是迈克尔杰克逊的脚本格式，也就是他们的文件扩展名不是以。mjs”。现在我无法控制 npm 注册表。所以这种实验性的标志用法我不可能在任何项目中使用，因为现在没有 npm 包的节点项目几乎没有用。我永远也不会致力于使这两者之间的互操作性正确，因为还有更好的方法。

# 我还有什么其他选择？

## 通天塔

另一个选择是使用巴贝尔，就像我们多年来一直做的那样。然后 Babel 将我们的 ES6 代码转换成 ES5 代码，然后我们可以使用转换后的代码运行我们的服务器。我以前写过关于使用 Babel 来导入/导出使用 Node 的 ES6 模块，但是设置 Babel 是一件痛苦的事情，也是一个额外的步骤，我宁愿尽可能避免。

## ESM

John-David Dalton 创建了一个超级易用的 npm 包，名为 esm，它允许您今天就在 node 中使用明天的 ES6 模块！这是一个零配置的解决方案，刚刚好。

### 使用 esm

在您的项目中使用 esm 不需要做太多工作。首先，您需要将它安装到您的项目中。

*   对于新项目，运行 npm init esm 或 yarn create esm，这取决于您更喜欢哪个包管理器

*   对于现有项目，yarn 添加 esm 或 npm 安装 esm。

之后，你需要做的就是在启动你的服务器时需要这个包与节点。为此，您可以在运行服务器时使用“需要命令行”选项。因此，要运行 index.js 文件的节点服务器，可以使用 node -r esm index.js 命令

如果您不想修改命令行参数，可以在单独的文件中要求 esm 模块。因此，创建一个新文件，比如 server.js，它的内容应该是:

`require = require("esm")(module/*, options*/)
module.exports = require("./index.js")`

然后您可以像平常一样使用 node 运行 server.js。

如果您遵循我上面提到的步骤，您不需要理解实现细节，并且可以很容易地在 node 中为您的 ES6 模块使用导入/导出。因此，现在就开始在您的节点项目中移除对 babel 的依赖，并享受今天编写未来模块的乐趣吧！

与其他节点开发人员分享这篇文章，帮助他们避免在节点项目中使用 commonjs 的麻烦。