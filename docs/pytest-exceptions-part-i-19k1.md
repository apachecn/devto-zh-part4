# pytest 异常-第一部分

> 原文：<https://dev.to/serhatteker/pytest-exceptions-part-i-19k1>

`pytest`框架使得编写小测试变得容易，同时也支持应用程序和库的复杂功能测试。

`pytest`相对于`unittest`模块的优势之一是我们不需要在不同的数据结构上使用不同的断言方法。所以我宁愿用`pytest`。

一个好的测试套件应该既测试输入正确时的预期行为，也测试输入触发某些异常时的预期行为。如果不测试异常，我们就不能确定它们是否会在必要的时候被抛出，我们的测试覆盖了所有的异常。所以测试`exceptions`也很重要。

假设我们有一个如下的基本函数:

```
def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by Zero")

    return a / b 
```

Enter fullscreen mode Exit fullscreen mode

相关测试应如下:

```
import pytest

from pytest_exception_handle import divide

def test_zero_division():
    assert divide(12, 3) == 4 
```

Enter fullscreen mode Exit fullscreen mode

并且带异常测试的完整测试应该是这样的:

```
import pytest

from pytest_exception_handle import divide

def test_zero_division():
    assert divide(12, 3) == 4

def test_zero_division_exception():
    with pytest.raises(ValueError):
        assert divide(1, 0) 
```

Enter fullscreen mode Exit fullscreen mode

在运行`pytest -v` — `-v`标志进行详细输出之后，您应该会看到如下的
输出:

```
platform linux -- Python 3.6.8, pytest-3.8.1, py-1.8.0, pluggy-0.12.0 --
/tmp/python/pytest_exception_handle/.venv/bin/python3
cachedir: .pytest_cache
rootdir: /tmp/python/pytest_exception_handle, inifile:

collected 2 items
tests/test_pytest_exception_handle/test_exception_basic.py::test_zero_division PASSED                                           [ 50% ]
tests/test_pytest_exception_handle/test_exception_basic.py::test_zero_division_exception PASSED                                 [100% ] 
```

Enter fullscreen mode Exit fullscreen mode

如果你也想测试`error message`:

```
import pytest

from pytest_exception_handle import divide

def test_zero_division():
    assert divide(12, 3) == 4
The pytest framework makes it easy to write small tests, yet scales to support complex functional testing for applications and libraries. One of the advantages of pytest over the unittest module is that we don’t need to use different assert methods on different data structures. So I’d rather use pytest. A good test suite should test the expected behaviour both when the input is correct and also when the input triggers some exception.

def test_zero_division_exception():
    with pytest.raises(ValueError):
        assert divide(1, 0)

def test_zero_division_exception_with_message():
    with pytest.raises(ValueError, match="Cannot divide by Zero"):
        assert divide(1, 0) 
```

Enter fullscreen mode Exit fullscreen mode

是的，我们不需要额外的`ValueError` `Exception`用于“零除法误差”，因为`python`已经有一个内置的:

```
import pytest

def test_zero_division_builtin():
    with pytest.raises(ZeroDivisionError):
        assert 1/0 
```

Enter fullscreen mode Exit fullscreen mode