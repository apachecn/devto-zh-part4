# TPP 主题 14:领域语言

> 原文：<https://dev.to/steadbytes/tpp-topic-14-domain-languages-2hdb>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-14-challenges/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 挑战 1

> 你当前项目的一些需求可以用特定领域的语言来表达吗？有没有可能编写一个编译器或翻译器来生成所需的大部分代码？

是的，这是我的团队和我想调查实际实施的东西。当前的项目是一个 REST API，正在讨论的需求是*用户授权*。**业务**标识了许多不同的用户*类型*——访客、集成、超级、管理员等。其中每一种都有一组不同的权限来管理可以执行哪些 API 操作，即:

```
guest: GET /foo, GET /bar
integration: ANY /foo, POST /bar
super: ANY /foo, ANY /bar
administrator: ANY /foo, ANY /bar, ANY /baz 
```

如果上面只有一小部分动作和用户类型，那么领域语言就不值得努力。然而，API 包含大量的端点，用户类型和权限/端点(当然)会随着时间而改变。领域语言将为这些权限提供一个简单的规范，并生成**[django](https://www.djangoproject.com/)/[django REST 框架](https://www.django-rest-framework.org/)代码*和*相应的测试。由于 YAML 用于项目中使用的其他工具( [OpenAPI](https://swagger.io/docs/specification/about/) ， [CircleCI](https://circleci.com/) e.t.c)，领域语言可以使用 YAML 以保持一致性。一个*非常简单的*例子:**

 **### 领域语言规范

```
/foo:
    GET:
        - guest
    ANY:
        - integration
        - super
        - administrator
/bar:
    GET:
        - guest
    POST:
        - integration
    ANY:
        - super
        - administrator
/baz:
    ANY:
        - administrator 
```

### [T3】例子`/foo` DRF `ViewSet`](#example-raw-foo-endraw-drf-raw-viewset-endraw-)

使用[django-rest-framework-roles](https://github.com/computer-lab/django-rest-framework-roles)实现基于角色的权限。

```
from rest_framework.viewsets import ModelViewSet
from rest_framework.exceptions import PermissionDenied
from drf_roles.mixins import RoleViewSetMixin
from api.models import Foo
from api.serializers import FooSerializer

class FooViewSet(RoleViewSetMixin, ModelViewSet):
    queryset = Foo.objects.all()
    serializer_class = FooSerializer

    def perform_create_for_guest_user(self, serializer):
        raise PermissionDenied()

    def perform_update_for_guest_user(self, serializer):
        raise PermissionDenied()

    def perform_destroy_for_guest_user(self):
        raise PermissionDenied()

    # can now implement non-boilerplate code if needed 
```

### 示例`/foo`权限测试

使用 [pytest-django](https://pytest-django.readthedocs.io/en/latest/index.html) 。

```
import pytest
from api.models import User
from django.contrib.auth.models import Group
from rest_framework.exceptions import PermissionDenied
from django.urls import reverse

@pytest.mark.django_db
class TestFoo:
    def test_guest_can_perform_GET(self, client):
        username = "test"
        password = "1234"
        user = User.objects.create(username=username, password=password)
        user.groups.add(Group.objects.get(name="guest"))
        client.login(username=username, password=password)
        r = client.get(reverse("foo-list"))
        assert r.status_code == 200

        with pytest.raises(PermissionDenied):
            client.get(reverse("foo-list"))

    def test_guest_cannot_perform_POST(self, client):
        # ... 
    # ... 
```

我在这里跳过了很多细节，但是总体思路是有的，应该可以为。

## 挑战 2

> 如果你决定采用迷你语言作为一种更接近问题领域的编程方式，你就接受了实现它们需要一些努力。你能看到你为一个项目开发的框架可以在其他项目中重用的方法吗？

上面给出的例子非常具体，但是它可以在另一个使用 django-rest-framework 的项目中重用，该项目也需要基于角色的权限。话虽如此，通过实现不同的编译器，领域语言本身可以跨语言/框架使用——例如，用于 [Flask](https://flask.palletsprojects.com/en/1.1.x/) 或 [Phoenix](https://phoenixframework.org/) 。

## 练习 4

> 我们想实现一个迷你语言来控制一个简单的绘图包(可能是一个海龟图形系统)。这种语言由单字母命令组成。一些命令后跟一个数字。例如，以下输入将绘制一个矩形:

```
P 2 # select pen 2
D # pen down
W 2 # draw west 2cm
N 1 # then north 1
E 2 # then east 2
S 1 # then back south
U # pen up 
```

> 实现解析该语言的代码。它的设计应该使添加新命令变得简单。

这里有一个简单的 Python 实现——专注于领域语言解析，不包括实际执行“海龟动作”的代码。它包括一些基本的错误处理，可以通过向`COMMANDS`字典添加新条目来轻松扩展。

```
from collections import namedtuple
from typing import Iterable

Command = namedtuple("Command", ["token", "handler", "has_arg"])

# assume handler functions are implemented elsewhere COMMANDS = {
    c.token: c
    for c in (
        Command("P", select_pen, True),
        Command("U", pen_up, False),
        Command("D", pen_down, False),
        Command("N", move_pen, True),
        Command("E", move_pen, True),
        Command("S", move_pen, True),
        Command("W", move_pen, True),
    )
}

def get_arg(line: str) -> int:
    """ Retrieve integer argument from input ``line``.

    Raises:
        ValueError: If no integer argument present in ``line``
    """
    try:
        return int(line[2])
    except (IndexError, ValueError):
        raise ValueError(f"Command requires integer argument: {line}")

def parse(lines: Iterable[str]):
    for l in lines:
        cmd = COMMANDS.get(l[0])
        if cmd is None:
            raise Exception(f"Unknown command: {l}")
        if cmd.has_arg:
            cmd.handler(get_arg(l))
        else:
            cmd.handler() 
```

## 练习 5

> 在前一个练习中，我们为 turtle 图形语言实现了一个简单的解析器——它是一种外部领域语言。现在再次将其实现为内部语言。不要做任何聪明的事情:只需为每个命令编写一个函数。您可能必须将命令的名称改为小写，并且您可能需要将它们包装在某个内容中以提供一些上下文。

我上一个练习的解决方案实际上也回答了这个问题——在外部领域语言中用来执行动作的`Command.handler`函数是内部语言。下面是使用内部语言重写的上一个练习的输入示例:

```
from enum import Enum, auto

class Direction(Enum):
    NORTH = auto()
    EAST = auto()
    SOUTH = auto()
    WEST = auto()

select_pen(2)
pen_down()
move_pen(Direction.WEST, 2)
move_pen(Direction.NORTH, 1)
move_pen(Direction.EAST, 2)
move_pen(Direction.SOUTH, 1)
pen_up() 
```

## 练习 6

> 设计一个 BNF 文法来解析一个时间规范。应接受以下所有示例:

```
4pm, 7:38pm, 23:42, 3:16, 3:16am 
```

`<hour-tens-place>`和`<minute-tens-place>`分别将小时和分钟的可能值约束为`00`->-`23`和`00`->-`59`:

```
<time> ::= <hour> <period> | <hour> ":" <minute> <period> | <hour> ":" <minute>
<period> ::= "am" | "pm"
<hour> ::= <hour-tens-place> <digit> | <digit>
<minute> ::= <minute-tens-place> <digit> | <digit>
<hour-tens-place> ::= "0"| "1" | "2"
<minute-tens-place> ::= "0" | "1" | "2" | "3" | "4" | "5"
<digit> ::= "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9" 
```

## 练习 7

> 使用您选择的语言中的一个 PEG 解析器生成器为前面练习中的 BNF 语法实现一个解析器。输出应该是一个整数，包含午夜过后的分钟数。

使用 [instaparse](https://github.com/Engelberg/instaparse) 库的 Clojure 实现:

```
(ns  tpp-14-ex7.core  (:require  [instaparse.core  :as  insta]))  (def  time-spec  (insta/parser  "time = hour period | hour ':' minute period | hour ':' minute
    period = \"am\" | \"pm\"
    hour = hour-tens-place digit | digit
    minute = minute-tens-place digit | digit
    hour-tens-place = '0' | '1' | '2'
    minute-tens-place = '0' | '1' | '2' | '3' | '4' | '5'
    digit = '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'"))  (time-spec  "4pm")  ; => [:time [:hour [:digit "4"]] [:period "pm"]]  (time-spec  "7:38pm")  ; => [:time [:hour [:digit "7"]] ":" [:minute [:minute-tens-place "3"] [:digit "8"]] [:period "pm"]]  (time-spec  "23:42")  ; => [:time [:hour [:hour-tens-place "2"] [:digit "3"]] ":" [:minute [:minute-tens-place "4"] [:digit "2"]]]  (time-spec  "3:16")  ; => [:time [:hour [:digit "3"]] ":" [:minute [:minute-tens-place "1"] [:digit "6"]]]  (time-spec  "3:16am")  ; => [:time [:hour [:digit "3"]] ":" [:minute [:minute-tens-place "1"] [:digit "6"]] [:period "am"]] 
```

## 练习 8

好老 regex！下面是一个 Python 实现，它将一个时间字符串转换成自午夜以来的分钟数，以及一些基本测试:

```
import re

import pytest

period = r"(?P<period>am|pm)"
hour = r"(?P<hour>([0-2]\d)|\d)"
minute = r"(?P<minute>([0-5]\d)|\d)"

TIME_SPECS = [
    re.compile(p)
    for p in [
        fr"\A{hour}{period}\Z",
        fr"\A{hour}:{minute}{period}\Z",
        fr"\A{hour}:{minute}\Z",
    ]
]

def time_to_minutes(s: str) -> int:
    for spec in TIME_SPECS:
        m = spec.match(s)
        if m:
            parts = m.groupdict()
            return sum(
                (
                    int(parts["hour"]) * 60,
                    int(parts.get("minute", 0)),
                    12 * 60 if parts.get("period") == "pm" else 0,
                )
            )

@pytest.mark.parametrize(
    "time,expected",
    [
        ("4pm", 960),
        ("7:38pm", 1178),
        ("23:42", 1422),
        ("3:16", 196),
        ("3:16am", 196),
        ("3", None),
        ("3:am", None),
        ("31:30", None),
        ("10:65", None),
    ],
)
def test_time_to_minutes(time, expected):
    assert time_to_minutes(time) == expected 
```

注意，这确实匹配 BNF 语法，但是有一些缺失的约束。语法和解析代码都将接受一个小时大于一天结束时间的时间字符串。

```
>>> time_to_minutes("24:30")
1470 
```**