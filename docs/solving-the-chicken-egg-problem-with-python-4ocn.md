# 用 Python 解决“鸡生蛋”问题

> 原文：<https://dev.to/xni/solving-the-chicken-egg-problem-with-python-4ocn>

你好！

对 Python 来说，没有什么比鸡肉和鸡蛋更美味了。所以今天我们的 Python 决定解决一个古老的哲学问题:先有鸡还是先有蛋？为了做到这一点，它决定创建两个类，然后执行一些建模，以最终回答这个问题。

好，我们开始吧。我们的蟒蛇是所有蟒蛇中的佼佼者，它的序列号是 2.7。首先，它创建了 3 个文件:

```
# filename: models/egg.py

import models.chicken

class Egg(object):
    def __init__(self, name):
        super(Egg, self).__init__()
        self.name = name

    def wait(self):
        return models.chicken.Chicken("Chicken from [{self.name}]".format(self=self)) 
```

```
# filename: models/chicken.py

import models.egg

class Chicken(object):
    def __init__(self, name):
        super(Chicken, self).__init__()
        self.name = name

    def create_egg(self):
        return models.egg.Egg("Egg of [{self.name}]".format(self=self)) 
```

```
# filename: main.py

import models.chicken

if __name__ == '__main__':
    # Decision maker!
    c = models.chicken.Chicken("Ryaba")
    print(c.create_egg().wait().create_egg().name) 
```

Grand-pa Python 对结果很满意:

```
$ python2.7 main.py 
Egg of [Chicken from [Egg of [Ryaba]]] 
```

对于那些对 Python 中的循环依赖持怀疑态度的人来说——是的，它们是受支持的，但只是在一定程度上。通常，当解释器看到`import models.egg`时，它检查这个模块是否被导入，如果是，就使用缓存中这个模块的地址。如果不是，它会立即在缓存中创建一条记录，然后开始实际的导入。这就是为什么如果你的代码中只有`import <module_name>`语句，你是安全的。

一旦我们决定使用`from <module_name> import <object>`语句，我们的循环依赖将无法被解析。我们试试吧！

```
# filename: models/egg.py

from models.chicken import Chicken

class Egg(object):
    def __init__(self, name):
        super(Egg, self).__init__()
        self.name = name

    def wait(self):
        return Chicken("Chicken from [{self.name}]".format(self=self)) 
```

```
# filename: models/chicken.py

from models.egg import Egg

class Chicken(object):
    def __init__(self, name):
        super(Chicken, self).__init__()
        self.name = name

    def create_egg(self):
        return Egg("Egg of [{self.name}]".format(self=self)) 
```

```
$ python2.7 main.py 
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    import models.chicken
  File "/data/models/chicken.py", line 1, in <module>
    from models.egg import Egg
  File "/data/models/egg.py", line 1, in <module>
    from models.chicken import Chicken
ImportError: cannot import name Chicken

$ python3.7 main.py 
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    import models.chicken
  File "/data/models/chicken.py", line 1, in <module>
    from models.egg import Egg
  File "/data/models/egg.py", line 1, in <module>
    from models.chicken import Chicken
ImportError: cannot import name 'Chicken' from 'models.chicken' (/data/models/chicken.py) 
```

这或多或少是可以预测的，这是一个真正的先有鸡还是先有蛋的问题。为了将`Chicken`类导入到`egg`模块中，你需要解析`chicken`模块(因此，仅仅一个模块的地址是不够的)，为了完全解析`chicken`模块，你需要完成对`egg`模块的解析。所以，没门。

好的。现在我需要你们的全部注意力:我们将把`import <package>.<module>`替换成`from <package> import <module>`。

```
# filename: models/egg.py

from models import chicken

class Egg(object):
    def __init__(self, name):
        super(Egg, self).__init__()
        self.name = name

    def wait(self):
        return chicken.Chicken("Chicken from [{self.name}]".format(self=self)) 
```

```
# filename: models/chicken.py

from models import egg

class Chicken(object):
    def __init__(self, name):
        super(Chicken, self).__init__()
        self.name = name

    def create_egg(self):
        return egg.Egg("Egg of [{self.name}]".format(self=self)) 
```

而且结果有点出乎意料:

```
$ python2.7 main.py 
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    import models.chicken
  File "/data/models/chicken.py", line 1, in <module>
    from models import egg
  File "/data/models/egg.py", line 1, in <module>
    from models import chicken
ImportError: cannot import name chicken

$ python3.7 main.py 
Egg of [Chicken from [Egg of [Ryaba]]] 
```

因此，Python3 改变了导入包的方式，通常它们比 Python2 更早地附加到 sys.modules 上。很高兴知道。

今天最后一个惊喜。现在我们使用`import <package>.<module> as <alias>`语法。

```
# filename: models/egg.py

import models.chicken as chicken

class Egg(object):
    def __init__(self, name):
        super(Egg, self).__init__()
        self.name = name

    def wait(self):
        return chicken.Chicken("Chicken from [{self.name}]".format(self=self)) 
```

```
# filename: models/chicken.py

import models.egg as egg

class Chicken(object):
    def __init__(self, name):
        super(Chicken, self).__init__()
        self.name = name

    def create_egg(self):
        return egg.Egg("Egg of [{self.name}]".format(self=self)) 
```

你能预测产量吗？

```
$ python2.7 main.py 
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    import models.chicken
  File "/data/models/chicken.py", line 1, in <module>
    import models.egg as egg
  File "/data/models/egg.py", line 1, in <module>
    import models.chicken as chicken
AttributeError: 'module' object has no attribute 'chicken'

$ python3.6 main.py 
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    import models.chicken
  File "/data/models/chicken.py", line 1, in <module>
    import models.egg as egg
  File "/data/models/egg.py", line 1, in <module>
    import models.chicken as chicken
AttributeError: module 'models' has no attribute 'chicken'

$ python3.7 main.py 
Egg of [Chicken from [Egg of [Ryaba]]] 
```

Python3.7 赢了:)好的。这里真正发生的是在 3.7 版本中`import A.B.C as D`语法中有一个[错误](https://bugs.python.org/issue30024)被[修复了](https://github.com/python/cpython/pull/3217/files)。

感谢阅读！