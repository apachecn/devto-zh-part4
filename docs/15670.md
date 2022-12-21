# 用于数据流和分析的语言

> 原文：<https://dev.to/calebwin/a-language-for-data-flow-and-analysis-3fmj>

对于许多人来说，Python 是他们进行数据分析的首选语言。这在很大程度上是因为 Python 语言使得思考和编写操作数据单元的函数变得非常容易。例如，该函数确定某一年龄的人所属的号码段。

```
def age_segment(age):
    if age < 12: return 0
    elif age < 18: return 1
    elif age < 28: return 2
    elif age < 48: return 3
    elif age < 68: return 4
    elif age < 88: return 5
    else: return 6 
```

这是另一个函数。它接受一个年龄段号，并返回该人在法律上是否未成年。

```
def is_under_age(age_segment):
    if age_segment <= 1:
        return True
    return False 
```

这是最后一个。这个函数的作用是打印出一个年龄。这就是它的工作。

```
def print_age(age_segment):
    if age_segment == 0: print("0-12")
    elif age_segment == 1: print("12-18")
    elif age_segment == 2: print("18-18")
    elif age_segment == 3: print("28-48")
    elif age_segment == 4: print("48-68")
    elif age_segment == 5: print("68-88")
    elif age_segment == 6: print("88+") 
```

所有这些功能都是对“单个数据单元”进行操作的。但在数据分析中，一个非常常见的事情是拥有一堆“数据单元”,然后拥有一种功能“管道”,每个单元通过每个功能执行一个操作，逐步转换数据。

举个例子，假设我们有这样一个数据列表。你想要获取数据的每个元素，并通过这种函数的“管道”来传递它，你可以构建这种管道，首先将数据传递给`age_segment`，然后传递给`is_under_age`，最后传递给`print_age`。

```
ages = [9, 19, 37, 28, 48, 13] 
```

现在，您可能会意识到，您可以使用 Python 的 for 循环结构来循环这个问题，并按照列表中的年龄顺序调用函数。这也是很多人会做的。但是你会遇到一些问题。

*   您必须了解 Python 才能改变管道的结构(例如，您想要删除`age_segment`,因为您的数据已经是年龄段)
*   你将不得不编写(在我看来)杂乱的代码来使函数并行运行(Python 提供了`multiprocessing`库来使函数同时运行——而不是一个接一个地运行——并轻松地在它们之间传递数据；但是这引入了相当多的额外代码)

事实上，这就是它的样子。

```
from multiprocessing import Process, Queue
from ages_utils import age_segment as age_segment
from ages_utils import is_under_age as is_under_age
from ages_utils import ages as ages
from ages_utils import print_age as print_age
class PLPipeSentinel: pass
def run_ages(stream, out_queue):
    for data in stream:
        out_queue.put(data)
    out_queue.put(PLPipeSentinel())

def run_age_segment(in_queue, out_queue):
    while 1:
        inp = in_queue.get()
        if isinstance(inp, PLPipeSentinel):
            outp = PLPipeSentinel()
        if not isinstance(inp, PLPipeSentinel):
            outp = age_segment(inp)
        if out_queue is not None:
            out_queue.put(outp)
        if isinstance(inp, PLPipeSentinel):
            break

def run_is_under_age(in_queue, out_queue):
    while 1:
        inp = in_queue.get()
        if isinstance(inp, PLPipeSentinel):
            outp = PLPipeSentinel()
        if not isinstance(inp, PLPipeSentinel):
            result = is_under_age(inp)
            if result:
                outp = inp
            else:
                continue
        if out_queue is not None:
            out_queue.put(outp)
        if isinstance(inp, PLPipeSentinel):

            break
def run_print_age(in_queue, out_queue):
    while 1:
        inp = in_queue.get()
        if isinstance(inp, PLPipeSentinel):
            outp = PLPipeSentinel()
        if not isinstance(inp, PLPipeSentinel):
            outp = print_age(inp)
        if out_queue is not None:
            out_queue.put(outp)
        if isinstance(inp, PLPipeSentinel):
            break

if __name__ == "__main__":
    data = ages()
    in_age_segment = Queue()
    in_is_under_age = Queue()
    in_print_age = Queue()
    ages_process = Process(target=run_ages, args=(data, in_age_segment))
    age_segment_process = Process(target=run_age_segment, args=(in_age_segment,in_is_under_age,))
    is_under_age_process = Process(target=run_is_under_age, args=(in_is_under_age,in_print_age,))
    print_age_process = Process(target=run_print_age, args=(in_print_age,None,))
    ages_process.start()
    age_segment_process.start()
    is_under_age_process.start()
    print_age_process.start()
    age_segment_process.join()
    is_under_age_process.join()
    print_age_process.join() 
```

也许这对你来说是清晰可辨的，但对我来说肯定不是。

如果有一种简单的高级语言来描述管道的结构，并有一个编译器将语言代码编译成 Python，那会怎么样？这种语言是任何人都可以使用的语言——科学家，商业主管，...几乎任何人。

您将使用管道的组件，对组件的功能有基本的了解(例如- is_under_age 告诉我年龄是否被认为是法定的未成年)，但不需要了解它们是如何实现的。懂 Python 的人会编写组件的实现，但管道可以被编写、重写和重新构造，而不需要咨询懂 Python 的人，因为管道是用简单的高级语言编写的，它抽象了组成任何管道的功能。

这个高级语言是存在的！这叫做管道。使用 Pipelines 语言，上面的 63 行 Python 代码被简化为下面的 5 行描述。

```
from ages_utils import ages
from ages_utils import age_segment
from ages_utils import is_under_age
from ages_utils import print_age

ages |> age_segment /> is_under_age |> print_age 
```

`|>`表示传递给`age_segment`和`print_age`的每个元素都通过传递给`age_segment()`和`print_age()`函数进行转换，然后将结果传递给管道中的下一个函数(如果存在的话)。

`\>`表示传入`is_under_age`的每个元素都被过滤。该元素被传入`is_under_age`，如果结果是`True`，它就被传递下去。如果结果是`False`，它不会被传递。

这种语言不仅仅是管道、过滤器和为更复杂的数据流设计的特性，一切都可以在 GitHub 知识库的“README”中找到。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[管道](https://github.com/calebwin/pipelines)

### Pipelines 是一种高级数据分析语言。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/3ba431a99a6e27734d97d549c0f16122.png)](https://camo.githubusercontent.com/c0cf42e4c8a3f9a9112789a6d711b183aa27d2f5/68747470733a2f2f692e696d6775722e636f6d2f72627832486c682e706e67)

Pipelines 是一种用于制作大规模并行管道的语言和运行时。与定义数据流的其他语言不同，管道语言要求在 Python 脚本语言中单独定义组件的实现。这允许实现的细节从流水线的结构中分离出来，同时提供对成千上万个用于机器学习、数据分析和处理的活动库的访问。跳到[入门](https://github.com/calebwin/pipelines#some-next-steps)安装管道编译器。

### 一个例子

作为一个介绍性的例子，偶数上的 Fizz Buzz 的一个简单管道可以写成如下形式

```
from fizzbuzz import numbers
from fizzbuzz import even
from fizzbuzz import fizzbuzz
from fizzbuzz import printer
numbers
/> even 
|> fizzbuzz where (number=*, fizz="Fizz", buzz="Buzz")
|> printer
```

同时，组件的实现将会用 Python 编写

```
def numbers()
    for
```

…</article>

[View on GitHub](https://github.com/calebwin/pipelines)

如果你决定在你自己的数据分析工作中使用，请让我知道-我很想看看你做什么！如果没有，如果你能在以后启动 GitHub 库，我会很感激，如果你觉得这很有趣并且可能有用的话。