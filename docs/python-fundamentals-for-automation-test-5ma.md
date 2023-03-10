# 自动化测试的 Python 基础

> 原文：<https://dev.to/cuongld2/python-fundamentals-for-automation-test-5ma>

```
I. About Python
II. Install Python :
    Get and install from official site:
    2\. Check for installation correctly or not:
    3\. For MacOS:
    4\. For Linux:
    5\. Python virtual environment:
III. Install pips and other libs:
    Install pip:
    2\. Install other libs
IV. Git Installation
V. Pycharm IDE
    2\. Simple Configuration and usage:
        2.1 : Fresh new project:
        2.2 : Clone projects from other source:
VI. Python types and basic structures 
```

Enter fullscreen mode Exit fullscreen mode

一、关于 Python

Python 是一种通用的灵活编程语言。

[![Alt Text](img/eadfabdb04803d5f33585d99d4463df6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--42telMVR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y278i0nqc1tfw7nkiso0.jpg)

目前我们有两个主要的 Python 版本:Python 2 和 Python 3。但是从 2020 年开始将不再支持 Python 2。

此外，Python3 有很多积极的变化，使其易于开发和维护(易于处理字符串，非 ASCII 字符..)
二。安装 Python:

```
Get and install from official site: 
```

Enter fullscreen mode Exit fullscreen mode

请到 Python 的官方网站下载:Python 下载网站

[![Alt Text](img/195a37eaf98ba7cf2a8053b58008d3ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hIUEsF4m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j2cjzkoazq7x0tf8hgmz.PNG)

获取最新版本:当前最新版本是 3.7.4

注意:在 Win 7 中，如果安装 Python 3，请安装版本 3-7-3(不适用于 3-7-4)...

从 exe 安装 Python

[![Alt Text](img/93780417027755f43735fe61774ab6e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x7zTYi0k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxpflresrilchtp782ov.png)

1.  检查安装是否正确:

安装成功后，转到系统环境变量检查路径。

[![Alt Text](img/61cb9684118c09fcdd7d9ff8fc56c7e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BMrbQX2a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ob0exzimimn4ke7fd962.PNG)

检查 python 是否安装正确:

打开命令提示符-> python - version

→应该显示当前 python 版本为:3.7.4

[![Alt Text](img/5c28f6a01603192c04401a3f16a3cda5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dBgLrYX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fmq5my7ss79quwcxjfm5.PNG)

```
Some simple REPL python: 
```

Enter fullscreen mode Exit fullscreen mode

REPL 代表读取评估打印循环，是 Pycom 设备上可访问的交互式 MicroPython 提示符的名称

```
From Command Prompt :→ python 
```

Enter fullscreen mode Exit fullscreen mode

```
→ print('Hello World')

a = 5

b = 100

a+b → 105

quit() to end current repl 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt Text](img/854166260ad9e194196ecfbfbed077b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wgdv1IKV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h16x4hiulzs0ismwwgbl.PNG)

1.  对于 MacOS:

通常 python2 已经安装在 MacOS 中，你不能卸载 python2

接下来你需要做的是安装 Python3 和现有的 python2

安装 Python3 有两种选择:

```
Go to Python official site and download the package to install
Install python via homebrew 
```

Enter fullscreen mode Exit fullscreen mode

1.  对于 Linux:

请遵循这个指南 python-for-linux

1.  Python 虚拟环境:

    手动创建虚拟环境:虚拟环境 python
    或者你可以通过你喜欢的 IDE(例如:Pycharm)用虚拟环境解释器创建项目

三。安装 pip 和其他库:

```
Install pip: 
```

Enter fullscreen mode Exit fullscreen mode

到 pip 官方网站安装:Pip 官方网站

检查是否通过以下命令安装了 pip:pip-version

[![Alt Text](img/65a49f1e3f1fe78bad8ef4d04f396fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jZl4niUb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ms3ctwmsoh27ffp331ll.PNG)

1.  安装其他库

    大多数情况下，您会通过 pip 安装 python lib

pip 安装

对于当前的使用，要安装在 python 中进行自动化测试所需的所有库，请按照以下要求安装

text fileEdit file

pip 安装-r 要求. txt

要验证是否安装了库，请在命令提示符下键入，例如，检查是否安装了 pytest:pytest-version

[![Alt Text](img/04f7d09df1735d57edd3fef26739f03c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0h9Fgqn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3l8j9d2z8kn0clebjb5z.PNG)

```
Or you can install the local lib from source code by: 
```

Enter fullscreen mode Exit fullscreen mode

pip 安装

例如:

pip install C:\ Users \ cuongld \ PycharmProjects \ pytest-testrail
IV。Git 安装

转到 git 安装页面:git-installation-page

检查是否安装了 git:git-version
v . py charm IDE

Pycharm 有 3 个主要版本:企业版、社区版和教育版

```
Download and and install

Download and install pycharm community from jetbrains site : Pycharm IDE 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt Text](img/0ea4e78ae8c2bf139d8fd94ab1f6bf37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xABYeNfQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r6lh6osnztht5dbckf2k.png)

[![Alt Text](img/81f3af855b5144848a0d2d7efaae1fb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IUL9D5NE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i3ub6ivnrwga3ihls9ku.png)

1.  简单配置和使用:2.1:全新项目:

文件→新建项目→选择要使用的框架或纯 python
2.2:从其他来源克隆项目:

我将给出 github 项目的例子

```
Go to github page stored project 
```

Enter fullscreen mode Exit fullscreen mode

[![Alt Text](img/f999e6041167be8b5ad0925d7fa573de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DUbpjqGr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bmtm8ydulnobi73pa73p.PNG)

→

[![Alt Text](img/99f40fe35d7e5aade5a4ebde004e17ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJhairQV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ennftxk6j6bm2v283vo5.PNG)

```
Clone project in pycharm 
```

Enter fullscreen mode Exit fullscreen mode

VCS →从版本控制中签出→ Git → HTTPS 克隆

(如果项目是私有的，或者只允许某些帐户访问→您可能需要在 pycharm 中输入用户名和密码)

```
Or by command line : 
```

Enter fullscreen mode Exit fullscreen mode

转到存储项目源代码的位置

cd 存储 _ 项目

git 克隆[https://username:password @ github . com/username/repository . git](https://username:password@github.com/username/repository.git)
克隆成功后，在 Pycharm 中打开项目:
文件- >打开- >选择项目目录。

不及物动词 Python 类型和基本结构

```
Every things is an object 
```

Enter fullscreen mode Exit fullscreen mode

例如，在 Java 中，要创建一个函数，必须将它赋给一个类(或接口..)

但是在 Python 中，你可以在模块中单独创建一个函数

1.  类型可以是“不可变的”、“可变的”

例如:字符串类型是不可变的

但是列表是可变的

[![Alt Text](img/83b682adc5e2390c2285694c7f084f7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zt2qQJKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4zcy14gcn72doray6u1y.PNG)

1.  基本用法:

    创建类的新实例:instance _ Class = Class _ name()
    Python 使用缩进来理解代码块
    要在类内调用函数，必须使用 with self，例如:

类设置元素(BasePageElement):

```
def find_continue_where_left_off(self,driver):

 self.find_shallow_element()

Import can be local or global in module
Inheritance from parent class : class name (BaseClassName): 
```

Enter fullscreen mode Exit fullscreen mode