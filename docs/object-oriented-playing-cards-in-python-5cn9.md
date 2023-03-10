# Python 中面向对象的扑克牌

> 原文：<https://dev.to/wrrnwng/object-oriented-playing-cards-in-python-5cn9>

从 3.4 版本开始， [`enum`](https://docs.python.org/3/library/enum.html) 成为 Python 标准库的一部分，我认为经典的 OOP 扑克牌示例将是对它的一个很好的演示。

在这个例子中，我不会像在扑克中那样担心实际的扑克牌数值(2 比 3 更有价值，黑桃比红桃更有价值，等等。)因为在许多游戏中，纸牌可能不符合这些价值。

我将首先为套件创建四个`enum`的集合，为卡的面值创建十三个`enum`的集合。我也将使用`auto`，因为我不会担心`enum`的确切值，尽管我会按扑克数值顺序列出它们，而且`auto`会自动给我这些数值。

```
from enum import Enum, auto

class Suite(Enum):
    DIAMOND = auto()
    CLUB = auto()
    HEART = auto()
    SPADE = auto()

class Value(Enum):
    TWO = auto()
    THREE = auto()
    FOUR = auto()
    FIVE = auto()
    SIX = auto()
    SEVEN = auto()
    EIGHT = auto()
    NINE = auto()
    TEN = auto()
    JACK = auto()
    QUEEN = auto()
    KING = auto()
    ACE = auto() 
```

Enter fullscreen mode Exit fullscreen mode

现在我将使用[getter 和 setter](https://warrenwong.org/using-getters-and-setters-in-python)创建`Card`类，这样我可以确保在初始化每个卡时传递正确的`enum` s。

```
class Card:
    def __init__ (self, suite: Suite, value: Value):
        self.suite = suite
        self.value = value

    @property
    def suite(self):
        return self._suite

    @property
    def value(self):
        return self._value

    @suite.setter
    def suite(self, suite: Suite):
        if suite not in Suite:
            raise Exception
        self._suite = suite

    @value.setter
    def value(self, value: Value):
        if value not in Value:
            raise Exception
        self._value = value

    def __repr__ (self):
        return f"<Card {self.value} of {self.suite}>" 
```

Enter fullscreen mode Exit fullscreen mode

如果我们试图用有效的`Suite`或`Value`之外的任何东西初始化`Card`，Python 将引发一个`Exception`。

好了，现在我们可以为一副纸牌创建一个类。

```
class Deck:
    def __init__ (self):
        self.cards = [Card(s, v) for s in Suite for v in Value]

    def __repr__ (self):
        output = [f"{c}\n" for c in self.cards]
        return "".join(output) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们试一试，看看是否一切正常。

```
>>> from card import Suite, Value, Card, Deck
>>> deck = Deck()
>>> len(deck.cards)
52
>>> deck
<Card Value.TWO of Suite.DIAMOND>
<Card Value.THREE of Suite.DIAMOND>
<Card Value.FOUR of Suite.DIAMOND>
<Card Value.FIVE of Suite.DIAMOND>
<Card Value.SIX of Suite.DIAMOND>
<Card Value.SEVEN of Suite.DIAMOND>
<Card Value.EIGHT of Suite.DIAMOND>
<Card Value.NINE of Suite.DIAMOND>
<Card Value.TEN of Suite.DIAMOND>
<Card Value.JACK of Suite.DIAMOND>
<Card Value.QUEEN of Suite.DIAMOND>
<Card Value.KING of Suite.DIAMOND>
<Card Value.ACE of Suite.DIAMOND>
<Card Value.TWO of Suite.CLUB>
<Card Value.THREE of Suite.CLUB>
<Card Value.FOUR of Suite.CLUB>
<Card Value.FIVE of Suite.CLUB>
<Card Value.SIX of Suite.CLUB>
<Card Value.SEVEN of Suite.CLUB>
<Card Value.EIGHT of Suite.CLUB>
<Card Value.NINE of Suite.CLUB>
<Card Value.TEN of Suite.CLUB>
<Card Value.JACK of Suite.CLUB>
<Card Value.QUEEN of Suite.CLUB>
<Card Value.KING of Suite.CLUB>
<Card Value.ACE of Suite.CLUB>
<Card Value.TWO of Suite.HEART>
<Card Value.THREE of Suite.HEART>
<Card Value.FOUR of Suite.HEART>
<Card Value.FIVE of Suite.HEART>
<Card Value.SIX of Suite.HEART>
<Card Value.SEVEN of Suite.HEART>
<Card Value.EIGHT of Suite.HEART>
<Card Value.NINE of Suite.HEART>
<Card Value.TEN of Suite.HEART>
<Card Value.JACK of Suite.HEART>
<Card Value.QUEEN of Suite.HEART>
<Card Value.KING of Suite.HEART>
<Card Value.ACE of Suite.HEART>
<Card Value.TWO of Suite.SPADE>
<Card Value.THREE of Suite.SPADE>
<Card Value.FOUR of Suite.SPADE>
<Card Value.FIVE of Suite.SPADE>
<Card Value.SIX of Suite.SPADE>
<Card Value.SEVEN of Suite.SPADE>
<Card Value.EIGHT of Suite.SPADE>
<Card Value.NINE of Suite.SPADE>
<Card Value.TEN of Suite.SPADE>
<Card Value.JACK of Suite.SPADE>
<Card Value.QUEEN of Suite.SPADE>
<Card Value.KING of Suite.SPADE>
<Card Value.ACE of Suite.SPADE> 
```

Enter fullscreen mode Exit fullscreen mode