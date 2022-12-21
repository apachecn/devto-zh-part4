# 用大小写相等运算符(===)确定对象的类

> 原文：<https://dev.to/spitsman/determining-class-of-an-object-with-case-equality-operator-4ocl>

如果被传递的类在被传递对象的类的祖先列表中，Ruby 中的大小写相等运算符(或三重等于，`===`)返回 true:

```
1.class.ancestors # [Integer, Numeric, Object, ...]
Numeric === 1 # true
Object === 1 # true 
```

所以可以用它来确定物体的类别:

```
String === 'abc' # true
'abc'.class #=> String 
```

在上述案例中，案例相等运算符的工作方式类似于`#kind_of?`(或`#is_a?` ):

```
1.kind_of?(Integer) # true
1.is_a?(Numeric) # true 
```

上面的类有不同的`===`操作符的实现，这就是比较结果不同的原因:

```
String.===('abc') # the same as String === 'abc' 
```

这也意味着参数的顺序很重要:

```
1 === Integer # false 
```