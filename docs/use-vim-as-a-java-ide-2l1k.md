# 将 Vim 用作 Java IDE

> 原文：<https://dev.to/spacevim/use-vim-as-a-java-ide-2l1k>

# 使用 Vim 作为 Java IDE

> 为什么 canonical_url 没有 word？！
> 原点 URL 是[https://spacevim.org/use-vim-as-a-java-ide/](https://spacevim.org/use-vim-as-a-java-ide/)

这是将 SpaceVim 用作 Java IDE 的通用指南，包括层配置和使用。
将涵盖以下各部分:

*   [安装](#installation)
*   [语言服务器](#language-server)
*   [代码完成](#code-completion)
*   [代码大纲](#code-outline)
*   [重命名符号](#rename-symbol)
*   [Javadoc 悬停](#javadoc-hovers)
*   [语法 lint](#syntax-lint)
*   [导入包](#import-packages)
*   [跳转到测试文件](#jump-to-test-file)
*   [运行代码](#running-code)
*   [代码格式化](#code-formatting)
*   [REPL](#repl)

### 安装

SpaceVim 是 Vim 和 neovim 配置，所以你需要安装 Vim 或者 neovim，
这里有两个安装 neovim 和 vim8 带`+python3`功能的指南。
按照[快速入门指南](https://spacevim.org/quick-start-guide/)安装 SpaceVim。

SpaceVim 默认不启用语言层，所以需要启用`lang#java`层。
按`SPC f v d`打开 SpaceVim 配置文件，添加以下部分:

```
[[layers]]
  name = "lang#java" 
```

### 语言服务器

要启用语言服务器协议支持，您可能需要启用 lsp 层。

```
[[layers]]
  name = "lsp"
  filetypes = [
    "java"
  ]
  [layers.override_cmd]
    java = [
    "java",
    "-Declipse.application=org.eclipse.jdt.ls.core.id1",
    "-Dosgi.bundles.defaultStartLevel=4",
    "-Declipse.product=org.eclipse.jdt.ls.core.product",
    "-Dlog.protocol=true",
    "-Dlog.level=NONE",
    "-noverify",
    "-Xmx1G",
    "-jar",
    "D:\\dev\\jdt-language-server-latest\\plugins\\org.eclipse.equinox.launcher_1.5.200.v20180922-1751.jar",
    "-configuration",
    "D:\\dev\\jdt-language-server-latest\\config_win",
    "-data",
    "C:\\Users\\Administrator\\.cache\\javalsp"
    ] 
```

您需要用 org . eclipse . equinox . launcher jar 的实际名称替换`D:\dev\jdt-language-server-latest\plugins\org.eclipse.equinox.launcher_1.5.200.v20180922-1751.jar`

配置标志可以指向:

*   `config_win`，适用于 Windows
*   `config_mac`，适用于 MacOS
*   `config_linux`，针对 Linux

数据标志值应该是服务器工作目录的绝对路径。这应该不同于用户项目文件的路径(在初始化握手期间发送)。

### 代码补全

已经包含在`lang#java`层中的 javacomplete2 为 java 文件提供了 omnifunc 和 deoplete 源代码。
有了这个插件和`autocomplete`层，完工弹出菜单会自动打开。

[![code complete](img/59039ed510dfd3bf40081fead1eb1889.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hBKVL9D5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/46297202-ba0ab980-c5ce-11e8-81a0-4a4a85bc98a5.png)

### 代码大纲

默认的大纲插件是 tagbar，键绑定是`F2`。这个键绑定将在左侧打开一个大纲侧边栏。

[![java outline](img/702189c4bc6e182bcde46442a3052c4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yOiVAX5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/53250502-7c313d80-36f5-11e9-8fa2-8437ecf57a78.png)

要在当前缓冲区中模糊查找轮廓，您需要启用一个模糊查找层，例如 denite 层，
然后按`Leader f o`:

[![java fuzzy outline](img/e58eb7cc9c9e28f9137924c81e000272.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u2qd9ZLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53250728-f1047780-36f5-11e9-923d-0b34568f9566.gif)

### 重命名符号

为 java 启用 lsp 层后，可以使用`SPC l e`来重命名光标下的符号:

[![rename java symblo](img/c54ca782db1c19ac149df3acf31dd2bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUaZbGOY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53250190-da115580-36f4-11e9-9590-bf945fa8dcc0.gif)

### Javadoc 悬停

获取光标符号 doc 的默认键绑定是`SPC l d`或`K`:

[![javadoc](img/e515d3f0294b4e30a0b49fb390b05c59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQ2CYsYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/53255520-bf44de00-3700-11e9-9f47-50bc50ed6e83.gif)

### 语法线头

`checkers`层提供异步林挺功能，该层默认使用 [neomake](https://github.com/neomake/neomake) 。
neomake 支持 maven、gradle 和 eclipse 项目。它将为这些项目自动生成类路径。

[![lint-java](img/37bc8e698f7933573256999a5144be16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lnWoo_cd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/46323584-99b81a80-c621-11e8-8ca5-d8eb7fbd93cf.png)

在上面的图片中，我们可以看到棋盘格层提供以下功能:

*   在快速修复窗口中列出错误和警告
*   标志错误和警告位置在左侧
*   在状态行上显示错误和警告的数量
*   在当前行下方显示光标错误和警告信息

### 导入包

导入包有两种功能，自动导入包和手动导入包。SpaceVim 将在 popmenu 上选择类名后导入包。
同样，您可以使用键绑定`<F4>`在光标处导入类。如果有多个类别，当前窗口下方会显示一个菜单。

[![import class](img/0ba97359c1aecbc3d086654e5c6eda82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2HjytjK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/13142418/46298485-c04e6500-c5d1-11e8-96f3-01d84f9fe237.png)

### 跳转到测试文件

SpaceVim 使用 vim-project 管理项目中的文件，您可以在项目的根目录下添加一个`.projections.json`，内容如下:

```
{  "src/main/java/*.java":  {"alternate":  "src/test/java/{dirname}/Test{basename}.java"},  "src/test/java/**/Test*.java":  {"alternate":  "src/main/java/{}.java"}  } 
```

通过这种配置，您可以通过命令`:A`在源代码和测试文件之间跳转

[![jump-test](img/0a24b2bad68dba243b100d7aa1b98793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_MTQA1u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/46322905-12b57300-c61e-11e8-81a2-53c69d10140f.gif)

### 运行代码

基于 JavaUnite，你可以使用`SPC l r c`来运行当前函数或者使用`SPC l r m`来运行当前类的主函数。

[![run-main](img/78badcc50bc1b229d6c3b09c42792cc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NUE-pwCe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/46323137-61174180-c61f-11e8-94df-61b6998b8907.gif)

### 代码格式化

为了格式化 java 代码，你还需要在你的路径中有[不可信](https://github.com/uncrustify/uncrustify)或[样式](http://astyle.sourceforge.net/)。
顺便说一句，谷歌的 [java 格式器](https://github.com/google/google-java-format)也能很好地与 neoformat 兼容。

[![format-java](img/3d96e5792ada873fdb6a246c1e02d719.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yW8UnSrt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/46323426-ccadde80-c620-11e8-9726-d99025f3bf76.gif)

### REPL

您需要安装 jdk9，JDK 9 提供了一个内置工具`jshell`，SpaceVim 使用`jshell`作为默认的下级 REPL 进程:

[![REPl-JAVA](img/77afd8093525969838ad3e85c39924dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_zkiCB71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/13142418/34159605-758461ba-e48f-11e7-873c-fc358ce59a42.gif)