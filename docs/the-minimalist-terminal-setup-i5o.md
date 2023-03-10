# 极简终端设置

> 原文：<https://dev.to/ok_ape/the-minimalist-terminal-setup-i5o>

**TL；DR** Alacritty + Tmux + Vim

我发现开发环境的优点在于它的简单性。没有外部插件，一个 IDE 或设置能做多少才是它们真正的效用。

我喜欢极简的设置，我不喜欢用外部插件和主题来膨胀软件，只是为了让它看起来漂亮。保存在`/home`目录中某处的配置文件比 GUI 或向导更优雅。如果你和我一样，你也会喜欢这种极简的终端设置。

[![terminal screenshot](img/9b9ffc52cce971ec4b00ccfa44388b3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---mVacGoD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tG4iJHH.png)

# 阿拉克里蒂

Alacritty 只不过是一个终端模拟器。它没有交互式终端模拟器中常见的功能。列出所有它**没有**的东西。

*   多个选项卡
*   首选项向导
*   包管理器(任何类型)
*   内置主题
*   没有任何种类的 GUI 设置窗口。

事情清单**是**。

*   快的
*   简单的
*   自主的
*   可扩张的
*   < 2.0 MB 大小

[Alacritty 比任何交互式终端应用都要快](https://github.com/jwilm/alacritty#faq)。它不需要额外的设置或依赖来运行。它还可以应用现有配置文件中的设置，即`.bashrc`、`.profile`等。

#### 配置

*   **配色**:打开位于`~/.config/alacritty/alacritty.yml`的`alacritty.yml`文件，移动到`colors:`变量。根据您的喜好更改各自的颜色值。你也可以访问这个 [wiki](https://github.com/jwilm/alacritty/wiki/Color-schemes) 复制一些冷色值。

```
# Colors (Ayu Dark)
colors:
  # Default colors
  primary:
    background: '0x0A0E14'
    foreground: '0xB3B1AD'

  # Normal colors
  normal:
    black: '0x01060E'
    red: '0xEA6C73'
    green: '0x91B362'
    yellow: '0xF9AF4F'
    blue: '0x53BDFA'
    magenta: '0xFAE994'
    cyan: '0x90E1C6'
    white: '0xC7C7C7'

  # Bright colors
  bright:
    black: '0x686868'
    red: '0xF07178'
    green: '0xC2D94C'
    yellow: '0xFFB454'
    blue: '0x59C2FF'
    magenta: '0xFFEE99'
    cyan: '0x95E6CB'
    white: '0xFFFFFF' 
```

*   **字体**:在同一个配置文件中，移动到`font:`变量，在`normal:`变量下，取消`family:`的注释，添加你喜欢的字体名称和样式。

```
font:
  # Normal (roman) font face
  normal:
    # Font family
    #
    # Default:
    #   - (macOS) Menlo
    #   - (Linux) monospace
    #   - (Windows) Consolas
    family: 'SF  Mono'

    # The `style` can be specified to pick a specific face.
    style: Regular 
```

*   **字体大小**:在配置文件中，移动到`size:`变量并更改值，确保在整数后添加`.0`小数。

```
 # Point size
  size: 11.0 
```

*   **行高**:在配置文件中，`offset:`变量有两个子变量`x`和`y`，其中`y`可以称为行高，`x`可以称为字母间距。

```
 offset:
    x: 0
    y: 0 
```

#### 下载

Alacritty 几乎适用于所有主要平台 Linux、BSD、Windows、Mac 等。它也可以作为 bash 文件使用。你可以在这里下载二进制文件和包文件[。](https://github.com/jwilm/alacritty/releases/tag/v0.3.2)

# Tmux

[![tmux screenshot](img/5bd4e3b4a08e01a3e2e05fd030191a94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Az4eFHDb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/V8X9SX3.png)

Alacritty 没有的关键东西是标签。我们需要标签。我们喜欢把事情放在标签上，然后放到下一个标签上。

Alacritty 中选项卡的解决方案是 Tmux。Tmux 是基于命令行的窗口和会话管理器。通过它的键绑定，Tmux 变得非常强大。您可以同时打开多个会话，并让作业一直运行，直到您下次访问它们。

#### 下载

Tmux 可以直接从 [github](https://github.com/tmux/tmux/releases) 下载，也可以通过包管理器安装。

# Vim

最后，维姆。这不需要介绍。除了配色方案文件，我不使用任何 vim 插件。

# 我的设置

我花了大量的时间研究配置选项、配色方案和其他设置，所以你不必。我已经将我所有的设置保存在我的 github [ap4gh/settings](https://github.com/ap4gh/settings) 资源库中。只需将它们复制粘贴到各自的位置即可。

#### 故障排除

某些 vim 配置可能会出现问题。但别担心，我是来帮你的。就在我的 [twitter](https://twitter.com/ok_ape) 上发个消息。

阅读下一篇:[用盖茨比创建博客的最简单方法](https://www.diaryofdev.com/how-I-created-devdiary-blog-20190126/)