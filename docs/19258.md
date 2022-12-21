# 成为 Python Jinja 大师:自动化前台素材编辑

> 原文：<https://dev.to/karlredman/be-a-python-jinja-master-automated-front-matter-editing-1b8a>

## TL；速度三角形定位法(dead reckoning)

我很高兴地宣布 EditFrontMatter 的初始版本:一个使用 Jinja2 模板编辑 markdown front matter 的 python 模块。

*   参见: [EditFrontMatter 项目主页](https://karlredman.github.io/EditFrontMatter/index.html)
*   文档中包含了几个示例
    1.  [基本用法](https://karlredman.github.io/EditFrontMatter/examples/example1/readme.html)
    2.  [高级多路处理器](https://karlredman.github.io/EditFrontMatter/examples/example2/readme.html)
    3.  [使用多线程编辑文件的递归目录遍历器](https://karlredman.github.io/EditFrontMatter/examples/example3/readme.html)

## 音高:

想象一下，您可以在 markdown 中保留所有不同的文章和文档内容。然后使内容适合任何静态站点生成器及其主题，而无需手动编辑每个文件。使用 [EditFrontMatter](https://karlredman.github.io/EditFrontMatter/index.html) 你可以编写简单的脚本，利用 [Jinja2](http://jinja.pocoo.org/) 的能力，轻松地添加/编辑/删除你的减价文件的封面。

## 一次演示:

这是一个简单的例子，你也可以在文档中找到，如[例 1](https://karlredman.github.io/EditFrontMatter/examples/example1/readme.html) 。这里，我们使用一个 Jinja2 模板来编辑一个 markdown 文件的前端内容。在编辑过程中，模板中包含的*以外的所有字段都将被忽略。此外，虽然空白和注释将被删除 <sup id="fnref2">[2](#fn2)</sup> ，但是原始前端内容的顺序将被保留<sup id="fnref3">[3](#fn3)</sup>——新的字段将被追加。*

*   带有 yaml 前置内容的原始降价文件( [example1.md](https://github.com/karlredman/EditFrontMatter/blob/master/examples/data/example1.md) )

```
---
title: "EditFrontMatter  Class  Example  1"
description: "Edit  some  fields  in  this  front  matter"
catagories: [programming, python, markdown]

deleteme: this will be deleted

tags: [front matter, administration, testing]

# comments and spaces will be eliminated (see docs)

author: "Karl  N.  Redman"
creatordisplayname: "Karl  N.  Redman"
creatoremail: "karl.redman@example.com"
date: 2019-05-23T17:43:45-05:00
lastmodifierdisplayname: "Karl  N.  Redman"
lastmodifieremail: "karl.redman@gmail.com"
lastmod: 2019-05-23T17:43:45-05:00
toc: false
type: "page"
hasMath: false
draft: false
weight: 5
---

# EditFontMatter Class Example 1

Edit several fields of front matter.

## Fields affected in this example:
 * toc
 * note: uses local template variable
 * pre: false
 * post: true
* draft:
 * note: uses jinja2 filter (callback)
 * pre: false
 * post: true
* hasMath
 * note: uses program variable
 * pre: true
 * post: false
* stuff:
 * note: uses program variable to create field
 * pre: did not exist
 * post: (list) ['one', 'two', 'three']
* deleteme:
 * note: removed from final result
 * pre: this will be deleted
 * post: N/A 
```

*   Jinja2 模板，将更新源减价文件( [template1.j2](https://github.com/karlredman/EditFrontMatter/blob/master/examples/data/template1.j2) )的前置事项数据。请注意，`toc`字段处理被模板覆盖。所有局部`set` Jinja 变量优先于其他处理方法。

```
{% set toc = "true" %}

toc: {{ toc }}
draft: {{ false | canPublish }}
hasMath: {{ hasMath }}
stuff: {{ addedVariable }} 
```

*   用 Jinja2 模板编辑 markdown 文件的 python 程序( [example1.py](https://karlredman.github.io/EditFrontMatter/examples/example1/example1.html) :

这个程序/脚本使用一个 [Jinja2 模板过滤器](http://jinja.pocoo.org/docs/2.10/templates/#filters)和一个回调函数，通过调用[edit front matter . add _ jinja filter()](https://karlredman.github.io/EditFrontMatter/editfrontmatter/editfrontmatter.EditFrontMatter.EditFrontMatter.add_JinjaFilter.html)来为`draft`字段提供数据。另外，在调用 [EditFrontMatter.run()](https://karlredman.github.io/EditFrontMatter/editfrontmatter/editfrontmatter.EditFrontMatter.EditFrontMatter.run.html#editfrontmatter.EditFrontMatter.EditFrontMatter.run) 时会提供特定的`key, value`变量对。来自`run()`的值将用于添加或编辑`key, value`对。最后，实例(列表)变量`EditFrontMatter().keys_toDelete`指定在前面要删除的键。

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 

from editfrontmatter import EditFrontMatter
import os

def canPublish_func(val):
    # do some processing....
    return True

def main():

    # generic path - overridden by env var `TEST_DATA_DIR`
    DATA_PATH = "../data/"

    if "TEST_DATA_DIR" in os.environ:
        DATA_PATH = os.path.abspath(os.environ.get("TEST_DATA_DIR")) + "/"

    # set path to input file
    file_path = os.path.abspath(DATA_PATH + "example1.md")

    # initialize `template_str` with template file content
    template_str = ''.join(open(os.path.abspath(DATA_PATH + "template1.j2"), "r").readlines())
    print(template_str)

    # instantiate the processor
    proc = EditFrontMatter(file_path=file_path, template_str=template_str)

    # set fields to delete from yaml
    proc.keys_toDelete = ['deleteme']

    # add a filter and callback function
    proc.add_JinjaFilter('canPublish', canPublish_func)

    # populate variables and run processor
    proc.run({'toc': 'no effect', 'hasMath': "false",
              'addedVariable': ['one', 'two', 'three']})

    # dump file
    print(proc.dumpFileData())

if __name__ == '__main__':
    main() 
```

*   最终输出:

```
---
title: "EditFrontMatter  Class  Example  1"
description: "Edit  some  fields  in  this  front  matter"
catagories:
- programming
- python
- markdown
tags:
- front matter
- administration
- testing
author: Karl N. Redman
creatordisplayname: Karl N. Redman
creatoremail: karl.redman@example.com
date: 2019-05-23 22:43:45
lastmodifierdisplayname: Karl N. Redman
lastmodifieremail: karl.redman@gmail.com
lastmod: 2019-05-23 22:43:45
toc: true
type: page
hasMath: false
draft: true
weight: 5
stuff:
- one
- two
- three
---

# EditFontMatter Class Example 1

Edit several fields of front matter.

## Fields affected in this example:
 * toc
 * note: uses local template variable
 * pre: false
 * post: true
* draft:
 * note: uses jinja2 filter (callback)
 * pre: false
 * post: true
* hasMath
 * note: uses program variable
 * pre: true
 * post: false
* stuff:
 * note: uses program variable to create field
 * pre: did not exist
 * post: (list) ['one', 'two', 'three']
* deleteme:
 * note: removed from final result
 * pre: this will be deleted
 * post: N/A 
```

# 我的个人使用案例:

请允许我描述一下我的工作流程:我维护着一组专门降价内容的存储库。这些内容被分成不同的主题，从个人日志到各种项目和处于不同完成状态的文章。在过去的几年里，我写的文档数量已经超过了 1000 个。为了简化我的工作流程，我希望所有内容都可以在一个地方访问，同时能够预览这些文档，并最终使用各种方法发布它们。为此，我所有的降价文件都包含了最少的封面内容。当我发布这些内容文件时，我需要某种方法来批处理内容文件并修改它们的元数据。

我使用 [vimwiki](https://vimwiki.github.io/) 来编辑我所有的减价内容。Vimwiki 允许我轻松地索引各种主题，保存主题相关的日记，或者使用 [Vim](https://www.vim.org/) 编辑内容:我选择的“前往”编辑器。我所有的各种项目和主题相关的内容都在单独的 [git](https://git-scm.com/) 存储库中进行跟踪，并作为[子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)导入到“工作树”中。这使得添加、归档和修改主题变得非常简单。

我的个人开发生态系统使用由[雨果](https://gohugo.io/)生成的静态站点来托管内容(顺便说一下，我的知识库——我使用[docdocdock 主题](https://themes.gohugo.io/docdock/))。这个静态站点被插入到我的生态系统的构建系统中( [drone](https://drone.io/) )。当我编辑`working three`中的任何文档，并将该内容推送到我的 git 存储库时，我的 [Gitea](https://gitea.io) 实例会获取更改并触发一个无人机构建。

反过来，drone 构建了我的可搜索知识库，并将其发布到我的生态系统的 web 服务器上。从 a `git push`到通过我的 web 服务显示内容，总时间是`< 1 minute`。这包括 [EditFrontMatter](https://karlredman.github.io/EditFrontMatter) 所做的处理，以设置任何数量的特定主题和其他目的所需的前置内容字段(例如，将前置内容中的“编辑此页面”链接改为指向文档本身的相应内容的 Gitea `edit page`:内容的在线编辑是一个内置的好处！).

在 EditFrontmatter 的帮助下，当我决定将一个项目从我的个人知识库转移到一个发布实体(如 Github)时，我可以很容易地更改“编辑此页面”的 URL。我不再担心必须手动编辑一堆内容文件来更改 Hugo 主题，或者更改正在使用的静态站点生成器。

我现有的脚本使用一组 Jinja 模板来为我执行这些操作。只需几秒钟就能产生准确的结果，而不是几分钟容易出错的冗长编辑。虽然这看起来很复杂，但实际上并不复杂。这些步骤中的大部分(如果不是全部的话)是我们作为开发人员无论如何都要做的事情。这是我简化流程的尝试，因为这是一个定义良好的工作流程。

# 结论:

在以前的文章中，我提到过许多文章是相互关联的。我还回避了一个事实，我正在为一个项目做一个更大、更复杂的文档集——构建一个你可以在任何地方使用的完整的开发生态系统。虽然 EditFrontmatter 是一个小型的类/模块(不到 150 行代码)，但是文档却有 1000 行文本。这就是文档的本质——相对于代码行，产品总是比文档短得多。

markdown 的强大之处在于，我可以继续编写我的项目文档的内容，而不必担心我最终将如何发布它——或者我将在哪里发布它。事实上，由于 EditFrontMatter 提供了轻松操作我创建的内容的元数据的能力，我可以在各种发布实体中近乎实时地查看更改。我没有被“供应商锁定”,我的项目也没有。此外，根据我使用的构建系统，我可以很容易地在 markdown 本身中包含从一个文档到下一个文档的内容——推广“编写一次”的概念。

最后，如果你对我如何生成[斯芬克斯感到好奇，请阅读将通过](https://sphinx-rtd-theme.readthedocs.io/en/stable/) [Github 页面](https://help.github.com/en/articles/configuring-a-publishing-source-for-github-pages)托管的 Docs 主题文档，查看 EditFrontMatter 主页上的[关于主题](https://karlredman.github.io/EditFrontMatter/about_theme/about_theme.html)部分。如果你想了解我为什么使用`reST`和`python`来为`Hugo`构建静态页面，请查看 [FAQ](https://karlredman.github.io/EditFrontMatter/faq.html) 。

现在去做一个 Python Jinja 高手吧！

* * *

1.  支持 markdown front matter 的热门站点生成器:Directly - [盖茨比](https://www.gatsbyjs.org/docs/adding-markdown-pages/#add-a-markdown-file)、[赫索](https://hexo.io/docs/front-matter)、[雨果](https://gohugo.io/content-management/front-matter)、[杰凯尔](https://jekyllrb.com/docs/front-matter/)。间接- [Netlify CMS](https://www.netlifycms.org/docs/nextjs/) ， [Next.js](https://jaketrent.com/post/serve-markdown-nextjs-server/) ， [Nuxt](https://dzone.com/articles/including-markdown-content-in-a-vue-or-nuxt-spa) 。[还有很多...](https://www.staticgen.com/) [↩](#fnref1)

2.  这个模块的未来版本可能会提供额外的功能，比如保存注释，通过[ruamel . YAML](https://yaml.readthedocs.io/en/latest/overview.html)；这取决于对这些特征的请求频率。 [↩](#fnref2)

3.  EditFrontMatter 使用 [oyaml](https://github.com/wimglenn/oyaml) 来维护前台的顺序。(见脚注`2` ) [↩](#fnref3)