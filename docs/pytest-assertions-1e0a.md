# PyTest 断言

> 原文：<https://dev.to/xni/pytest-assertions-1e0a>

您可能已经看到 PyTest 如何报告错误:

```
def test_make_empty_file():
    name = "/tmp/empty_test"
    make_empty_file(name)
    with open(name, "r") as fp:
    > assert not fp.read()
    E assert not 'hello'
    E + where 'hello' = ()
    E      +  where = .read 
```

但是你知道它是怎么做到的吗？你见过这样的错误吗

```
assertion failed, but when it was re-run for printing intermediate values, it did not fail. 
```

我非常推荐阅读这篇文章