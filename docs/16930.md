# 一个 Bug 是什么时候引入的？用 Pytest 对分

> 原文：<https://dev.to/orenovadia/git-bisect-with-python-tests-a4f>

原来一个非常老的测试被破坏了一段时间，没有在自动构建中运行，哎呀。

找到破坏测试的确切提交将非常有帮助。好吧，[git-平分](https://git-scm.com/docs/git-bisect)来拯救:git 平分将二分搜索法提交历史以找到错误的提交。我们可以使用 [pytest](https://docs.pytest.org/) 来告诉 git 对于任何可能的提交，测试是否通过。

## 设置

下面是一个有简单功能和单元测试的玩具仓库:

```
$ ls my_script.py  README 
```

这是`my_script.py` :

```
from functools import reduce
from operator import mul

def product(factors):
    return reduce(lambda x , y: x - y, factors)

def test_product():
    assert product([1, 2, 3,]) == 6 
```

到目前为止很简单。`test_product`因某种原因而破产。

让我们来看看提交历史:

```
$ git log --oneline
7e26872 Unrelated commit # 21
f67f19e Unrelated commit # 20
...
2b37410 Unrelated commit # 15
80956a1 Unrelated commit # 14
d2a3327 Some innocent change that certainly did not break anything
8cc3f8b Unrelated commit # 13
1b09ced Unrelated commit # 12
...
3e73dfa Unrelated commit # 2
790ba87 Unrelated commit # 1
0c75c27 unrelated commit
d7af196 Added product function with a unit-test 
```

## 平分

使用`bisect`有几种方法。我们将告诉它测试何时通过，以及如何运行测试，剩下的事情就交给它了。

我们从`bisect start` :
开始

```
$ bisect_start 
```

告诉它我们当前的提交被破坏:

```
$ git bisect bad HEAD 
```

让我们找到一个我们知道测试通过的提交。比方说，引入测试的提交:

```
$ git blame my_script.py | grep test_product
d7af196a (oren 2019-06-24 12:48:35 -0700 8) def test_product(): 
```

告诉`bisect`这个提交是好的:

```
$ git bisect good d7af196a
Bisecting: 11 revisions left to test after this (roughly 4 steps)
[97c3a24f28f72bec1885445eb3947e446d0804fe] Unrelated commit # 10 
```

现在我们开始用一个运行测试的命令来分割，分割使用命令的退出代码来判断每个提交是好是坏，这很有效，因为如果测试失败，`pytest`有一个非零的退出代码(并且，`pytest`可以运行[一个特定的函数](https://docs.pytest.org/en/latest/usage.html) ):

```
git bisect run pytest -qqq my_script.py::test_product
....
....
....
d2a3327df55cb9bf9b43780f9adb223e93ba093d is the first bad commit
commit d2a3327df55cb9bf9b43780f9adb223e93ba093d
Author: oren <----@----.--->
Date:   Mon Jun 24 12:50:58 2019 -0700

    Some innocent change that certainly did not brake anything

:100644 100644 0be2cd4c2d0e450026507ee86e058785de56cadc 98c1b14d74c4f46b538df5cc1d32b9bb42afc791 M  my_script.py
bisect run success 
```

酷！

事实上，这是一个糟糕的承诺:

```
$ git show d2a3327df55cb9bf9b43780f9adb223e93ba093d
commit d2a3327df55cb9bf9b43780f9adb223e93ba093d
Author: oren <----@----.--->
Date:   Mon Jun 24 12:50:58 2019 -0700

    Some innocent change that certainly did not brake anything
.......
.......

 def product(factors):
-    return reduce(mul, factors)
+    return reduce(lambda x , y: x - y, factors) 
```

注意:如果您使用脚本作为命令来`git bisect run`。确保这个脚本没有被 git 跟踪，否则当 git 修改版本时它可能会改变。

干杯！