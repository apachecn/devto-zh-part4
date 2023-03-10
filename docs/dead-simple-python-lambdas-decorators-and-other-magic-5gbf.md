# 死亡的简单 Python: Lambdas、Decorators 和其他魔法

> 原文：<https://dev.to/codemouse92/dead-simple-python-lambdas-decorators-and-other-magic-5gbf>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

Python 以看起来像魔术而闻名，这可能部分是由于函数可以采用多种形式:lambdas、decorators、closures 等等。一个恰当的函数调用可以做令人惊奇的事情，甚至不用编写一个类！

你可能会说函数很神奇。

# 功能再探

我们已经在[数据类型和不变性](https://dev.to/codemouse92/dead-simple-python-data-typing-and-immutability-41dm)中提到了函数。如果你还没有读过那篇文章，我建议你现在就回去看看。

让我们看一个函数的简单例子，只是为了确保我们在同一页上。

```
def cheer(volume=None):
    if volume is None:
        print("yay.")
    elif volume == "Louder!":
        print("yay!")
    elif volume == "LOUDER!":
        print("*deep breath*")
        print("yay!")

cheer()  # prints "yay." cheer("Louder!")  # prints "yay!" cheer("LOUDER!")  # prints "*deep breath* ...yay!" 
```

Enter fullscreen mode Exit fullscreen mode

这没什么奇怪的。函数`cheer()`接受单个参数`volume`。如果我们不为`volume`传递一个参数，它将默认为值`None`。

# 什么是函数式编程？

我们这些来自面向对象编程语言的人已经学会从类和对象的角度考虑一切。数据与负责访问和修改数据的函数一起被组织到对象中。有时这很有效，但其他时候，课程就开始感觉像太多的样板文件。

**函数式编程**几乎与此相反。我们围绕*功能*进行组织，我们通过这些功能传递数据。我们必须遵守几条规则:

*   函数应该只接受输入，只产生输出。

*   功能不应该有副作用；他们不应该修改自己外部的任何东西。

*   功能应该(理想地)总是为相同的输入产生相同的输出。函数内部不应该有会破坏这种模式的状态。

现在，在你重写你所有的 Python 代码成为纯函数之前， ***停止！*** 不要忘记 Python 的一个美妙之处在于它是一种*多范例语言*。你不必选择一种范式并坚持下去；您可以在您的代码中混合和匹配这两者的优点。

事实上，我们已经这样做了！迭代器和生成器都是从函数式编程中借鉴来的，它们与对象一起工作得很好。也可以随意加入 lambdas、decorators 和 closures。关键在于为工作选择最好的工具。

实际上，我们很少能一起避免副作用。在将函数式编程的概念应用于 Python 代码时，您应该更多地关注副作用，而不仅仅是完全避免它们。把它们限制在没有更好的方法解决问题的情况下。这里没有严格的规则可以依赖；你需要培养自己的洞察力。

# 递归

当一个函数调用自己时，这被称为**递归。当我们需要重复一个函数的整个逻辑，但是一个循环是不合适的(或者感觉太混乱)时，这是很有帮助的。**

**注意:**我下面的例子是为了突出 recusion 本身而简化的。这实际上不是递归是最佳方法的情况；当您需要在不同的数据上重复调用复杂的语言时，例如当您遍历一个树结构时，recusion 更好。

```
import random
random.seed()

class Villain:

    def __init__(self):
        self.defeated = False

    def confront(self):
        # Roll of the dice.
        if random.randint(0,10) == 10:
            self.defeated = True

def defeat(villain):
    villain.confront()
    if villain.defeated:
        print("Yay!")
        return True
    else:
        print("Keep trying...")
        return defeat(villain)

starlight = Villain()
victory = defeat(starlight)

if victory:
    print("YAY!") 
```

Enter fullscreen mode Exit fullscreen mode

与`random`相关的东西可能看起来很新。这实际上与这个主题无关，但简单地说，我们可以通过首先在程序开始时植入随机数生成器(`random.seed()`)来生成随机整数，然后调用`random.randint(min, max)`，其中`min`和`max`定义了可能值的包含范围。

这里逻辑的重要部分是`defeat()`函数。只要小人没被打败，函数就调用自己，传递`villain`变量。这种情况会一直发生，直到其中一个函数调用返回值。在这种情况下，值沿着递归调用堆栈返回，最终存储在`victory`中。

不管花多长时间，我们最终都会打败那个恶棍。

## 当心无限递归

递归可能是一个强大的工具，但它也可能带来一个问题:*如果我们没有办法停止怎么办？*

```
def mirror_pool(lookers):
    reflections = []
    for looker in lookers:
        reflections.append(looker)
    lookers.append(reflections)

    print(f"We have {len(lookers) - 1} duplicates.")

    return mirror_pool(lookers)

duplicates = mirror_pool(["Pinkie Pie"]) 
```

Enter fullscreen mode Exit fullscreen mode

显然，这将永远持续下去！一些语言没有提供一个干净的方法来处理这个问题——函数会无限递归，直到某个东西崩溃。

Python 稍微优雅地停止了这种疯狂。一旦达到设定的递归深度(通常是 997-1000 次)，它就停止整个程序并引发一个错误:

> RecursionError:调用 Python 对象时超出了最大递归深度

像所有的错误一样，我们可以在事情失去控制之前发现这一点

```
try:
    duplicates = mirror_pool(["Pinkie Pie"])
except RecursionError:
    print("Time to watch paint dry.") 
```

Enter fullscreen mode Exit fullscreen mode

令人欣慰的是，由于我编写这段代码的方式，我实际上不需要做任何特殊的事情来清理 997 个副本。递归函数从不返回，所以在这种情况下`duplicates`保持未定义。

然而，我们可能想用另一种方式控制递归，这样我们就不必使用`try-except`来防止灾难。在我们的递归函数中，我们可以通过添加一个`calls`参数来跟踪它被调用了多少次，一旦它变得太大就中止。

```
def mirror_pool(lookers, calls=0):
    calls += 1

    reflections = []
    for looker in lookers:
        reflections.append(looker)
    lookers.append(reflections)

    print(f"We have {len(lookers) - 1} duplicates.")

    if calls < 20:
        lookers = mirror_pool(lookers, calls)

    return lookers

duplicates = mirror_pool(["Pinkie Pie"])
print(f"Grand total: {len(duplicates)} Pinkie Pies!") 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要弄清楚如何在不丢失原件的情况下删除 20 个副本，但至少程序没有崩溃。

**注意:**你可以用`sys.setrecursionlimit(n)`覆盖最大递归层次，这里`n`是你想要的最大值。

# 嵌套函数

有时，我们可能希望在函数中重用一段逻辑，但是我们不希望通过创建另一个函数来搞乱我们的代码。

```
def use_elements(target):
    elements = ["Honesty", "Kindness", "Laughter",
                "Generosity", "Loyalty", "Magic"]

    def use(element, target):
        print(f"Using Element of {element} on {target}.")

    for element in elements:
        use(element, target)

use_elements("Nightmare Moon") 
```

Enter fullscreen mode Exit fullscreen mode

当然，这个简单例子的问题在于*的有用性*并不是立即就能看出来的。当我们有一大块逻辑想抽象成一个函数以便重用，但又不想在主函数之外定义时，嵌套函数就变得很有用。如果`use()`函数相当复杂，并且可能不仅仅是从循环中调用，这种设计将是合理的。

尽管如此，这个简单的例子显示了基本的概念。这也带来了另一个困难。你会注意到，每次我们调用内部函数`use()`时，我们都会将`target`传递给它，这感觉很没意义。我们不能只使用已经在局部范围内的`target`变量吗？

事实上，我们可以。

```
def use_elements(target):
    elements = ["Honesty", "Kindness", "Laughter",
                "Generosity", "Loyalty", "Magic"]

    def use(element):
        print(f"Using Element of {element} on {target}.")

    for element in elements:
        use(element)

use_elements("Nightmare Moon") 
```

Enter fullscreen mode Exit fullscreen mode

然而，一旦我们试图修改变量，我们就会遇到麻烦:

```
def use_elements(target):
    elements = ["Honesty", "Kindness", "Laughter",
                "Generosity", "Loyalty", "Magic"]

    def use(element):
        print(f"Using Element of {element} on {target}.")
        target = "Luna"

    for element in elements:
        use(element)

    print(target)

use_elements("Nightmare Moon") 
```

Enter fullscreen mode Exit fullscreen mode

运行该代码会引发一个错误:

> UnboundLocalError:在赋值前引用了局部变量“target”

显然，它不再看到我们的局部变量`target`。这是因为默认情况下，*为名称赋值会隐藏*封闭作用域中的任何现有名称。所以，`target == "Luna"`行试图创建一个新的变量，限制在`use()`的范围内，并且在`use_elements()`的封闭范围内隐藏(隐藏)变量`target`。Python 看到了这一点，并假设，因为我们在函数`use()`中定义了`target`，所以对该变量的所有引用都与该本地名称相关。这不是我们想要的！

关键字`nonlocal`允许我们告诉内部函数，我们正在使用来自*的变量`target`来封闭局部作用域*。

```
def use_elements(target):
    elements = ["Honesty", "Kindness", "Laughter",
                "Generosity", "Loyalty", "Magic"]

    def use(element):
        nonlocal target
        print(f"Using Element of {element} on {target}.")
        target = "Luna"

    for element in elements:
        use(element)

    print(target)

use_elements("Nightmare Moon") 
```

Enter fullscreen mode Exit fullscreen mode

现在，该说的都说了，该做的都做了，我们看到值`Luna`被打印出来。我们在这里的工作完成了！

**注意:**如果你想让一个函数能够修改定义在*全局*作用域(在所有函数之外)的变量，使用`global`关键字代替`nonlocal`。

# 关闭

基于嵌套函数的思想，并回忆起一个函数与任何其他对象没有什么不同，我们可以创建一个实际构建并返回另一个函数的函数，称为**闭包**。

```
def harvester(pony):
    total_trees = 0

    def applebucking(trees):
        nonlocal pony, total_trees
        total_trees += trees
        print(f"{pony} harvested from {total_trees} trees so far.")

    return applebucking

apple_jack = harvester("Apple Jack")
big_mac = harvester("Big Macintosh")
apple_bloom = harvester("Apple Bloom")

north_orchard = 120
west_orchard = 80  # watch out for fruit bats east_orchard = 135
south_orchard = 95
near_house = 20

apple_jack(west_orchard)
big_mac(east_orchard)
apple_bloom(near_house)
big_mac(north_orchard)
apple_jack(south_orchard) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`applebucking()`是闭包，因为它*封闭了非局部变量`pony`和`total_trees`的*。即使在外部函数终止后，闭包仍然保留对这些变量的引用。

闭包从`harvester()`函数返回，可以像任何其他对象一样存储在变量中。事实上，它“封闭”了一个非局部变量，使得它本身成为一个*封闭*；否则，它只是一个函数。

在这个例子中，我使用闭包来有效地创建具有*状态*的对象。换句话说，每个采集者都记得他或她采集了多少棵树。这种特殊的用法并不完全符合函数式编程，但是如果你不想创建一个完整的类来存储一个函数的状态，那么这种用法非常有用！

`apple_jack`、`big_macintosh`、`apple_bloom`现在是三种不同的功能，各有自己独立的状态；他们每个人都有一个不同的名字，并记得他们收获了多少棵树。在一个闭包的状态中发生的事情对其他的没有影响。

当我们运行代码时，我们看到这个状态在起作用:

```
Apple Jack harvested from 80 trees so far.
Big Macintosh harvested from 135 trees so far.
Apple Bloom harvested from 20 trees so far.
Big Macintosh harvested from 255 trees so far.
Apple Jack harvested from 175 trees so far. 
```

Enter fullscreen mode Exit fullscreen mode

像苹果派一样容易。

## 闭包的问题

闭包本质上是“隐式类”，因为它们将功能及其持久信息(状态)放在同一个对象中。然而，闭包有几个独特的缺点:

*   可以说，您无法访问“成员变量”。在我们的例子中，我永远也到不了`apple_jack`闭包上的`total_trees`变量！我只能在闭包自己的代码的上下文中使用这个变量。

*   关闭的状态完全不透明。除非您知道闭包是如何编写的，否则您不知道它在跟踪什么信息。

*   因为前面两点，根本不可能直接知道一个闭包什么时候有任何状态*。*

 *当使用闭包时，您需要准备好处理这些问题，以及它们带来的所有调试困难。我建议*只在需要一个函数来存储调用之间的少量私有状态时使用*，并且只在代码中的有限时间内使用，以至于编写整个类感觉不合理。(此外，不要忘记生成器和协程，它们可能更适合许多这样的场景。)

基本上就是*那里*。闭包仍然是 Python repitoire 的有用部分，只要你非常小心地使用它们。

# Lambdas

一个 **lambda** 是一个由单个表达式组成的匿名函数(没有名字)。

这个定义本身就是许多程序员无法想象他们为什么需要它的原因。写一个缺少名字的函数，基本上使重用完全不切实际，这有什么意义？当然，你可以*给一个变量赋值一个λ，但是在那种情况下，你不应该写一个函数吗？*

为了理解这一点，我们先来看一个没有 lambdas 的例子*:* 

```
class Element:

    def __init__(self, element, color, pony):
        self.element = element
        self.color = color
        self.pony = pony

    def __repr__(self):
        return f"Element of {self.element} ({self.color}) is attuned to {self.pony}"

elements = [
    Element("Honesty", "Orange", "Apple Jack"),
    Element("Kindness", "Pink", "Fluttershy"),
    Element("Laughter", "Blue", "Pinkie Pie"),
    Element("Generosity", "Violet", "Rarity"),
    Element("Loyalty", "Red", "Rainbow Dash"),
    Element("Magic", "Purple", "Twilight Sparkle")
]

def sort_by_color(element):
    return element.color

elements = sorted(elements, key=sort_by_color)
print(elements) 
```

Enter fullscreen mode Exit fullscreen mode

我想让你注意的主要事情是`sort_by_color()`函数，我写这个函数的明确目的是按照颜色对列表中的元素对象进行排序。实际上，这有点麻烦，因为我再也不需要这个函数了。

这就是兰姆达斯的用武之地。我可以删除整个函数，将`elements = sorted(...)`行改为:

```
elements = sorted(elements, key=lambda e: e.color) 
```

Enter fullscreen mode Exit fullscreen mode

使用 lambda 允许我精确地描述我的逻辑*我使用它的地方*，而不是其他地方。(`key=`部分只是表示我正在将 lambda 传递给`sorted()`上的`key`参数。)

λ具有结构`lamba <parameters>: <return expression>`。它可以收集任意多的参数，用逗号分隔，但是它只能有一个*表达式，其值是隐式返回的。*

**GOTCHA ALERT:** 与常规函数不同，Lambdas 不支持类型注释(类型提示)。

如果我想重写 lambda 来按元素名称排序，而不是按颜色排序，我只需要修改表达式部分:

```
elements = sorted(elements, key=lambda e: e.name) 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。

同样，lambdas 主要在需要将一个函数和一个表达式传递给另一个函数时有用。这是另一个例子，这次在 lambda 上有更多的参数。

为了设置这个例子，让我们从一个飞行器的类开始，它存储了飞行器的名称和最大速度，并返回一个飞行器的随机速度。

```
import random
random.seed()

class Flyer:

    def __init__(self, name, top_speed):
        self.name = name
        self.top_speed = top_speed

    def get_speed(self):
        return random.randint(self.top_speed//2, self.top_speed) 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够让任何给定的飞行器对象执行任何飞行技巧，但是将所有的逻辑放入类本身是不切实际的...也许有成千上万的飞行技巧和变种！

兰姆达斯是定义这些把戏的一种方式。我们首先向这个类添加一个函数，它可以接受一个函数作为参数。我们假设这个函数总是有一个参数:表演魔术的速度。

```
 def perform(self, trick):
        performed = trick(self.get_speed())
        print(f"{self.name} perfomed a {performed}") 
```

Enter fullscreen mode Exit fullscreen mode

为了使用它，我们创建一个 Flyer 对象，然后将函数传递给它的`perform()`方法。

```
rd = Flyer("Rainbow Dash", 780)
rd.perform(lambda s: f"barrel-roll at {s} mph.")
rd.perform(lambda s: f"flip at {s} mph.") 
```

Enter fullscreen mode Exit fullscreen mode

因为 lambda 的逻辑是函数调用中的*，所以更容易看到发生了什么。*

回想一下，您*被允许*将 lambdas 存储在一个变量中。当您希望代码如此简短，但需要一些可重用性时，这实际上是很有帮助的。例如，假设我们有另一个飞行器，我们想让他们两个都做一个滚桶动作。

```
spitfire = Flyer("Spitfire", 650)
barrelroll = lambda s: f"barrel-roll at {s} mph."

spitfire.perform(barrelroll)
rd.perform(barrelroll) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们可以把`barrelroll`写成一个合适的单行函数，但是通过这样做，我们省去了一点样板文件。而且，由于在这段代码之后我们不会再使用这个逻辑，所以没有必要把一个成熟的函数挂在外面。

同样，**可读性很重要**。Lambdas 对于简短清晰的逻辑片段来说非常优秀，但是如果你有更复杂的东西，你应该*明确地*写一个合适的函数。

# 装修工

假设我们想要修改任何函数的行为，而实际上不改变函数本身。

让我们从一个合理的基本函数开始:

```
def partial_transfiguration(target, combine_with):
    result = f"{target}-{combine_with}"
    print(f"Transfiguring {target} into {result}.")
    return result

target = "frog"
target = partial_transfiguration(target, "orange")
print(f"Target is now a {target}.") 
```

Enter fullscreen mode Exit fullscreen mode

跑步给了我们:

```
Transfiguring frog into frog-orange.
Target is now a frog-orange. 
```

Enter fullscreen mode Exit fullscreen mode

很简单。但是，如果我们想增加一些额外的宣传呢？如你所知，我们真的不应该把那个逻辑*放在*我们的`partial_transfiguration`函数中。

这就是装修工的用武之地。装饰器在函数周围“包装”了额外的逻辑，这样我们实际上并没有修改原始函数本身。这使得代码更易于维护。

让我们从创建一个装饰器开始。这里的语法乍一看可能有点让人不知所措，但是请放心，我会把它分解开来。

```
import functools

def party_cannon(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print("Waaaaaaaait for it...")
        r = func(*args, **kwargs)
        print("YAAY! *Party cannon explosion*")
        return r

    return wrapper 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经意识到`wrapper()`实际上是一个*闭包*，它是由我们的`party_cannon()`函数创建并返回的。我们传递我们正在“包装”的函数，`func`。

然而，我们真的对我们正在包装的函数一无所知！它可能有也可能没有参数。闭包的参数列表`(*args, **kwargs)`可以接受几乎任何数量的参数，从零到(实际上)无穷大。当我们调用时，我们以同样的方式将这些参数传递给`func()`。

当然，如果在`func()`上的参数列表和通过装饰器传递给它的参数*之间存在某种不匹配，通常和预期的错误将会出现(这显然是一件好事。)*

在`wrapper()`中，我们用`func()`随时随地调用我们的函数。我选择在打印我的两条消息之间这样做。

我不想丢弃`func()`返回的值，所以我将返回值赋给`r`，并确保在我的包装器末尾用`return r`返回它。

请注意，对于在包装器中调用函数的方式，或者甚至对于调用多少次或**都没有严格的规则。您也可以用您认为合适的任何方式处理参数和返回值。关键是要确保包装器实际上不会以某种意想不到的方式*破坏*它所包装的函数。**

包装器前面奇怪的小行`@functools.wraps(func)`，实际上是一个装饰器本身。如果没有它，被包装的函数将会对自己的身份感到困惑，扰乱我们对像`__doc__`(文档字符串)和`__name__`这样重要的函数属性的外部访问。这个特殊的装饰器确保这种情况不会发生；被包装的函数保留它自己的身份，可以从函数外部以所有常用的方式访问它。(要使用这个特殊的装饰器，我们必须先使用`import functools`。)

现在我们已经写好了`party_cannon`装饰器，我们可以用它来给`partial_transfiguration()`函数添加我们想要的大喇叭。这么做就这么简单:

```
@party_cannon
def partial_transfiguration(target, combine_with):
    result = f"{target}-{combine_with}"
    print(f"Transfiguring {target} into {result}.")
    return result 
```

Enter fullscreen mode Exit fullscreen mode

第一行，`@party_cannon`是我们必须做的唯一更改！`partial_transfiguration`功能现在被*修饰为*。

**注意:**你甚至可以*堆叠*多个装饰者，一个在另一个上面。只要确保每个装饰器在它所包装的函数或装饰器之前立即*到来。*

我们以前的用法一点都没变:

```
target = "frog"
target = partial_transfiguration(target, "orange")
print(f"Target is now a {target}.") 
```

Enter fullscreen mode Exit fullscreen mode

然而，产出确实发生了变化:

```
Waaaaaaaait for it...
Transfiguring frog into frog-orange.
YAAY! *Party cannon explosion*
Target is now a frog-orange. 
```

Enter fullscreen mode Exit fullscreen mode

# 回顾

我们已经讨论了 Python 中函数“魔力”的四个方面。让我们花点时间来回顾一下。

*   **递归**是函数调用自身。当心“无限递归”；Python 不会让递归栈的深度超过大约一千次递归调用。

*   **嵌套函数**是在另一个函数中定义的函数。

*   嵌套函数可以*读取其封闭范围内的变量*，但是它不能修改它们，除非您在嵌套函数中首先将变量指定为`nonlocal`。

*   一个**闭包**是一个嵌套函数，*对*一个或多个非局部变量进行闭包，然后由封闭函数返回。

*   一个 **lambda** 是一个匿名(未命名)函数，由一个表达式组成，其值被返回。Lambdas 可以像其他对象一样被传递和赋值给变量。

*   装饰者“包装”另一个函数来扩展它的行为，而不需要直接修改你包装的函数。

有关这些主题的更多信息，您可以阅读文档。(你实际上会注意到，在官方文档中很少提到嵌套函数和闭包；它们是设计模式，而不是正式定义的语言结构。)

*   [Python 参考:复合语句—函数定义](https://docs.python.org/3/reference/compound_stmts.html#function-definitions)

*   [Python 参考:表达式— Lambda](https://docs.python.org/3/reference/expressions.html#lambda)

*   PEP 318:函数和方法的装饰器

*   [Python 标准库:functools](https://docs.python.org/3/library/functools.html)

* * *

*感谢 [@deniska](https://dev.to/deniska) 、 [@asdf](https://dev.to/asdf) 、@SnoopDeJi (Freenode IRC)和 [@sandodargo](https://dev.to/sandordargo) (DEV)提出的改进建议。**