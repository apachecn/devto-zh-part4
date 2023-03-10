# 死简单 Python:生成器和协程

> 原文：<https://dev.to/codemouse92/dead-simple-python-generators-and-coroutines-21ll>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

编程通常是关于等待的。等待函数，等待输入，等待计算，等待测试通过...

...等着 Jason 写另一个*死简单 Python* 已经。

如果你的程序等你一次不是很好吗？这正是生成器和协程所做的！在过去的三篇文章中，我们一直在为此做准备，但是我很高兴地宣布，等待结束了。

如果你还没有读过[循环和迭代器](https://dev.to/codemouse92/dead-simple-python-loops-and-iterators-15bp)、[迭代器工具](https://dev.to/codemouse92/dead-simple-python-iteration-power-tools-3i3n)和[列表理解和生成器表达式](https://dev.to/codemouse92/dead-simple-python-list-comprehensions-and-generator-expressions-2mb5)，你应该先浏览一下。

对于其他人，让我们开始吧。

# 迎接发电机

如何生成任意长度的斐波那契数列？显然，您需要跟踪一些数据，并且需要以某种方式操作这些数据来创建下一个元素。

您的第一反应可能是创建一个可迭代的类，这不是一个坏主意。让我们从这里开始，使用我们在前面章节中已经介绍过的内容:

```
class Fibonacci:

    def __init__(self, limit):
        self.n1 = 0
        self.n2 = 1
        self.n = 1
        self.i = 1
        self.limit = limit

    def __iter__(self):
        return self

    def __next__(self):
        if self.i > self.limit:
            raise StopIteration

        if self.i > 1:
            self.n = self.n1 + self.n2
            self.n1, self.n2 = self.n2, self.n

        self.i += 1
        return self.n

fib = Fibonacci(10)
for i in fib:
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

存储得更紧凑，而且如果你一直在关注这个系列，可能不会有什么惊喜。然而，对于像序列这样简单的东西来说，这种方法可能有点力不从心。当然有很多样板文件。

这种情况正是发电机的用途。

```
def fibonacci(limit):
    if limit >= 1:
        yield (n2 := 1)

    n1 = 0

    for _ in range(1, limit):
        yield (n := n1 + n2)
        n1, n2 = n2, n

for i in fibonacci(10):
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

生成器肯定更紧凑——只有 9 行，而该类有 22 行——但是可读性一样好。

秘制酱就是`yield`关键字，不退出函数就返回值。`yield`在功能上与我们类中的`__next__()`函数相同。生成器将运行到(包括)它的`yield`语句，然后在它做任何事情之前等待另一个`__next__()`调用。一旦它*收到那个调用，它将继续运行，直到它遇到另一个`yield`。*

> **注意:**那个看起来很奇怪的`:=`是 Python 3.8 中新的“海象运算符”，它赋值并返回值。如果您使用的是 Python 3.7 或更早版本，您可以将这些语句分成两行(单独的赋值和`yield`语句)。

您还会注意到缺少一个`raise StopIteration`语句。发电机不需要它们；事实上，自从 [PEP 479](https://www.python.org/dev/peps/pep-0479/) 以来，他们甚至不允许他们。当生成器函数终止时，无论是自然终止还是通过`return`语句终止，`StopIteration`都会在后台自动产生。

## 发电机并试

**修订日期:2019 年 11 月 29 日**

过去,`yield`不能出现在`try-finally`语句的`try`子句中。定义了生成器语法的 PEP 255 解释了原因:

> 困难在于不能保证生成器会被恢复，因此不能保证 finally 块会被执行；这严重违背了 finally 的宗旨，令人难以忍受。

这在 PEP 342 [PEP 342](https://www.python.org/dev/peps/pep-0342/) 中有所改动，在 Python 2.5 中定型。

那么，为什么要讨论这样一个古老的变革呢？简单:到今天为止，我的印象是`yield`不可能出现在`try-finally`里。一些关于这个话题的文章错误地引用了旧规则。

## 以发电机为对象

您可能还记得 Python 将函数视为对象，生成器也不例外！基于前面的例子，我们可以保存一个特定的生成器实例。

例如，如果我只想打印出斐波那契数列的第 10-20 个值呢？

首先，我将把生成器保存在一个变量中，这样我就可以重用它。这个限制对我来说没什么大不了的，所以我会用大一点的。使用我的循环范围来确定我要显示的内容会更容易，因为这使限制逻辑接近打印语句。

```
fib = fibonacci(100) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我将使用一个循环来跳过前 10 个元素。

```
for _ in range(10):
    next(fib) 
```

Enter fullscreen mode Exit fullscreen mode

`next()`函数实际上是循环用来遍历可迭代对象的。在生成器的情况下，它返回由`yield`返回的任何值。在这种情况下，由于我们还不关心这些价值，我们只是把它们扔掉(对它们什么也不做)。

顺便说一下，我也可以调用`fib.__next__()`——反正这是`next(fib)`对*的调用*——但是我更喜欢我所采用的方法的简洁外观。通常归结为偏好；两者同样有效。

我现在准备从生成器中访问一些值，但不是所有的值*。因此，我仍将使用一个`range()`，并用`next()`直接从生成器中检索值。* 

```
for n in range(10, 21):
    print(f"{n}th value: {next(fib)}") 
```

Enter fullscreen mode Exit fullscreen mode

这很好地打印出了所需的值:

```
10th value: 89
11th value: 144
12th value: 233
13th value: 377
14th value: 610
15th value: 987
16th value: 1597
17th value: 2584
18th value: 4181
19th value: 6765
20th value: 10946 
```

Enter fullscreen mode Exit fullscreen mode

你会记得我们早些时候把限额定为 100。我们现在已经完成了发电机的工作，但我们真的不应该就这样走开，让它等待另一个`next()`电话！让它在我们程序的其余部分闲置在内存中会浪费资源(无论多么少)。

相反，我们可以手动告诉生成器我们已经完成了。

```
fib.close() 
```

Enter fullscreen mode Exit fullscreen mode

这将手动关闭发电机，就好像它已经到达了一个`return`语句。现在垃圾收集器可以清理它了。

# 迎接协程

生成器允许我们快速定义一个 iterable，在调用之间存储它的状态。然而，如果我们想要相反的结果:在中传递信息*,并让函数耐心地等待，直到它得到它？Python 为此提供了**协程**。*

对于任何已经有点熟悉协程的人来说，您应该理解我所指的是被明确称为**简单协程**(尽管我只是为了读者的理智才通篇说“协程”)。)如果你看过任何使用并发的 Python 代码，你可能已经遇到过它的近亲，**本机协同程序**(也称为“异步协同程序”)。

现在，请理解简单协程和本地协程都被官方认为是“协程”，并且它们共享许多原则；本机协程建立在简单协程引入的概念之上。当我们在后面的文章中讨论`async`时，我们将回到这个问题。

现在再次假设当我说“协程”时，我指的是一个简单的协程。

想象一下，你想找出一串字符串之间所有常见的字母，比如查尔斯·狄更斯书中那些有趣的角色名字。你不知道有多少个字符串，它们会在运行时被输入，而且不一定是一次输入。

显然，这种方法必须:

*   可重复使用。
*   陈述到目前为止共有的字母。)
*   本质上是迭代的，因为我们不知道会得到多少个字符串。

典型的函数对于这种情况并不理想，因为我们必须一次将所有的数据作为一个列表或元组来传递，而且它们本身并不存储状态。同时，除非第一次被调用，否则生成器不能处理输入。

我们可以尝试一门课，尽管那是一大堆样板。无论如何，让我们从那里开始，只是为了更好地理解我们正在处理的问题。

在我的第一个版本中，我将改变传递给类的列表，这样我可以随时查看结果。如果我坚持使用一个类，我可能不会这样做，但是对于我们的目的来说，它是最小的可行类。此外，它在功能上与我们将很快编写的协程相同，这对于比较方法很有用。

```
class CommonLetterCounter:

    def __init__(self, results):
        self.letters = {}
        self.counted = []
        self.results = results
        self.i = 0

    def add_word(self, word):
        word = word.lower()
        for c in word:
            if c.isalpha():
                if c not in self.letters:
                    self.letters[c] = 0
                self.letters[c] += 1

        self.counted = sorted(self.letters.items(), key=lambda kv: kv[1])
        self.counted = self.counted[::-1]

        self.results.clear()
        for item in self.counted:
            self.results.append(item)

names = ['Skimpole', 'Sloppy', 'Wopsle', 'Toodle', 'Squeers',
         'Honeythunder', 'Tulkinghorn', 'Bumble', 'Wegg',
         'Swiveller', 'Sweedlepipe', 'Jellyby', 'Smike', 'Heep',
         'Sowerberry', 'Pumblechook', 'Podsnap', 'Tox', 'Wackles',
         'Scrooge', 'Snodgrass', 'Winkle', 'Pickwick']

results = []
counter = CommonLetterCounter(results)

for name in names:
    counter.add_word(name)

for letter, count in results:
    print(f'{letter} apppears {count} times.') 
```

Enter fullscreen mode Exit fullscreen mode

根据我的输出，查尔斯·狄更斯特别喜欢有 e，o，s，l，p 的名字，谁知道呢？

我们可以用一个**协程**来完成同样的结果。

```
def count_common_letters(results):
    letters = {}

    while True:
        word = yield
        word = word.lower()
        for c in word:
            if c.isalpha():
                if c not in letters:
                    letters[c] = 0
                letters[c] += 1

        counted = sorted(letters.items(), key=lambda kv: kv[1])
        counted = counted[::-1]

        results.clear()
        for item in counted:
            results.append(item)

names = ['Skimpole', 'Sloppy', 'Wopsle', 'Toodle', 'Squeers',
         'Honeythunder', 'Tulkinghorn', 'Bumble', 'Wegg',
         'Swiveller', 'Sweedlepipe', 'Jellyby', 'Smike', 'Heep',
         'Sowerberry', 'Pumblechook', 'Podsnap', 'Tox', 'Wackles',
         'Scrooge', 'Snodgrass', 'Winkle', 'Pickwick']

results = []
counter = count_common_letters(results)
counter.send(None)  # prime the coroutine 
for name in names:
    counter.send(name)  # send data to the coroutine 
counter.close()  # manually end the coroutine 
for letter, count in results:
    print(f'{letter} apppears {count} times.') 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细看看这里发生了什么。乍一看，协程与函数没有什么不同，但是对于生成器来说，`yield`关键字的使用就完全不同了。

然而，在协程中，`yield`代表“等到得到输入，然后就在这里使用它”。

您会注意到，这两种方法的大多数处理逻辑是相同的；我们只是取消了课堂样板。我们像存储对象一样存储协程的实例，只是为了确保我们每次向它发送更多数据时都使用同一个实例。

类和协程的主要区别在于用法。我们使用它的`send()`函数:
向协程发送数据

```
for name in names:
    counter.send(name) 
```

Enter fullscreen mode Exit fullscreen mode

然而，在我们这样做之前，我们必须首先通过调用`counter.send(None)`(如上所述)或`counter.__next__()`来启动协程。协程不能立即接收值；它必须首先运行从*到*到第一个`yield`的所有代码。

与生成器一样，当协程到达其正常执行流程的末尾，或者当它遇到一个`return`语句时，它就完成了。因为在我们的例子中这两件事都不可能发生，所以我手动关闭协程:

```
counter.close() 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，使用协程:

*   将它的一个实例保存为变量，例如，`counter`，
*   用`counter.send(None)`、`counter.__next__()`或`next(counter)`做好准备，
*   用`counter.send()`发送数据给它，
*   如有必要，用`counter.close()`关闭。

## 协程并试

还记得关于生成器和不要在`try-finally`语句的`try`子句中放置`yield`的规则吗？*这里不适用！*因为`yield`在协程中的行为非常不同(处理传入数据，而不是传出数据)，所以以这种方式使用它是完全可以接受的。

# 扔()

生成器和协同程序也有一个`throw()`函数，用于在它们暂停的地方引发异常。从[“错误”文章](https://dev.to/codemouse92/dead-simple-python-errors-l82)中你会记得异常可以被用作执行流程的正常部分。

例如，假设您想要向远程服务器发送数据。您已经有了方便的小连接对象，并使用协程通过该连接发送数据。

在代码的其他地方，您发现已经失去了网络连接，但是由于您与服务器的通信方式，协程如此努力发送的所有数据都将毫无怨言地落入黑洞。哎呀。

考虑一下我编写的这个示例代码。(假设实际的连接逻辑本身不适合处理回退或报告连接错误。)

```
class Connection:
    """ Stub object simulating connection to a server """

    def __init__(self, addr):
        self.addr = addr

    def transmit(self, data):
        print(f"X: {data[0]}, Y: {data[1]} sent to {self.addr}")

def send_to_server(conn):
    """ Coroutine demonstrating sending data """
    while True:
        raw_data = yield
        raw_data = raw_data.split(' ')
        coords = (float(raw_data[0]), float(raw_data[1]))
        conn.transmit(coords)

conn = Connection("example.com")

sender = send_to_server(conn)
sender.send(None)

for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}")

# Simulate connection error... conn.addr = None
# ...but assume the sender knows nothing about it. 
for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}") 
```

Enter fullscreen mode Exit fullscreen mode

运行这个例子，我们看到前五个`send()`呼叫进入`example.com`，但是最后五个呼叫进入`None`。这显然不行——我们想报告问题，并开始将数据发送到一个文件中，这样数据就不会永远丢失。

这就是`throw()`的用武之地。一旦我们知道我们失去了连接，我们就可以提醒协程注意这个事实，允许它做出适当的响应。

我们首先将一个`try-except`添加到我们的协程:

```
def send_to_server(conn):
    while True:
        try:
            raw_data = yield
            raw_data = raw_data.split(' ')
            coords = (float(raw_data[0]), float(raw_data[1]))
            conn.transmit(coords)
        except ConnectionError:
            print("Oops! Connection lost. Creating fallback.")
            # Create a fallback connection!
            conn = Connection("local file") 
```

Enter fullscreen mode Exit fullscreen mode

我们的使用示例只需要一个变化:一旦我们知道我们失去了连接，我们就使用`sender.throw(ConnectionError)` :

```
conn = Connection("example.com")

sender = send_to_server(conn)
sender.send(None)

for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}")

# Simulate connection error... conn.addr = None
# ...but assume the sender knows nothing about it. 
sender.throw(ConnectionError) # ALERT THE SENDER! 
for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}") 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！现在，只要协程发出警报，我们就会收到关于连接问题的消息，其余的消息被路由到我们的本地文件。

# 屈服于

当使用生成器或协程时，您不仅仅局限于本地`yield`。事实上，您可以使用`yield from`获得其他可迭代程序、生成器或协程。

例如，假设我想重写我的斐波那契数列到没有限制，我只想硬编码前五个值来开始。

```
def fibonacci():
    starter = [1, 1, 2, 3, 5]
    yield from starter

    n1 = starter[-2]
    n2 = starter[-1]

    while True:
        yield (n := n1 + n2)
        n1, n2 = n2, n 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`yield from`暂时移交给另一个 iterable，无论它是一个容器、一个对象还是另一个生成器。一旦这个迭代到达终点，*这个*发生器就会像平常一样继续运行。

仅仅使用这个生成器，你不会知道它在部分时间使用了另一个迭代器。它和往常一样工作。

```
fib = fibonacci()

for n in range(1,11):
    print(f"{n}th value: {next(fib)}")

fib.close() 
```

Enter fullscreen mode Exit fullscreen mode

协程也可以以类似的方式移交。例如，在我们的连接示例中，如果我们创建第二个协程来处理向文件写入数据，会怎么样？如果我们有一个连接错误，我们可以切换到在后台使用。

```
class Connection:
    """ Stub object simulating connection to a server """

    def __init__(self, addr):
        self.addr = addr

    def transmit(self, data):
        print(f"X: {data[0]}, Y: {data[1]} sent to {self.addr}")

def save_to_file():
    while True:
        raw_data = yield
        raw_data = raw_data.split(' ')
        coords = (float(raw_data[0]), float(raw_data[1]))
        print(f"X: {coords[0]}, Y: {coords[1]} sent to local file")

def send_to_server(conn):
    while True:
        if conn is None:
            yield from save_to_file()
        else:
            try:
                raw_data = yield
                raw_data = raw_data.split(' ')
                coords = (float(raw_data[0]), float(raw_data[1]))
                conn.transmit(coords)
            except ConnectionError:
                print("Oops! Connection lost. Using fallback.")
                conn = None

conn = Connection("example.com")

sender = send_to_server(conn)
sender.send(None)

for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}")

# Simulate connection error... conn.addr = None
# ...but assume the sender knows nothing about it. 
sender.throw(ConnectionError) # ALERT THE SENDER! 
for i in range(1, 6):
    sender.send(f"{100/i}  {200/i}") 
```

Enter fullscreen mode Exit fullscreen mode

这种行为在 [PEP 380](https://www.python.org/dev/peps/pep-0380/) 中有定义，所以请阅读以了解更多信息。

# 结合生成器和协程

您可能想知道:“我可以像从生成器那样直接从协程中组合两个返回数据吗？”

我在写这篇文章的时候也对此很好奇，显然你*可以*。这一切都与识别函数何时被当作生成器而不是协程有关。

这个问题的关键很简单:`__next__()`和`send(None)`对协程来说实际上是同一件事。

```
def count_common_letters():
    letters = {}

    word = yield
    while word is not None:
        word = word.lower()
        for c in word:
            if c.isalpha():
                if c not in letters:
                    letters[c] = 0
                letters[c] += 1
        word = yield

    counted = sorted(letters.items(), key=lambda kv: kv[1])
    counted = counted[::-1]

    for item in counted:
        yield item

names = ['Skimpole', 'Sloppy', 'Wopsle', 'Toodle', 'Squeers',
         'Honeythunder', 'Tulkinghorn', 'Bumble', 'Wegg',
         'Swiveller', 'Sweedlepipe', 'Jellyby', 'Smike', 'Heep',
         'Sowerberry', 'Pumblechook', 'Podsnap', 'Tox', 'Wackles',
         'Scrooge', 'Snodgrass', 'Winkle', 'Pickwick']

counter = count_common_letters()
counter.send(None)

for name in names:
    counter.send(name)

for letter, count in counter:
    print(f'{letter} apppears {count} times.') 
```

Enter fullscreen mode Exit fullscreen mode

我只需要观察协程何时开始接收`None`(当然是在初始启动之后)。由于我将`yield`的结果存储在`word`中，一旦`word`为`None`，我就可以跳出*接收*信息的循环。

当我们从使用协程作为协程切换到使用它作为生成器时，它需要在开始用`yield`输出数据之前处理单个`send(None)`。([这个 StackOverflow 问题](https://stackoverflow.com/a/53205431/472647)展示了那个现象。)在*调用*我们的协程时，我们在转换用法之前从未明确地`send(None)`；Python 在后台完成这项工作。

另外，记住协程/生成器仍然是一个函数。它只是在每次遇到`yield`时暂停一下。在我的例子中，我不能突然回到使用`counter`作为协程，因为没有执行流将我带回`word = yield`。完全有可能*编写它，这样你就可以来回切换，尽管如果以牺牲可读性或变得过于复杂为代价，这可能是不可取的。*

 *# 回顾

生成器和协程允许您快速编写“等待”您的函数。稍后，我们将见到*本机协程*，一种用于并发的协程。

让我们回顾一下本节的要点:

*   **生成器**是等待你请求输出的迭代程序。
*   生成器被写成普通的函数，除了它们使用`yield`关键字返回值，就像一个类使用它的`__next__()`函数一样。
*   当生成器到达其执行顺序的自然结尾，或者命中一个`return`语句时，它引发`StopIteration`并结束。
*   **协同程序**类似于生成器，除了它们等待信息被*通过`foo.send()`函数发送给*。
*   生成器和协程都可以通过`next(foo)`或`foo.__next__()`前进到下一个产出语句。
*   在用`foo.send()`向协程发送任何东西之前，它必须用`foo.send(None)`、`next(foo)`或`foo.__next__()`进行“预处理”。
*   使用`foo.throw()`可以在当前`yield`引发异常。
*   可以用' foo.close()手动停止生成器或协程。
*   单个函数可以首先表现得像一个协程，然后像一个生成器。

和往常一样，您可以从文档中了解更多信息:

*   [Python 教程:类-生成器](https://docs.python.org/3/tutorial/classes.html#generators)
*   PEP 255:简单的发电机
*   [PEP 479:更改生成器内部的停止迭代处理](https://www.python.org/dev/peps/pep-0479/)
*   [PEP 342:通过增强生成器的协同程序](https://www.python.org/dev/peps/pep-0342/)
*   [PEP 380:委托给子发电机的语法](https://www.python.org/dev/peps/pep-0380/)

* * *

*感谢`deniska` (Freenode IRC `#python`)、[@韵脚](https://dev.to/rhymes)、 [@florimondmanca](https://dev.to/florimondmanca) (DEV.to)提供的修改建议。**