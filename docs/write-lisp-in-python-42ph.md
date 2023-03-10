# 用 python 写 lisp

> 原文：<https://dev.to/mak12776/write-lisp-in-python-42ph>

最近在思考如何将 lisp 编程语言和 python 融合，尝试用 python 写一个类似编译器的 lisp 语法。一些可以转换如下 lisp 代码的东西:

```
(repeat 2 (write-ln (+ 10 20))) 
```

Enter fullscreen mode Exit fullscreen mode

到此树:

```
word: "repeat"
    int: 2
    word: "write-ln"
        symbol: +
            int: 10
            int: 20 
```

Enter fullscreen mode Exit fullscreen mode

然后编写一个执行树的执行器。

但是我发现我不需要编写 lisp 语法解析器。相反，我可以直接使用 python 内置类型。

例如:

```
('repeat', ('print', ('add', 10, 20))) 
```

Enter fullscreen mode Exit fullscreen mode

然后我思考的结果是我写了这个简单的`python builtins types compiler`。(`pbtc`为速记)

```
#!/usr/bin/python3 
# filename: main.py 
import itertools
import sys

def add(*args):
    result = args[0] if len(args) != 0 else None
    for arg in args[1:]:
        result += arg
    return result

def sub(*args):
    result = args[0] if len(args) != 0 else None
    for arg in args[1:]:
        result -= arg
    return result

def mul(*args):
    result = args[0] if len(args) != 0 else None
    for arg in args[1:]:
        result *= arg
    return result

def div(*args):
    result = args[0] if len(args) != 0 else None
    for arg in args[1:]:
        result /= arg
    return result

def true_div(*args):
    result = args[0] if len(args) != 0 else None
    for arg in args[1:]:
        result //= arg
    return result

def join(sep, args):
    return sep.join(args)

tuple_compilers = {
    'add': add,
    'sub': sub,
    'mul': mul,
    'div': div,
    'tdiv': true_div,
    'print': lambda *args: print(*args),

    'join': join,
    'repeat': itertools.repeat,
}

def compile_tuple(tree, memory, compile):
    if len(tree) == 0:
        raise ValueError('invalid tuple length: {}'.format(len(tree)))
    if not isinstance(tree[0], str):
        raise ValueError('invalid tuple instruction: {}'.format(tree[0]))
    if tree[0] not in tuple_compilers:
        raise ValueError('unknown tuple instruction: {}'.format(tree[0]))
    args = []
    for node in tree[1:]:
        args.append(compile(node, memory))
    return tuple_compilers[tree[0]](*args)

compilers = {
    tuple: compile_tuple,
    (list, dict, str, int, float, bytes): 
        lambda tree, memory, compile: tree,
}

def compile_tree(tree, compilers, memory = None):
    self = lambda tree, memory: compile_tree(tree, compilers, memory)
    for _type, compile in compilers.items():
        if isinstance(tree, _type):
            return compile(tree, memory, self)
    raise TypeError(type(tree).__name__)

with open(sys.argv[1]) as infile:
    globals_dict = {}
    exec('grammar = ' + infile.read())

compile_tree(grammar, compilers) 
```

Enter fullscreen mode Exit fullscreen mode

它不安全，速度慢，但是有效。

**测试:**

```
 # filename: lispy 
('print', 
    ('join', ' ', ('repeat', ('join', ' ', ['hi', 'bye']), 10))
    ) 
```

Enter fullscreen mode Exit fullscreen mode

**`$./main.py lispy`:**T3】

```
hi bye hi bye hi bye hi bye hi bye hi bye hi bye hi bye hi bye hi bye 
```

Enter fullscreen mode Exit fullscreen mode

很有趣...

也许后来，我写了一个更完整的版本，并在某个地方发布，作为开源。