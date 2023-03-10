# 使用 assertLogs 测试 Python 日志

> 原文：<https://dev.to/toti1212/use-assertlogs-to-test-python-logs-43j5>

众所周知，如果我们正确使用的话，原木可以拯救我们。在大多数情况下，创建正确的日志并将其添加到正确的位置对于分析我们的系统流程至关重要。另一方面，我们也知道，为了问心无愧地睡个好觉，我们必须测试我们的代码。

集合这些世界的精华，我们有一个好的实践，可以帮助我们确保如果我们遵循特定的流程路径，日志将被写入。
想象一下，如果一个结果是假的，您想要记录，并且您想要确保明天它将在日志文件中。为了确保没有你的同意，没有人会改变这种行为，或者可能，在未来被你自己删除。你将有一个测试，指责这一行动。

如果你是 Python 开发者，好消息！它有一个出色的`assert`功能来做到这一点！
我不知道这个的存在。我认为在你即将到来的考试中考虑它是一个好主意。

这里有一个虚拟的例子:

```
import logging

logger = logging.getLogger('backend')

# ... 
def foo(self, num):
    if num == 1:
        logger.warning('One is not acepted')
        return False
    # Do somenthing ...
    return True

# ... 
def test_foo(self):
    with self.assertLogs('backend', level='INFO') as cm:
        result = self.foo(1)
    self.assertFalse(result)
    self.assertIn('WARNING:backend:One is not acepted', cm.output) 
```

您可以在此阅读更多信息:

*   [单元测试文档-资产日志](https://docs.python.org/3/library/unittest.html#unittest.TestCase.assertLogs)