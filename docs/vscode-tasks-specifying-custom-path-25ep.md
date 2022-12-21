# VSCode 任务-指定自定义路径

> 原文：<https://dev.to/nirmaljpatel/vscode-tasks-specifying-custom-path-25ep>

macOS Catalina 选择 zsh 作为缺省 shell 来代替 bash。
这一转变让我的定制 VSCode 任务遇到了一个奇怪的问题，任务抛出了一个`zsh:1: command not found`错误。

我将 macOS 用户的默认登录 shell 设置为 zsh，并将 VSCode 设置为使用 zsh 作为集成终端的默认 shell。

尝试将`export PATH=$PATH:<mypath>`添加到我的`~/.zshrc`中，但也没用。

最终，我在 VSCode 任务选项
中将路径指定为自定义 env 变量

```
"tasks": [
    {
        "label": "Project Name",
        "type": "shell",
        "command": "appc ti project id -o text --no-banner",
        "options": {
            "env": {
                "PATH": "<mypath>:${env:PATH}"
            }
        }
    }
] 
```

Enter fullscreen mode Exit fullscreen mode