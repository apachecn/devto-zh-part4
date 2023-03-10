# 在 Python 中拆分大型类和多重继承

> 原文：<https://dev.to/blueset/splitting-a-large-class-and-multiple-inheritance-in-python-3n36>

当我开始为 2.0 更新重构 EFB 电报主频道(ETM)时，我正在研究如何以一种体面的方式将代码组织到不同的文件中。在这篇文章中，我想谈谈我使用的策略，与我当时阅读的另一个代码库相比， [`itchat`](https://github.com/littlecodersh/ItChat/) 。

在 ETM 版本 1 中，大部分代码都是由冗长难看的 1675 行`__init__.py`组成的。随着更多的功能计划添加到 ETM，我真的很难浏览代码，这带来了我对重构这个巨大的东西的需求。

那时候的<small>(令人惊讶的是，两年前的)</small>，我在一个足够大的项目上的主要参考是`itchat`。从那时起，他们的代码结构就没怎么变过。`itchat`确实有一个相当大的代码库，但是它分割功能的方式相当不理想。

它的方式是在每个文件的根级别定义所有函数，并有一个加载器函数将这些方法“加载”到一个名为`core`的对象中，该对象包含一些配置数据。对于 Python 解释器来说，由于它的动态类型，这种方法确实有效。但是当你试图处理代码时，这看起来真的很糟糕，因为 IDE 通常不能对以这种方式定义的对象给出任何提示。尽管每个函数的参数都以`self`开头，但当您试图对库本身进行操作时，也会发生这种情况。

然后我继续寻找分解一个大类的其他常见做法，有些建议在一个函数中导入函数，有些建议使用多重继承。<sup>[参。](https://stackoverflow.com/questions/9638446/is-there-any-python-equivalent-to-partial-classes)</sup>前者和`itchat`在做的事情没有太大区别，后者一开始看起来很有希望。我继续做了一些多重继承的实验，发现它确实提供了更好的 ide 自动完成功能，但只是在主类中。在 IDE 中，我看不到一个子类与另一个子类的区别。这仍然是合理的，因为所有这些子类只聚集在主类中，它们彼此不知道。

```
# core.py from .components import load_components

class Core:
    def method_1(self, param_1, param_2, param_3):
        """Doc string goes here."""
        raise NotImplementedError()

    def method_2(self):
        """Doc string goes here."""
        raise NotImplementedError()

    def method_3(self, param_1):
        """Doc string goes here."""
        raise NotImplementedError()

load_components(Core) 
```

```
# components/__init__.py from .component_1 import load_component_1
from .component_2 import load_component_2

def load_components(core):
    load_component_1(core)
    load_component_2(core) 
```

```
# components/component_1.py def load_contact(core):
    core.method_1 = method_1
    core.method_2 = method_2

def method_1(self, param_1, param_2, param_3):
    # Actual implementation
    ...

def method_2(self):
    # Actual implementation
    ... 
```

```
# components/component_2.py def load_contact(core):
    core.method_3 = method_3

def method_3(self, param_1):
    # Actual implementation
    ... 
```

我心想，为什么不能多做几个类，让它们互相参考呢？结果对我来说很有效。我将我的函数分成几个不同的“管理器”类，每个类都用对主类的引用来初始化。这些类以拓扑顺序被实例化，使得被其他类引用的类被更早地创建。在 ETM，被引用的类通常是那些数据提供者工具，即`ExperimentalFlagsManager`、`DatabaseManager`和`TelegramBotManager`。

```
# __init__.py from .flags import ExperimentalFlagsManager
from .db import DatabaseManager
from .chat_binding import ChatBindingManager

class TelegramChannel():
    def __init__(self):
        self.flags: ExperimentalFlagsManager = ExperimentalFlagsManager(self)
        self.db: DatabaseManager = DatabaseManager(self)
        self.chat_binding: ChatBindingManager = ChatBindingManager(self) 
```

```
# flags.py from typing import TYPE_CHECKING

if TYPE_CHECKING:
    # Avoid cycle import for type checking
    from . import TelegramChannel

class ExperimentalFlagsManager:
    def __init__(channel: 'TelegramChannel'):
        self.channel = channel
        ... 
```

```
# db.py from typing import TYPE_CHECKING
from .flags import ExperimentalFlagsManager

if TYPE_CHECKING:
    # Avoid cycle import for type checking
    from . import TelegramChannel

class DatabaseManager:
    def __init__(channel: 'TelegramChannel'):
        self.channel: 'TelegramChannel' = channel
        self.flags: ExperimentalFlagsManager = channel.flags
        ... 
```

```
# chat_binding.py from typing import TYPE_CHECKING
from .chat_binding import ChatBindingManager
from .db import DatabaseManager

if TYPE_CHECKING:
    # Avoid cycle import for type checking
    from . import TelegramChannel

class ChatBindingManager:
    def __init__(channel: 'TelegramChannel'):
        self.channel: 'TelegramChannel' = channel
        self.flags: ExperimentalFlagsManager = channel.flags
        self.db: DatabaseManager = channel.db
        ... 
```

在《重构 ETM》的过程中，我了解到 Python 中的多重继承还有另一种用途——mixins。当您想要将一组特性添加到许多其他类中时，Mixins 是非常有用的类。当我试图在所有管理器类中不断添加对`gettext`翻译器的引用时，这启发了我。

我添加了一个名为`LocaleMixin`的 mixin，它从主类引用中提取翻译函数(`gettext`和`ngettext`)(假设它们肯定在那里)，并分配一个反映这些方法的本地属性。

```
class LocaleMixin:
    channel: 'TelegramChannel'

    @property
    def _(self):
        return self.channel.gettext

    @property
    def ngettext(self):
        return self.channel.ngettext 
```

当 mixin 类被添加到继承类的列表中时，IDE 可以正确地识别这些助手属性，并且它们的定义被合并到同一个地方。我发现它比以前的风格更有条理。

* * *

最后，我发现简单地为我的代码的每个组件创建类是分解一个大类的最有组织的、IDE 友好的方式，mixins 有助于使引用或助手函数对多个类可用。

在 Python 中拆分一个大类和多重继承的帖子[最早出现在](https://blog.1a23.com/2019/09/14/splitting-a-large-class-and-multiple-inheritance-in-python/) [1A23 博客](https://blog.1a23.com)上。