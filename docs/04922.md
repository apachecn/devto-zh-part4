# 简单(有效)的 python 类型检查器

> 原文：<https://dev.to/fpim/easy-and-effective-python-type-checker-2ol8>

# 问题

python 隐式处理了类型。虽然看起来很灵活，但是开发人员在管理一个大型项目时经常发现它会引起混乱，特别是对于那些来自强类型语言的项目。

## 标注

python 的新版本(3.5 以上)允许在函数定义中加入类型提示。
然而，python 不支持类型检查，根据 [PEP 484](https://www.python.org/dev/peps/pep-0484/#non-goals) ，这取决于 python 开发者实现
他们自己的运行时类型检查功能:

> 虽然提议的类型化模块将包含一些运行时类型检查的构建块——特别是 get_type_hints()函数——但是必须开发第三方
> 包来实现特定的运行时类型检查
> 功能，例如使用 decorators 或元类。使用类型提示
> 进行性能优化是留给读者的练习。

尽管有像 [mypy](http://mypy-lang.org/)
这样的开源库为你做类型检查，但如果你想避免一个成熟的库带来的不必要的依赖，本文旨在向你介绍你需要知道的最基本的知识(和一个技巧)
。

### 基本类型提示

下面的函数打算将两个整数作为参数，并返回它们的和，
您可以通过添加`:type`来指定函数参数的类型，通过添加`->type` :
来指定返回值的类型

```
def add(a:int, b:int)->int:
    return a + b

add(1,2)
# 3 
```

然而 python 对您传入的值的类型几乎没有做任何事情:

```
add('nah ', 'i dont care')
# 'nah i dont care' 
```

事实上 python 所做的是在函数的
`__annotations__`属性中添加了类型提示信息:

```
add.__annotations__
# {'a': <class 'int'>, 'b': <class 'int'>, 'return': <class 'int'>} 
```

访问魔法函数不好，有时它不能处理边缘情况，幸运的是`typing`模块带有一个
方便的函数`get_type_hints`供你访问对象的注释:

```
import typing
typing.get_type_hints(add)
# {'a': <class 'int'>, 'b': <class 'int'>, 'return': <class 'int'>} 
```

### 签名

要执行类型检查，您需要在运行时分析函数的签名。`inspect.signature`模块
为您提供了一个方便的实用程序([签名对象](https://docs.python.org/3/library/inspect.html#inspect.Signature))来完成这项工作。

```
import inspect
sig = inspect.signature(add)
sig
<Signature (a: int, b: int) -> int>
sig.bind_partial(1,2)
# <BoundArguments (a=1, b=2)> sig.bind_partial(b=2,a=2)
# <BoundArguments (a=2, b=2)> sig.bind_partial(b=2,a=2).arguments
# OrderedDict([('a', 2), ('b', 2)]) 
```

`signature`对象的`bind_partial`方法将参数映射到它们对应的签名，我们可以
使用它和注释信息来创建一个简单的函数装饰器，它进行类型检查:

```
from functools import wraps
def type_check(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    @wraps(fn)
    def wrapped(*args,**kwargs):
        if len(annotation) > 0:
            arguments = sig.bind_partial(*args,**kwargs).arguments
            assert all(isinstance(arguments[k],v) for k,v in annotation.items())
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return wrapped

@type_check
def add(a:int, b:int)->int:
    return a + b

add(1,2)
# 3 
add('1',2)
#Traceback (most recent call last):
#  File "<stdin>", line 1, in <module>
#  File "/Projects/aw/shit.py", line 37, in wrapped
#    assert all(isinstance(arguments[k],v) for k,v in annotation.items())
#AssertionError 
```

以上是你能创建的最基本的类型检查器，然而这个类型检查器使用了众所周知缓慢的`inspect`模块。

### 让我们计时吧

```
import inspect
from timeit import timeit
import typing
from functools import wraps

def type_check(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    @wraps(fn)
    def wrapped(*args,**kwargs):
        if annotation:
            arguments = sig.bind_partial(*args,**kwargs).arguments
            assert all(isinstance(arguments[k],v) for k,v in annotation.items())
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return wrapped

def useless_wrapper(fn):
    @wraps(fn)
    def wrapped(*args,**kwargs):
        return fn(*args,**kwargs)
    return wrapped

def add(a:int, b:int)->int:
    return a + b

base_add = useless_wrapper(add)
tc_add = type_check(add)

t= timeit('add(1,1)',
       setup='from __main__ import add', number=100000)
print('it takes ',t,' seconds to run add 100000 times')

t= timeit('base_add(1,1)',
       setup='from __main__ import base_add', number=100000)
print('it takes ',t,' seconds to run base_add 100000 times')

t= timeit('tc_add(1,1)',
       setup='from __main__ import tc_add', number=100000)
print('it takes ',t,' seconds to run tc_add 100000 times') 
```

结果:

```
#it takes  0.013391613000000024  seconds to run add 100000 times
#it takes  0.029804532999999994  seconds to run base_add 100000 times
#it takes  0.708789169  seconds to run tc_add 100000 times 
```

添加一个函数装饰器会增加一点开销，而类型检查器
会给原始函数带来巨大的开销。这是因为在本机 python 代码中，`bind_partial`方法
动态分析了`*args`和`**kwarg`将被映射到签名的位置，而在 c 中，实际函数调用的
`*args`和`**kwarg`的处理被优化了，现在如果我们可以利用这一点会怎么样呢？

### 钻营

```
fn_s = """
def magic_func {0}:
    {1}
                """

def type_check_fast(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    if annotation:
        assert_str = 'assert ' + ' and '.join(["isinstance({k},{v})".format(k=k,v=v.__name__) for k,v in annotation.items()])
        print('compiling:\n', fn_s.format(sig, assert_str))
        exec(fn_s.format(sig,assert_str))
        func = locals()['magic_func']
    @wraps(fn)
    def deced(*args,**kwargs):
        if annotation:
            func(*args,**kwargs)
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return deced 
```

是的，这里用的是`exec`。技巧是编译一个带有跟随目标函数的签名的函数，
并动态构造断言语句，这样字符串

```
fn_s = """
def magic_func {0}:
    {1}
                """ 
```

被格式化为:

```
#def magic_func (a: int, b: int) -> int:
#    assert isinstance(a,int) and isinstance(b,int) 
```

然后由`exec`语句对字符串进行评估。

在局部作用域中定义的新函数可以通过`locals()['magic_func']`访问。

让我们把它们放在一起:

```
import inspect
from timeit import timeit
import typing
from functools import wraps
fn_s = """
def magic_func {0}:
    {1}
                """

def type_check_fast(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    if annotation:
        assert_str = 'assert ' + ' and '.join(["isinstance({k},{v})".format(k=k,v=v.__name__) for k,v in annotation.items()])
        print('compiling:\n', fn_s.format(sig, assert_str))
        exec(fn_s.format(sig,assert_str))
        func = locals()['magic_func']
    @wraps(fn)
    def deced(*args,**kwargs):
        if annotation:
            func(*args,**kwargs)
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return deced

def type_check(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    @wraps(fn)
    def wrapped(*args,**kwargs):
        if annotation :
            arguments = sig.bind_partial(*args,**kwargs).arguments
            assert all(isinstance(arguments[k],v) for k,v in annotation.items())
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return wrapped

def useless_wrapper(fn):
    @wraps(fn)
    def wrapped(*args,**kwargs):
        return fn(*args,**kwargs)
    return wrapped

def add(a:int, b:int)->int:
    return a + b

base_add = useless_wrapper(add)
tc_add = type_check(add)
fast_tc_add = type_check_fast(add)

t= timeit('add(1,1)',
       setup='from __main__ import add', number=100000)
print('it takes ',t,' seconds to run add 100000 times')

t= timeit('base_add(1,1)',
       setup='from __main__ import base_add', number=100000)
print('it takes ',t,' seconds to run base_add 100000 times')

t= timeit('tc_add(1,1)',
       setup='from __main__ import tc_add', number=100000)
print('it takes ',t,' seconds to run tc_add 100000 times')

t= timeit('fast_tc_add(1,1)',
       setup='from __main__ import fast_tc_add', number=100000)
print('it takes ',t,' seconds to run fast_tc_add 100000 times') 
```

结果:

```
#compiling:
# 
#def magic_func (a: int, b: int) -> int:
#    assert isinstance(a,int) and isinstance(b,int)
#                
#it takes  0.013479943000000001  seconds to run add 100000 times
#it takes  0.030140912  seconds to run base_add 100000 times
#it takes  0.713209548  seconds to run tc_add 100000 times
#it takes  0.07377745000000002  seconds to run fast_tc_add 100000 times 
```

新型检查器比原来的快 100 倍。假设
添加一个“无用的”装饰器(调用一个额外的函数)会增加 0.017 秒的开销，
我们用`fast_tc_add`调用的两个额外的函数实现了 0.07 秒。

### Hack 2 -自动类型检查

给你写的每个函数都加一个装饰器是非常非常难看的。如果我们可以:

1.  在每个模块的末尾，访问在局部作用域中声明的所有变量。
2.  对于所有属于“当前”模块的变量，是函数类型:
3.  用 type_check 装饰器包装这些函数。

将以下内容另存为`tc.py` :

```
import inspect
import typing
from functools import wraps
fn_s = """
def magic_func {0}:
    {1}
                """

def type_check_fast(fn):
    sig = inspect.signature(fn)
    annotation = typing.get_type_hints(fn)
    return_type = annotation.pop('return',None)
    if annotation:
        assert_str = 'assert ' + ' and '.join(["isinstance({k},{v})".format(k=k,v=v.__name__) for k,v in annotation.items()])
        exec(fn_s.format(sig,assert_str))
        func = locals()['magic_func']
    @wraps(fn)
    def deced(*args,**kwargs):
        if annotation:
            func(*args,**kwargs)
        return_value = fn(*args,**kwargs)
        if return_type:
            assert isinstance(return_value,return_type)
        return return_value
    return deced

def auto_dec(name,dic_locals):
    for k,v in dic_locals.items():
        if hasattr(v,'__module__') and v.__module__ == name and inspect.isfunction(v):
            dic_locals[k] = type_check_fast(v) 
```

然后，在另一个`.py`文件中，在所有函数都被清除后，放入`auto_dec(__name__,locals())`:

```
from tc import auto_dec

def add(a:int,b:int)->int:
    return a+b

def otherfunc(a:int,b:int)->int:
    return a+b

def otherotherfunc(a:int,b:int)->int:
    return a+b

auto_dec(__name__,locals())

if __name__ == '__main__' :
    print(add(1,2))
    print(otherfunc(1,2))
    print(otherotherfunc('nah','got string')) 
```

结果:

```
3
3
Traceback (most recent call last):

  File "/Projects/aw/test.py", line 17, in <module>
    print(otherotherfunc('nah','got string'))
  File "/Projects/aw/tc.py", line 20, in deced
    func(*args,**kwargs)
  File "<string>", line 3, in magic_func
AssertionError 
```

这篇文章的源代码可以在这里找到[。](https://github.com/fpim/type_checker)