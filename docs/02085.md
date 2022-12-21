# 测试 mypy 存根、插件和类型

> 原文：<https://dev.to/wemake-services/testing-mypy-stubs-plugins-and-types-1b71>

**最初发表于我的博客**:【https://sobolevn.me/2019/08/testing-mypy-types T2】

你有没有尝试过:

*   在自己的项目中创建复杂的泛型类型？
*   为你的库写[可分发存根](https://www.python.org/dev/peps/pep-0561/)？
*   创建[自定义`mypy`插件](https://mypy.readthedocs.io/en/latest/extending_mypy.html)？
*   编写自定义类型检查器？

如果你尝试做这些，你很快就会发现你需要测试你的类型。等等，什么？我来详细解释一下这个悖论。

## Python 中对类型的第一次测试

让我们从历史课开始。
第一次对`mypy`产生兴趣，我发现他们的测试技术独特而有趣。这就是[看起来](https://github.com/python/mypy/blob/master/test-data/unit/check-lists.test)的样子:

```
[case testNestedListAssignmentToTuple]
from typing import List
a, b, c = None, None, None # type: (A, B, C)

a, b = [a, b]
a, b = [a]  # E: Need more than 1 value to unpack (2 expected)
a, b = [a, b, c]  # E: Too many values to unpack (2 expected, 3 provided) 
```

这看起来很熟悉:

*   `[case]`定义了一个新的测试，就像`def test_`一样
*   里面的内容是用`mypy`处理过的原始`python`源代码行
*   `# E:`注释是`assert`语句，告诉每行的`mypy`输出是什么

所以，我们也可以为我们的库编写这种测试，对吗？当我开始编写 [`returns`](https://github.com/dry-python/returns) 库(这是 Python 中的一个类型化 monad 实现)时，我就有这个问题。因此，我需要测试内部发生了什么，以及`mypy`揭示了哪些类型。然后我试图重用来自`mypy`的这个测试用例。

长话短说，这是不可能的。这个小助手内置在`mypy`源代码里面，不能重用。于是，我开始寻找其他的解决方案。

## 现代方法

我偶然发现了 [`pytest-mypy-plugins`](https://github.com/typeddjango/pytest-mypy-plugins) 的包裹。最初创建它是为了确保`django`的类型在 [`TypedDjango`](https://github.com/typeddjango) 项目中工作良好。查看一下我之前的帖子。

要在项目运行中安装`pytest-mypy-plugins`:

```
pip install pytest-mypy-plugins 
```

它的工作方式类似于`mypy`自己的测试用例，但设计略有不同。让我们创建一个`yaml`文件，并将其命名为`./typesafety/test_compose.yml` :

```
# ./typesafety/test_compose.yml
- case: compose_two_functions
  main: |
    from myapp import first, second

    reveal_type(second(first(1)))  # N: Revealed type is 'builtins.str*'
  files:
    - path: myapp.py
      content: |
        def first(num: int) -> float:
            return float(num)

        def second(num: float) -> str:
            return str(num) 
```

这是什么？

*   `case`定义，这基本上是一个测试的名称
*   包含测试所需的`python`源代码的`main`部分
*   `# N:`表示来自`mypy`的注释的注释
*   `files`您可以在其中创建将在该测试中使用的临时帮助文件的部分

不错！我们如何运行它？由于`pytest-mypy-plugins`是一个`pytest`插件，我们只需要照常运行`pytest`，并指定我们的`mypy`配置文件(默认为`mypy.ini` ):

```
pytest --mypy-ini-file=setup.cfg 
```

您可以有两个`mypy`配置:一个用于您的项目，一个用于测试。随便说说。来看看我们的`setup.cfg`内容:

```
[mypy]
check_untyped_defs = True
ignore_errors = False
ignore_missing_imports = True
strict_optional = True 
```

这是调用结果:

```
» pytest --mypy-ini-file=setup.cfg
================================ test session starts =================================
platform darwin -- Python 3.7.4, pytest-5.1.1, py-1.8.0, pluggy-0.12.0
rootdir: /code/, inifile: setup.cfg
plugins: mypy-plugins-1.0.3
collected 1 item

typesafety/test_compose.yml .                                                  [100%]

================================= 1 passed in 2.00s ================================== 
```

有用！让我们的例子变得复杂一点。

## 检查错误

我们还可以使用`pytest-mypy-plugins`来加强和检查我们的复杂类型规范的约束。让我们假设你有一个复杂泛型的类型定义，你想确保它能正确工作。

这实际上非常有帮助，因为您可以用原始的`mypy`检查来检查成功的案例，而您不能告诉`mypy`对特定的表达式或调用预期一个错误。

让我们从复杂类型定义开始:

```
# returns/functions.py from typing import Callable, TypeVar

# Aliases: _FirstType = TypeVar('_FirstType')
_SecondType = TypeVar('_SecondType')
_ThirdType = TypeVar('_ThirdType')

def compose(
    first: Callable[[_FirstType], _SecondType],
    second: Callable[[_SecondType], _ThirdType],
) -> Callable[[_FirstType], _ThirdType]:
    """Allows typed function composition."""
    return lambda argument: second(first(argument)) 
```

这段代码采用两个函数并检查它们类型是否匹配，这样它们就可以被组合。来测试一下:

```
# ./typesafety/test_compose.yml
- case: compose_two_wrong_functions
  main: |
    from returns.functions import compose

    def first(num: int) -> float:
        return float(num)

    def second(num: str) -> str:
        return str(num)

    reveal_type(compose(first, second))
  out: |
    main:9: error: Cannot infer type argument 2 of "compose"
    main:9: note: Revealed type is 'def (Any) -> Any' 
```

在这个例子中，我改变了我们做类型断言的方式:`out`比行内注释更容易多行输出。

现在我们有两个通过的测试:

```
» pytest --mypy-ini-file=setup.cfg
================================ test session starts =================================
platform darwin -- Python 3.7.4, pytest-5.1.1, py-1.8.0, pluggy-0.12.0
rootdir: /code, inifile: setup.cfg
plugins: mypy-plugins-1.0.3
collected 2 items

typesafety/test_compose.yml ..                                                 [100%]

================================= 2 passed in 2.65s ================================== 
```

让我们测试一个更复杂的情况。

## Extra mypy 设置

我们可以在每个测试的基础上改变`mypy`配置。让我们给现有配置添加一些新值:

```
- case: compose_optional_functions
  mypy_config:  # appends options for this test
    no_implicit_optional = True
  main: |
    from returns.functions import compose

    def first(num: int = None) -> float:
        return float(num)

    def second(num: float) -> str:
        return str(num)

    reveal_type(compose(first, second))
  out: |
    main:3: error: Incompatible default for argument "num" (default has type "None", argument has type "int")
    main:9: note: Revealed type is 'def (builtins.int*) -> builtins.str*' 
```

我们添加了 [`no_implicit_optional`](https://mypy.readthedocs.io/en/latest/command_line.html#none-and-optional-handling) 配置选项，该选项要求将显式的`Optional[]`类型添加到我们将`None`设置为默认值的参数中。我们的测试是从`mypy_config`部分获得的，该部分将选项附加到`--mypy-ini-file`设置的基本`mypy`设置。

## 自定义 DSL

`pytest-mypy-plugins`还允许创建定制的基于`yaml`的`DSL`来使你的测试过程更简单，测试用例更短。

想象一下，我们想要将`reveal_type`作为顶级键。它只会显示传递给它的源代码行的类型。像这样:

```
-   case: reveal_type_extension_is_loaded
    main: |
      def my_function(arg: int) -> float:
          return float(arg)
    reveal_type: my_function
    out: |
      main:4: note: Revealed type is 'def (arg: builtins.int) -> builtins.float' 
```

让我们来看看实现它需要什么:

```
# reveal_type_hook.py from pytest_mypy.item import YamlTestItem

def hook(item: YamlTestItem) -> None:
    parsed_test_data = item.parsed_test_data
    main_source = parsed_test_data['main']
    obj_to_reveal = parsed_test_data.get('reveal_type')
    if obj_to_reveal:
        for file in item.files:
            if file.path.endswith('main.py'):
                file.content = f'{main_source}\nreveal_type({obj_to_reveal})' 
```

我们在这里做什么？

1.  我们从`main:`键获得源代码
2.  然后从`reveal_type:`键追加`reveal_type()`调用

因此，我们有了一个满足我们最初想法的习俗`DSL`。

跑步:

```
» pytest --mypy-ini-file=setup.cfg --mypy-extension-hook=reveal_type_hook.hook
================================ test session starts =================================
platform darwin -- Python 3.7.4, pytest-5.1.1, py-1.8.0, pluggy-0.12.0
rootdir: /code, inifile: setup.cfg
plugins: mypy-plugins-1.0.3
collected 1 item

typesafety/test_hook.yml .                                                     [100%]

================================= 1 passed in 0.87s ================================== 
```

我们传递一个新的标志:`--mypy-extension-hook`，它指向我们自己的`DSL`实现。而且效果非常好！这就是如何在基于`yaml`的测试中重用大量代码。

## 结束语

对于在`python`中大量使用类型或`mypy`插件的人来说,`pytest-mypy-plugins`是绝对必要的。它简化了重构和分发类型的过程。

您可以在以下位置查看这些测试的真实使用示例:

*   [TypedDjango/django-stubs](https://github.com/typeddjango/django-stubs/tree/master/test-data)
*   [dry-python/returns](https://github.com/dry-python/returns/tree/master/typesafety)

分享您的使用案例！我们仍然处于这个项目的早期阶段，我们想知道我们的用户在想什么。