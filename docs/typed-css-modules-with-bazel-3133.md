# 使用 Bazel 的类型化 CSS 模块

> 原文：<https://dev.to/lewish/typed-css-modules-with-bazel-3133>

如果你正在用 [Bazel](https://bazel.build) 构建 TypeScript 并使用 CSS，你可能希望它被类型化。Webpack 之类的工具可以为您生成`.d.ts`文件，但是 Webpack 插件是通过将`.d.ts`文件添加到您的源目录中来实现的。

这在 Bazel 中是行不通的，在构建步骤中，您通常不能/不应该改变源代码。

这可以通过定制的构建规则很容易地实现。

*注:以下已用 Bazel 0.26.1 和 [rules_nodejs](https://github.com/bazelbuild/rules_nodejs) 0.31.1 测试。*

以下所有代码片段都来自一个开源项目，你可以在这里看到它们的完整运作:[https://github.com/dataform-co/dataform](https://github.com/dataform-co/dataform)

## 问题

假设我们有一个名为`styles.css`的 CSS 文件，包含以下内容:

```
.someClass {
  color: #fff;
}

.someClass .someOtherClass {
  display: flex;
} 
```

我们想要生成一个类似于
的`styles.css.d.ts`

```
export const someClass: string;
export const someOtherClass: string; 
```

因此，我们可以将此作为输入提供给任何消费规则，如`ts_library`，以确保我们没有使用任何不存在的类名，并让 TS 编译器满意。

## 使用`typed-css-modules`

我们可以使用`typed-css-modules`包来做到这一点。

首先，我们想设置一个`nodejs_binary`来生成这些类型。

首先将它添加到您的根`package.json`中，假设您使用`rules_nodejs`和`npm_install/yarn_install`规则管理您的项目依赖关系，然后将以下内容添加到您的 repo 中某个地方的`BUILD`文件中(我们将它保存在根`BUILD`文件中):

```
nodejs_binary(
    name = "tcm",
    data = [
        "@npm//typed-css-modules",
    ],
    entry_point = "@npm//node_modules/typed-css-modules:lib/cli.js",
) 
```

这允许我们从 Bazel 规则中运行 TCM CLI。

## 书写巴泽尔法则

现在我们需要定义一个 Bazel 规则，它可以实际运行 TCM CLI 并生成类型文件。在存储库中的某个地方添加一个名为`css_typings.bzl`的新文件，如下所示:

```
def _impl(ctx):
    outs = []
    for f in ctx.files.srcs:
        # Only create outputs for css files.
        if f.path[-4:] != ".css":
            fail("Only .css file inputs are allowed.")

        out = ctx.actions.declare_file(f.basename.replace(".css", ".css.d.ts"), sibling = f)
        outs.append(out)
        ctx.actions.run(
            inputs = [f] + [ctx.executable._tool],
            outputs = [out],
            executable = ctx.executable._tool,
            arguments = ["-o", out.root.path, "-p", f.path, "--silent"],
            progress_message = "Generating CSS type definitions for %s" % f.path,
        )

    # Return a structure that is compatible with the deps[] of a ts_library.
    return struct(
        files = depset(outs),
        typescript = struct(
            declarations = depset(outs),
            transitive_declarations = depset(outs),
            type_blacklisted_declarations = depset(),
            es5_sources = depset(),
            es6_sources = depset(),
            transitive_es5_sources = depset(),
            transitive_es6_sources = depset(),
        ),
    )

css_typings = rule(
    implementation = _impl,
    attrs = {
        "srcs": attr.label_list(doc = "css files", allow_files = True),
        "_tool": attr.label(
            executable = True,
            cfg = "host",
            allow_files = True,
            default = Label("//:tcm"),
        ),
    },
) 
```

这是一个相当简单的 Bazel 构建规则，它使用我们之前安装的 TCM CLI 生成 CSS 类型。

*   遍历每个输入 CSS 文件
*   对 CSS 文件运行 TCM 工具以生成类型
*   返回兼容的内容作为`ts_library`规则的输入，并且可以作为依赖项添加

**您需要将`Label("//:tcm")`行替换为您在上一步中创建的`nodejs_binary`规则的名称。**

## 把所有的东西放在一起

这里有一个使用这个规则的简单示例`BUILD`文件，假设您将`css_typings.bzl`文件放在一个`tools`目录中:

```
filegroup(
    name = "css",
    srcs = glob(["**/*.css"]),
)

load("//tools:css_typings.bzl", "css_typings")

css_typings(
    name = "css_typings",
    srcs = [":css"],
)

load("@npm_bazel_typescript//:index.bzl", "ts_library")

ts_library(
    name = "components",
    srcs = glob([
        "**/*.ts",
        "**/*.tsx",
    ]),
    data = [
        ":css",
    ],
    deps = [
        ":css_typings",
        ...
    ],
) 
```

你可能仍然需要使用上面的`data`属性捆绑 CSS 文件，假设这个库将被一些下游用户捆绑到 web 上。

这就是用 Bazel Typescript 项目生成 CSS 类型所需的全部内容！

如果这对你有用，请让我知道，我会把它放入一个仓库，这样它可以被导入和使用，而不需要复制粘贴。