# 用 Pytest 测试点击应用程序

> 原文：<https://dev.to/wangonya/testing-click-applications-with-pytest-2o79>

尽可能多地为您的代码编写测试是一个好习惯。如果您正在使用 Python， [pytest](https://pytest.org/en/latest/) 使得编写和运行测试的过程更加顺畅。不久前，我写了几篇关于开始使用 pytest 进行测试的文章，所以如果您完全不熟悉 pytest，您可能想看看它们:

*   [Python TDD 与 Pytest -入门](https://wangonya.com/blog/getting-started-with-pytest/)
*   [用 Pytest 断言异常](https://wangonya.com/blog/pytest-asserting-exceptions/)
*   [调试时捕获打印语句](https://wangonya.com/blog/pytest-capture-print/)
*   [跳过测试](https://wangonya.com/blog/pytest-skip/)

对于测试 CLI 应用程序，Click 提供了一个方便的模块:`click.testing`，它有一些有用的函数(特别是`CliRunner()`)来帮助我们调用命令并检查它们的行为。

我们将继续测试我们的应用程序的每个部分——创建、读取、更新和删除。

## 安装 pytest 并编写第一个测试

pytest 可以通过 pip 安装:

```
(env) $ pip install pytest 
```

Enter fullscreen mode Exit fullscreen mode

安装 pytest 后，在根目录下创建一个 tests 文件夹，并添加第一个测试文件:

```
(env) $ mkdir tests && cd tests

(env) $ touch test_app.py 
```

Enter fullscreen mode Exit fullscreen mode

在 test_app 文件中，添加以下代码作为开始:

```
def test_add():
    pass 
```

Enter fullscreen mode Exit fullscreen mode

要运行测试，在终端
上运行`pytest`

```
(env) $ pytest
================== test session starts ====================
platform linux -- Python 3.7.3, pytest-5.1.0, py-1.8.0, pluggy-0.12.0
rootdir: /home/wangonya/code/contacts-cli
collected 1 item

tests/test_app.py .                              [100%]

================== 1 passed in 0.04s ======================= 
```

Enter fullscreen mode Exit fullscreen mode

## 测试`add`命令

让我们编辑 test_app 文件来添加一个测试，看看`add`命令是否添加了一个新的联系人:

```
from click.testing import CliRunner

from app import add

runner = CliRunner()

def test_add():
    response = runner.invoke(add, ["test-user", "-m", "0"])
    assert response.exit_code == 0
    assert "Contact test-user added!" in response.output
    assert "{'mobile': '0'}" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们像在终端上一样调用命令，传入所需的参数和选项:`response = runner.invoke(add, ["test-user", "-m", "0"])`。

然后我们检查命令是否成功执行:`assert response.exit_code == 0`。

如果命令成功执行，我们希望在响应中返回一条成功消息，其中包含我们添加的值:

```
assert "Contact test-user added!" in response.output
assert "{'mobile': '0'}" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

其余的测试将遵循相同的格式。

## 测试`list`命令

```
def test_list():
    response = runner.invoke(list)
    assert response.exit_code == 0
    assert "Here\'s a list of all your contacts:" in response.output
    assert "'test-user': {'mobile': '0'}" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

list 命令不带任何参数或选项，所以我们直接调用它:`response = runner.invoke(list)`。

## 测试`view`命令

```
def test_view():
    response = runner.invoke(view, "test-user")
    assert response.exit_code == 0
    assert "{'mobile': '0'}" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

## 测试`update`命令

```
def test_update():
    response = runner.invoke(update, ["test-user", "-m", "12345"])
    assert response.exit_code == 0
    assert "Contact updated!" in response.output
    assert "{'mobile': '12345'}" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

## 测试`delete`命令

```
def test_delete():
    response = runner.invoke(delete, "test-user")
    assert response.exit_code == 0
    assert "Contact deleted!" in response.output

    # call view on test-user to confirm it doesn't exist
    response = runner.invoke(view, "test-user")
    assert response.exit_code == 0
    assert "The contact you searched for doesn't exist" in response.output 
```

Enter fullscreen mode Exit fullscreen mode

## 改进

随着应用程序的增长，您可能想考虑使用[夹具](https://docs.pytest.org/en/latest/fixture.html)，并在`conftest.py`文件中设置类似`runner`的东西。我们在这里成功了，因为我们的测试很简单，都在一个文件中。一旦引入了多个测试文件，遵循我们在这里使用的方法将会导致大量不必要的重复代码。

此外，我们在测试中直接调用了我们的 API。这种操作应该被完美地模仿。