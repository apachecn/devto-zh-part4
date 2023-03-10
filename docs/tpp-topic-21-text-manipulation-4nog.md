# TPP 主题 21:文本操作

> 原文：<https://dev.to/steadbytes/tpp-topic-21-text-manipulation-4nog>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-21-exercises/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 练习 11

> 您正在重写一个曾经使用 YAML 作为配置语言的应用程序。你的公司现在已经在 JSON 上标准化了，所以你有一堆需要转换成`.json`的`.yaml`文件。写一个脚本，取一个目录，把每个`.yaml`文件转换成对应的`.json`文件(所以`database.yaml`变成了`database.json`，内容是有效的 JSON)。

使用 [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) 和标准库 [`json`](https://docs.python.org/3/library/json.html) 模块，可以在 Python 中轻松完成 YAML 和 JSON 之间的转换。PyYaml 和`json`都分别将基本的 Python 对象(`dict`、`list`、`str`、`int` e.t.c)与 Yaml 和 JSON 相互转换，都提供了*几乎*一样的 API。

算法大纲:

1.  查找给定目录中的所有 YAML 文件
2.  使用 PyYaml 将 YAML 文件从磁盘加载到 Python 对象中
3.  将 Python 对象序列化为 JSON
4.  将序列化的 JSON 写入一个新的`.json`文件
5.  删除 YAML 文件
6.  对步骤 1 中的所有 YAML 文件重复步骤 2-5

```
import json
from pathlib import Path

import yaml

def main(d: Path):
    # make sure we have a valid directory to search in
    assert d.exists()
    assert d.is_dir()

    for yaml_f in d.glob("*.yaml"):
        # load original YAML data
        with yaml_f.open() as f:
            data = yaml.safe_load(f)
        # write data to new json file
        json_f = d / f"{yaml_f.stem}.json"
        with json_f.open(mode="w") as f:
            json.dump(data, f, indent=4)
        # delete original YAML file
        yaml_f.unlink()
        print(f"{yaml_f} -> {json_f}") 
```

完整的 CLI 脚本见 GitHub 上的 [`yaml_to_json.py`](https://github.com/SteadBytes/study/blob/fd20064fc352fabb0f48539733d56f45b33690f0/the-pragmatic-programmer-20th/21/exercises/11/yaml_to_json.py) 。

## 练习 12

> 您的团队最初选择使用`camelCase`名称作为变量，但是后来改变了他们的集体想法，转而使用`snake_case`。写一个脚本，扫描所有的源文件寻找`camelCase`的名字，并报告它们。

在现实世界中，我只需要完成任务，我会*肯定*用`egrep` :
解决这个问题

```
$ egrep -rn --color "\b[a-z]+((\d)|([A-Z0-9][a-z0-9]+))+([A-Z])?" /path/to/source/directory 
```

然而，出于教育目的，我在 Python 脚本中实现了这个功能以及单元测试。参见 GitHub 上的 [`camel_finder.py`](https://github.com/SteadBytes/study/blob/323b1eefc60fd219dd0ab2d59e6372572f429862/the-pragmatic-programmer-20th/21/exercises/12/camel_finder.py) 了解完整的 CLI 脚本和测试。

该任务分为以下几个高级步骤:

1.  迭代一系列文件(作为脚本的参数提供)
2.  遍历每个文件的行
3.  识别每行中的任何`camelCase`字符串
4.  输出每个识别的`camelCase`字符串的报告

Python 中的第 1 步和第 2 步很简单:

```
import sys
from pathlib import Path

def main(files: List[Path]):
    for file in files:
        with file.open() as f:
            for line in f:
                # do steps 3 and 4 

if __name__ == "__main__":
    if len(sys.argv) == 1:
        err_exit("Usage: ./camel_finder.py FILES...")

    main([Path(f) for f in sys.argv[1:]] 
```

第 3 步可以用一个正则表达式实现，如`egrep`示例所示。对于
这个练习，我使用的是 [Google Java style guide 对*`camelCase`-*而不是* PascalCase 的`camelCase`*](https://google.github.io/styleguide/javaguide.html#s5.3-camel-case) *定义。* 

```
import re

CAMEL_RE = re.compile(
    (
        # 1st character must be lower case
        r"\b[a-z]+"
        # followed by a single digit
        # OR upper case character/number followed by lower case characters or number
        r"((\d)|([A-Z0-9][a-z0-9]+))"
        # final character *may* be upper case
        r"+([A-Z])?"
    )
) 
```

*   这个正则表达式的演示:[https://regex101.com/r/paY5R6/1/](https://regex101.com/r/paY5R6/1/)

使用`CAMEL_RE`，可以从字符串中提取`camelCase`子字符串的位置。给定行的表示和其中包含的任何`camelCase`
子串的位置由两个`NamedTuple`对象提供:`MatchGroup`
和`Match`。这些是受标准库 [`re.Match`](https://docs.python.org/3/library/re.html#match-objects) 对象的启发，这些对象用于为`MatchGroup`
和`Match`提供数据。这在概念*“一行带有`camelCase`子串的文本*和*“一个匹配`camelCase`
子串的正则表达式”*之间提供了一个分离边界。从而允许在必要时更容易地改变用于寻找`camelCase`子串
的底层机制。

```
from typing import Generator, Iterable, NamedTuple

class MatchGroup(NamedTuple):
    start: int
    end: int

class Match(NamedTuple):
    lineno: int
    groups: List[MatchGroup]
    line: str

def find_camel(lines: Iterable[str]) -> Generator[Match, None, None]:
    for i, line in enumerate(lines):
        groups = [MatchGroup(*m.span()) for m in CAMEL_RE.finditer(line)]
        if groups:
            yield Match(i, groups, line) 
```

用法示例:

```
>>> lines = ["a camelCase line", "thisIs a line with two camelCase words", "a line without camel case"]
>>> for match in find_camel(lines):
...     print(match)
Match(lineno=0, groups=[MatchGroup(start=2, end=11)], line='a camelCase line')
Match(lineno=1, groups=[MatchGroup(start=0, end=6), MatchGroup(start=23, end=32)], line='thisIs a line with two camelCase words') 
```

步骤 4(不安装第三方软件包)包括使用 [ANSI 颜色转义码](https://en.wikipedia.org/wiki/ANSI_escape_code#Colors)。为了模拟`grep`的`--color`选项，每个匹配的
行都应该以如下格式输出:

```
<optional purple filename>:<green line number>: <white text> <red camelCase match> <white text>... 
```

例如:

```
<span>/path/to/my/file.txt</span>:<span>5</span>: this line has <span>camelCase</span> words as <span>subStrings</span>. 
```

“漂亮匹配”字符串是通过迭代给定的`Match`的
的每个`MatchGroup`并提取在`MatchGroup`的位置之前包含不匹配文本
*的原始行的一部分，提取在`MatchGroup`位置的原始行
的一部分并添加红色转义码:`\033[31m`来构建的。*

*   注意，每次使用颜色转义码后，使用`\033[m`重置颜色

```
def pretty_match(m: Match, filename: str = None) -> str:
    """
    Build a 'pretty' string representation of `m`, with coloured text and line
    numbers; optionally prefixed with `filename`.

    Colours:
        - Line numbers = green
        - Matches = red
        - Filenames = purple
        - Non-matching text = white
    """
    pretty_name = f"\033[35m{filename}\033[m:" if filename else ""
    l = []
    prev = 0
    for g in m.groups:
        # text up until match in white, match in red
        l.append((f"{m.line[prev:g.start]}"f"\033[31m{m.line[g.start:g.end]}\033[m"))
        prev = g.end
    l.append(m.line[prev:])
    return "".join([f"{pretty_name}\033[32m{m.lineno}\033[m:"] + l) 
```

将所有这些放在原来的`main`函数中:

```
def main(files: List[Path]):
    """
    Print a report on the locations of all camelCase strings in `file`. See
    `pretty_match` for output format.
    """
    show_filenames = len(files) > 1
    for file in files:
        with file.open() as f:
            for m in find_camel(f):
                print(pretty_match(m, filename=file if show_filenames else None)) 
```

## 练习 13

> 在前一个练习的基础上，添加在一个或多个文件中自动更改这些变量名的能力。记得保留原件的备份，以防事情变得非常非常糟糕。

同样，查看 GitHub 上的 [`camel_finder.py`](https://github.com/SteadBytes/study/blob/323b1eefc60fd219dd0ab2d59e6372572f429862/the-pragmatic-programmer-20th/21/exercises/13/camel_finder.py) 获取完整的 CLI 脚本和测试。

一旦找到一个`camelCase`子串，将它转换成 snake_case 需要两个步骤:

1.  在每个`camelCase`驼峰之间插入一个`_`字符
    *   `camelCase -> camel_Case`
    *   `camelCamelCase -> camel_Camel_Case`
2.  转换为小写

通过匹配和
捕获一个‘驼峰’字符:
，使用捕获组的正则表达式替换可用于步骤 1

```
camel(C)ase
camel(C)amel(C)ase 
```

然后通过引用替换:`'_\1'`中的捕获组，在“驼峰”字符前插入一个`_`字符。

```
 CONVERT_CAMEL_RE = re.compile(
    (
        # match a nomral 'hump' i.e. camel(C)ase or camel1(C)ase
        r"((?<=[a-z0-9])[A-Z]"
        # match mid-string uppercase humps, ignoring existing underscores
        # i.e. CAMEL(C)ase or HTTP(E)rror
        r"|(?!^)(?<!_)[A-Z](?=[a-z]))"
    )
)

def convert_camel_word(w: str) -> str:
    return CONVERT_CAMEL_RE.sub(r"_\1", w).lower() 
```

*   这个正则表达式的演示:[https://regex101.com/r/3AjsDU/1](https://regex101.com/r/3AjsDU/1)

用法示例:

```
>>> convert_camel_word("camelCase")
'camel_case'

>>> convert_camel_word("camelCamelCase")
'camel_camel_case'

>>> convert_camel_word("snakey_camelCase")
'snakey_camel_case' 
```

`convert_camel_word`旨在转换一个*单个*字，而不是包含`camelCase`字的任意文本的字符串
。例如:

```
>>> convert_camel_word("System.out.println(Arrays.toString(myArray));")
'system.out.println(_arrays.to_string(my_array));' 
```

而不是:

```
'System.out.println(Arrays.to_string(my_array));' 
```

为了转换任意文本的字符串，来自练习 12 的原始`camelCase`匹配被用于*首先*找到
单个`camelCase`字符串的位置。一旦找到，它们可以被传递到
`convert_camel_word`并插入到原始文本的正确位置。

```
 # factored out of existing find_camel function def find_match_groups(s: str) -> list:
    return [MatchGroup(*m.span()) for m in CAMEL_RE.finditer(s)]

def convert_camel_line(l: str) -> str:
    # find individual camelCase strings within `l`
    for g in find_match_groups(l):
        # replace with snake_case equivalent
        l = l[0 : g.start] + convert_camel_word(l[g.start : g.end]) + l[g.end :]
    return l

def convert_camel(lines: Iterable[str]) -> Generator[str, None, None]:
    return (convert_camel_line(l) for l in lines) 
```

将转换应用到文件包括备份原始文件，依次转换
每一行并写入新文件:

```
def transform_camel(file: Path):
    """
    Transform all occurences of camelCase strings in `file` to snake_case. The
    original file is renamed with a ".backup" extension to prevent data loss.
    """
    original = Path(str(file) + ".backup")
    file.rename(original)
    with original.open() as source:
        with file.open("w") as dest:
            dest.writelines(convert_camel(source) 
```

总结一下，现有的`main`入口点根据是否需要报告
或转换进行委托，并添加一个额外的`--convert`命令行参数。命令行参数解析现在由`argparse`执行，而不是手动检查`sys.argv` :

```
import argparse

def main(files: List[Path], convert=False):
    for f in files:
        if convert:
            convert_camel(f)
        else:
            report_camel(f, show_filenames=len(files) > 1)

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description=sys.modules[__name__].__doc__)
    parser.add_argument("files", nargs="+", help="source files to scan for camelCase")
    parser.add_argument(
        "--convert",
        action="store_true",
        dest="convert",
        help="perform camelCase to snake_case conversion",
    )
    args = parser.parse_args()
    main([Path(f) for f in args.files], convert=args.convert) 
```

CLI 帮助文本:

```
$ ./camel_finder.py -h
usage: camel_finder.py [-h] [--convert] files [files ...]

Scan source files for camelCase strings, reporting (grep style) on locations
or converting to snake_case. During conversion, orginal files are renamed with
a ".backup" extension. Yes I'm aware this can probably be achieved with a bash
one-liner.

positional arguments:
  files       source files to scan for camelCase

optional arguments:
  -h, --help  show this help message and exit
  --convert   perform camelCase to snake_case conversio 
```*