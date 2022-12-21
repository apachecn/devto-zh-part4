# 介绍 parse _ it——Python 配置解析器

> 原文：<https://dev.to/naorlivne/introducing-parseit-a-python-configuration-parser-4lkd>

无论您是将 Python 用于前端、后端、大数据、学术还是仅仅需要一个简单的脚本来自动化一项烦人的任务，您都很可能会为所述应用程序传递一些配置变量，这可以通过许多不同的方式来完成:

*   环境变量
*   命令行参数
*   配置文件

每种方法都有自己的优缺点&决定使用哪种方法会带来很多问题:

*   我应该选择哪种方法来配置我的应用程序？
*   我应该选择一种还是多种方法来配置我的应用程序？
*   我的配置文件应该使用哪种文件格式？

这个列表可以继续下去，因为每个问题只会带来更多的后续问题，这就是为什么我决定创建一个单一的“parse_it”包，它可以处理围绕 Python 项目设置和配置工作所需的所有工作，parse_it 轻松处理所有这些工作，其基本概念是，决定如何配置应用程序应该留给应用程序最终用户，而不是开发者。
安装

# 安装简单:

```
pip install parse_it 
```

# 使用

使用 parse_it 几乎和安装它一样简单:

```
# Load parse_it
from parse_it import ParseIt

# Create parse_it object.
parser = ParseIt() 
```

现在，无论如何配置，您都可以读取配置值(cli args、envvars、json/yaml/etc 文件)

```
my_setting = parser.read_configuration_variable("my_setting") 
```

parse_it 允许你做更多的事情，而不仅仅是读取一个变量&包括:

*   多种文件格式支持(JSON，YAML，TOML，HCL，INI，XML)
*   从多个配置文件中找到所需的设置密钥(默认情况下，从工作目录中递归读取所有有效的文件格式)
*   EnvVar 支持(包括可配置的自动大写和前缀)
*   命令行参数支持
*   自动更正数据类型(这样你的 EnvVars & CLI 参数将返回字典、列表、整型、Boolens 等)
*   易于配置
*   每个设置的默认值&全局适用于所有设置
*   根据需要轻松标记设置，这样，如果缺少设置，将会引发错误

以上所有选项(&更多)都可以轻松配置&在位于 https://github.com/naorlivne/parse_it[的项目 GitHub repo 的单页自述指南中有所描述](https://github.com/naorlivne/parse_it)