# 写写代码的代码

> 原文：<https://dev.to/mak12776/write-codes-that-writes-codes-568>

我们总是用手指写代码，我们总是在键盘上打字，我们一小时又一小时地工作来创造计算机程序。

但是我们写的东西可以被电脑写出来。目前的编程语言有`C`、`C++`、`Python`、`Java`，...在最后的编译步骤中将被翻译成机器字节码，这意味着**程序编写代码**。

但这不应该是结束。我们必须编写可以用其他编程语言编写代码的程序，如`C`、`C++`、`Python`、`java`、`ruby`或其他任何语言。

也许是一种新的编程语言，或者是一些改变代码结构的框架，或者是一个将文本翻译成其他东西的文本流处理器。

对于我提到的每一个主题，我都会给你一个简单的例子，但这并不是唯一的方法。

### 一种新的编程语言

在这段时间里，我们总是听说新的编程语言提供了一套功能并遵循一种设计哲学，但它们中的大多数总是复制其他编程语言的原则，并且它们不能用于日常用例。但是，如果我们忘记它们，并试图用我们的创造力从零开始创造一种新的编程语言，那将带给我们一整套新的想法来实现我们的目标。

例如，假设你想做一个简单的文本编辑器，为此目的的编程语言可以是这样的:

```
OpenWindow
{
    text_area: TextArea[editable, fullsize]
    KeyPress('CTRL+s') =>
    {
        path = AskPath('Save As', 'Save', 'Cancel)
        open(path, 'wt').write(text_area.text)
    }
    KeyPress('CTRL+o') =>
    {
        path = AskPath('Open', 'Open', 'Cancel)
        text_area.text = open(path).read()
    }
} 
```

或者你是一个艺术家，想画一些像素艺术:

```
use random, new_image

image = new_image([500, 500])
image.fill('Navy Blue')

repeat (40)
{
    image.draw_dot(
        random.position_range([0, 0], [image.width, 300]),
        random.color_range('bw:200', 'bw:256')
    )
}

image.draw_rectangle([0, 300], image.size, 'Dark Blue')

repeat(10)
{
    start = random.position_range([40, 200], [image.width - 40, 300])
    end = [start.x + random.choice(1, -1) * random.range(20, 35), 300]
    image.draw_rectangle(start, end, 'Dark Blue')
} 
```

你能猜到程序会画出什么吗？

或者像这样的一些编程语言:

```
set total_words to zero.
open "article.txt" then,
    for each line,
        add number of words in line to total_words.
    end.
end.
print total_words. 
```

### 元编程

> 如果你理解编译器是如何工作的，那么真正发生的事情与其说是 Lisp 有奇怪的语法，不如说是 Lisp 没有语法。您可以在解析树中编写程序，当解析其他语言时，这些程序会在编译器中生成。但是这些解析树完全可以被你的程序访问。你可以编写程序来操纵它们。在 Lisp 中，这些程序被称为宏。他们是写程序的程序。
> 
> **写程序的程序？**你什么时候想这么做？不经常，如果你用 Cobol 语言思考。如果你用 Lisp 语言思考。如果我能在这里给出一个强大的宏的例子，那就太方便了！怎么样？但是，如果我这样做了，对于不懂 Lisp 的人来说，这看起来就像是胡言乱语；这里没有空间来解释你需要知道的一切来理解它的意思。在 Ansi Common Lisp 中，我试图尽可能快地向前移动，尽管如此，我直到第 160 页才接触到宏。

来源链接:[https://stack overflow . com/questions/514644/到底什么是元编程](https://stackoverflow.com/questions/514644/what-exactly-is-metaprogramming)

或者一个可以编辑程序结构的框架，比如`python` :

```
from auto_code_gene import python as py
import sys

writer = py.PyWriter(sys.stdout)
node = \
py.Function('parse_argv', 'argv', [
    py.For('arg', 'argv', [
        py.If("len(argv) == 0", [
            py.Raise('ValueError', py.Str('empty argument')),
        ]),
        py.If("arg == '-'", [
            py.Pass()
        ])
    ])
])

node.write(writer) 
```

结果:

```
def parse_argv(argv):
    for arg in argv:
        if len(argv) == 0:
            raise ValueError('empty argument')
        if arg == '-':
            pass 
```

### 文本流处理器

一些可以翻译这段文字的程序:

```
INC stdlib.h
ST buffer:
    char *pntr, size_t start, size_t len, size_t size
    FN ${buffer}, allocate_buffer,
        pntr = malloc($size), start = 0, len = 0, size = $(size) 
```

到这个 C 文件:

```
#include <stdlib.h> 
struct buffer
{
    char *pntr;
    size_t start;
    size_t len;
    size_t size;
};

struct buffer *
allocate_buffer(size_t size)
{
    struct buffer *result;

    result = malloc(sizeof(struct buffer));
    if (result == NULL)
    {
        return NULL;
    }

    result->pntr = malloc(size);
    if (result->pntr == NULL)
    {
        free(result);
        return NULL;
    }

    result->start = 0;
    result->len = 0;
    result->size = size;

    return result;
} 
```

# 结论

**千万不要用手指写程序，要一直写做代码的程序。每当你想写东西的时候就这样做。因为计算机可以使这项工作自动化。**