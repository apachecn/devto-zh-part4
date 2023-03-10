# Python 调试装饰器

> 原文：<https://dev.to/serhatteker/python-debug-decorators-56jj>

有时在调试时，我们喜欢打印/记录一个方法的所有输入和输出:`name, args, kwargs, dict`等等。

为此我们会有一些 IDE 特性，但有时我们需要手动调试。

在手动调试时，我不想把`logger.debug(<messsage>)`写到一个模块的两行中的每一行。

解决方法:`Python Decorators`

先配置一个`logger`。详情请见我们的帖子 [Python 日志配置](https://dev.to/serhatteker/python-logging-configuration-59pp)。然后为我们的`debugger decorators`
造一个模块

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
# utils/debuggers.py 

class Debugger(object):
    """ Debug a method and return it back"""

    enabled = False

    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        if self.enabled:
            logger.debug(f'Entering : {self.func.__name__}')
            logger.debug(f'args, kwargs : {args, kwargs}')
            logger.debug(f'{self.func.__name__} returned : {self.func(*args, **kwargs)}')

        return self.func(*args, **kwargs) 
```

Enter fullscreen mode Exit fullscreen mode

然后调用它，并像下面这样装饰你的方法:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
# main.py 
from utils.debuggers import Debugger

@Debugger
def my_func(a, b, c, d):
    return a + b + c + d

if __name__ == "__main__":
    Debugger.enabled = True

    args_dict = dict(
        a=1,
        b=2,
        c=5,
        d=-10
    )

    my_func(**args_dict) 
```

Enter fullscreen mode Exit fullscreen mode

输出如下:

```
2019-07-21 18:43:25,635 [DEBUG] __main__: Entering : my_func
2019-07-21 18:43:25,635 [DEBUG] __main__: args, kwargs: ((), {'a': 1, 'b': 2, 'c': 5, 'd': -10})
2019-07-21 18:43:25,635 [DEBUG] __main__: my_func returned -2 
```

Enter fullscreen mode Exit fullscreen mode

如果你想要一个函数而不是一个类来调试:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
# utils/decorators.py 
import functools

def debugmethod(func):
    """ Debug a method and return it back"""

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        return_value = func(*args, **kwargs)

        logger.debug(f'Calling : {func.__name__}')
        logger.debug(f'args, kwargs: {args, kwargs}')
        logger.debug(f'{func.__name__} returned {return_value}')

        return return_value

    return wrapper 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道方法的执行时间；

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
# utils/decorators.py 
import time

def timerun(func):
    """ Calculate the execution time of a method and return it back"""

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        duration = time.time() - start

        logger.debug(f"Duration of {func.__name__} function was {duration}.")
        return result
    return wrapper 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
2019-07-21 18:43:25,636 [DEBUG] __main__: Duration of my_func was 0.00023937225341796875. 
```

Enter fullscreen mode Exit fullscreen mode

你可以如下组合你的装饰者:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
# main.py 
from utils.debuggers import debugmethod, timerun

@timerun
@debugmethod
def my_func(a, b, c, d):
    return a + b + c + d

if __name__ == "__main__":
    Debugger.enabled = True

    args_dict = dict(
        a=1,
        b=2,
        c=5,
        d=-10
    )

    my_func(**args_dict) 
```

Enter fullscreen mode Exit fullscreen mode

输出应该是这样的:

```
2019-07-21 18:43:25,635 [DEBUG] __main__: Calling : my_func
2019-07-21 18:43:25,635 [DEBUG] __main__: args, kwargs: ((), {'a': 1, 'b': 2, 'c': 5, 'd': -10})
2019-07-21 18:43:25,635 [DEBUG] __main__: my_func returned -2
2019-07-21 18:43:25,636 [DEBUG] __main__: Duration of my_func was 0.00023937225341796875. 
```

Enter fullscreen mode Exit fullscreen mode

好了，都搞定了。