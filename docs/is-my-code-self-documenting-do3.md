# 我的代码是自文档化的吗？

> 原文：<https://dev.to/hamatti/is-my-code-self-documenting-do3>

所以你知道有些人怎么说“好的代码文档本身”？

这是他们的意思吗？

```
def self_documenting_sum(a, b):
    with open(__file__, 'r') as source:
        data = source.read().split('\n')

    with open(__file__, 'w') as source:
        if not data[1].startswith('    "'):
            documentation = '    """Sums up two integers"""'
            new_source = data[:]
            new_source.insert(1, documentation)
            source.write('\n'.join(new_source))
        else:
            source.write('\n'.join(data))
    return a + b

print(self_documenting_sum(2,5)) 
```

Enter fullscreen mode Exit fullscreen mode

(这是一个 Python 脚本，在第一次运行时将 docstring 添加到它唯一的函数中。)

**编辑**:我在[hamatti/自我记录代码](https://github.com/Hamatti/self-documenting-code)中对这个想法做了一点扩展