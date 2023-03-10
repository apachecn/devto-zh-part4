# 从 linux 桌面上的命令行恢复应用程序及其位置

> 原文：<https://dev.to/johannesjo/restore-applications-and-their-positions-from-the-command-line-on-linux-desktops-4plk>

LWSM 是一个[开源](https://github.com/johannesjo/linux-window-session-manager)库/命令行工具，可以让你保存和重新加载你打开的程序和它们窗口的位置。
支持 Unity 和 Gnome 桌面，可能还支持所有其他使用 X 的合成窗口管理器(包括 XWayland)。

## 安装

需要安装 NodeJs。

```
npm install -g linux-window-session-manager 
```

对于旧发行版，您可能还需要通过`sudo apt-get install mlocate`安装`locate`命令。

## 更新

```
npm update -g linux-window-session-manager
# (optional) delete config to update it to the newest version
rm ~/.lwsm/config.json 
```

## 命令用法

### 保存会话:

```
# save the current session to ~/.lwsm/sessionData/DEFAULT.json
lwsm save

# save the current session to ~/.lwsm/sessionData/my-session.json
lwsm save my-session 
```

请注意，会话是为您当前运行的特定显示组合存储的。如果您想在插入外接显示器时存储不同的布局，这是可行的。只需在同一个会话中再次运行 save 命令。

### 恢复会话:

```
# restore the session from ~/.lwsm/sessionData/DEFAULT.json
lwsm restore

# restore the session from ~/.lwsm/sessionData/my-session.json
lwsm restore my-session   

# gracefully close all running apps before starting the session
lwsm restore --closeAllOpenWindows 
```

### 列出已保存的会话:

```
lwsm list 
```

### 重命名已保存的会话:

```
lwsm rename oldName newName 
```

### 调整配置:

您可以手动编辑`~/.lwsm/config.json`中的配置文件和`~/.lwsm/sessionData/[sessionName].json`中的会话文件。

### 命令行完成:

```
# Automatically install command-line completion
lwsm --setupCompletion
# Generate command-line completion code for bash and zsh shells for manual installation
lwsm --completion
# Generate command-line completion code for fish shell for manual installation
lwsm --completion-fish 
```

命令行完成是由[煎蛋](https://github.com/f/omelette)实现的，所以你可以参考它的自述文件来检查自动安装时将添加完成代码的文件。
自动安装后重启你的 shell 来应用修改。

## 配套工具

如果你喜欢这些东西，还有一个指示器小程序形式的[配套工具，一个](https://github.com/johannesjo/linux-window-session-manger-indicator) [gnome-shell-extension](https://github.com/johannesjo/gnome-shell-extension-window-session-manager) 和一个 [Ulauncher extension](https://github.com/kpost/ulauncher-lwsm) ，但是这个包也可以单独使用。