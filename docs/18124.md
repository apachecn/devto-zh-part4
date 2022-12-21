# 转到 vs 代码中的项目设置

> 原文：<https://dev.to/czyang/go-porject-settings-in-vs-code-3nl4>

# 执行 VS 代码中的项目设置

目前，VS 代码是开发 Go 项目的一个很好的工具，不需要太多的配置工作。对于大多数简单的 Go 项目，你只需要安装 Go 插件就可以把你的 VS 代码转换成 Go IDE。我已经注意到我在 VS 代码中设置 Go 时遇到的一些问题。

## 为项目使用 [Go 1.11 模块](https://github.com/golang/go/wiki/Modules)

设置起来有点棘手。因为我们需要设置环境虚拟
`GO111MODULE=on`和构建标志:`-mod=vendor`

```
// .vscode/launch.json for Go debug
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "debug",
            "program": "${workspaceFolder}",
            "env": {"GO111MODULE": "on"},
            "buildFlags": "-mod=vendor",
            "args": []
        }
    ]
} 
```

然后，您可以通过单击 vscode 中的 debug 按钮来运行 Go 项目。
您也可以更改`.vscode/settings.json`并添加`go.buildFlags`和`go.toolsEnvVars`。

对于`go test`，可以设置`go.testEnvVars`和`go.testFlags`。但是对于从原始项目派生出来的项目的测试，它不起作用。对于这种情况，请查看下面的文章。

## 禁用“保存时格式化”

VS 代码在保存默认值时启用了 go 格式。在大多数情况下，不建议在保存时禁用 go 格式。但是如果你在遗留项目或一些项目上工作，不要一开始就把 linter 和 format 放到工作流中。您可能希望禁用此功能。

看来 vscode-go 或 vscode 与当前版本
*`Version 1.35.1 (1.35.1)`有 bug go 插件版本:`0.11.0`*
VS code 无法禁用“格式保存”。这里有一个替代方案:

打开快捷方式编辑器，将`saveWithoutFormatting`改为
`cmd+s`，将`save`改为`cmd+k s` :

```
{  "key":  "cmd+s",  "command":  "workbench.action.files.saveWithoutFormatting"  } 
```

```
{  "key":  "cmd+k s",  "command":  "workbench.action.files.save"  } 
```

我们应该使用下面的方法来达到这个效果。但是它不适用于当前的 vscode。

解决的办法是将此属性添加到`.vscode/settings.json`
*中，对于`Version 1.35.1 (1.35.1)` go 插件版本:`0.11.0`*

```
 "[go]":  {  "editor.formatOnSave":  false  } 
```