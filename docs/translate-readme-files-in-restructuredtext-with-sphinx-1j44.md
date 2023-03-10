# 用 Sphinx 翻译 reStructuredText 中的自述文件

> 原文：<https://dev.to/blueset/translate-readme-files-in-restructuredtext-with-sphinx-1j44>

reStructuredText (reST)是一种在 Python 开发人员社区中流行的标记语言。reST 是用于`docutils`、Sphinx 文档生成器和 Python 包索引(PyPI)的标准标记语言。然而，到目前为止，reST 仍然不够流行。大多数翻译平台，包括我现在用于 EH Forwarder Bot 的 Crowdin，都不支持 reST 文档。

Sphinx 提供了一个插件`sphinx-intl`，它从 reST 文档中提取字符串并编译成 GNU `gettext`消息目录模板(。pot)文件，并用翻译成其他语言的字符串构建新文档。GNU `gettext`格式被翻译平台广泛接受，让我们的生活变得更加容易。如果您正在生成 HTML 或 PDF 文档，这将是现成的，但是如果您想要一个 reST 输出，这就不那么简单了。

为了利用我们在`sphinx-intl`中所拥有的，我们需要的是一个输出 reST 的 Sphinx 文档编写器。有一个名为 [`restbuilder`](https://github.com/sphinx-contrib/restbuilder) 的插件正是为此服务的，但它已经一年多没有更新了。`restbuilder`目前正在[寻找维护人员](https://github.com/sphinx-contrib/restbuilder/issues/3)。不幸的是，我真的没有太多的时间来维护这样一个复杂的项目。我所做的只是[分叉项目](https://github.com/blueset/restbuilder)，包括一些来自现有 PRs 的其他修复，并自己修复一些更多的东西。

## 提取字符串

由于`sphinx-build`通常使用目录，我们需要创建一个新的临时目录来隔离`README.rst`。我们也不想仅仅为了翻译而写一个`conf.py`文件，因为一切都可以在命令行中指出。

```
mkdir .build_readme
cp README.rst .build_readme/README.rst
sphinx-build -b gettext -C -D master_doc=README -D gettext_additional_targets=literal-block,image .build_readme ./readme_translations/locale/ .build_readme/README.rst
rm -rf .build_readme 
```

Enter fullscreen mode Exit fullscreen mode

第 3 行使用的标志:

*   `-b gettext`:使用 gettext 字符串提取器作为构建器
*   `-C`:不使用任何配置文件，仅使用`-D`选项
*   `-D master_doc=README`:设置首页名为`README`(这是我们这里唯一的文件)
*   `-D gettext_additional_targets=literal-block,image`:将代码块和图像(以及标题)包含到消息目录中。
*   `.build_readme`:源目录
*   `./readme_translations/locale/`:目的目录
*   `.build_readme/README.rst`:要构建的文件

这会将字符串提取到`./readme_translations/locale/README.pot`中。然后，该文件可以上传到任何翻译平台上，或者直接传递给翻译人员。

## 建立翻译后的自述文件

当你从翻译器那里得到翻译后的字符串时，你需要把它整理成一个`sphinx-intl`可以识别的文件夹结构，即`{language_code}/LC_MESSAGES/README.(po|mo)`。我已经把它们安排在`readme_translations/locale/{language_code}/LC_MESSAGES/`了。

为了构建翻译后的自述文件，我们需要为 Sphinx 安装`restbuilder`插件。这里我将使用我的叉子作为例子。

```
git clone https://github.com/blueset/restbuilder.git
cd restbuilder.git
python3 setup.py 
```

Enter fullscreen mode Exit fullscreen mode

然后，编写一个脚本来遍历 locale 文件夹，获得可用语言的列表，并使用它来构建要运行的命令列表。

```
import glob
from pathlib import Path

# My language code is defined in a POSIX-like style. E.g. en_US languages = [i[i.rfind('/')+1:] for i in glob.glob("./readme_translations/locale/*_*")]

# Compile .po files to .mo sources = glob.glob("./**/*.po", recursive=True)
dests = [i[:-3] + ".mo" for i in sources]
actions = [["msgfmt", sources[i], "-o", dests[i]] for i in range(len(sources))]

# Build translated README files actions.append(["mkdir", "./.build_readme/source"])
actions.append(["cp", "README.rst", "./.build_readme/source/README.rst"])

locale_dirs = (Path('.') / "readme_translations" / "locale").absolute()
for i in languages:
    actions.append(["sphinx-build", "-b", "rst", "-C",
                    "-D", f"language={i}", "-D", f"locale_dirs={locale_dirs}",
                    "-D", "extensions=sphinxcontrib.restbuilder",
                    "-D", "master_doc=README", "./.build_readme/source", f"./.build_readme/{i}"])
    actions.append(["mv", f"./.build_readme/{i}/README.rst", f"./readme_translations/{i}.rst"])
    actions.append(["rm", "-rf", f"./.build_readme/{i}"])
actions.append(["rm", "-rf", "./.build_readme/source"]) 
```

Enter fullscreen mode Exit fullscreen mode

使用的更多标志:

*   `-b rst`:使用 reST 作为输出格式
*   `-D language={language}`:表示要使用的语言代码
*   `-D extensions=sphinx.contrib.restbuilder`:加载已安装的`restbuilder`扩展

使用上面的命令，您可以使用 Sphinx 和 GNU `gettext`消息目录自动构建已翻译的自述文件。对于使用 [doit automation](https://pydoit.org/) 的完整示例代码，请访问 EFB 电报主通道库中的[脚本，并查找`task_gettext`和`task_msgfmt`方法。](https://github.com/blueset/efb-telegram-master/blob/master/dodo.py)

帖子[用 Sphinx](https://blog.1a23.com/2019/09/15/translate-readme-files-in-restructuredtext-with-sphinx/) 翻译 README 文件最先出现在 [1A23 博客](https://blog.1a23.com)上。