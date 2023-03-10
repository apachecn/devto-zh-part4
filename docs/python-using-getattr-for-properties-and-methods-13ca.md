# Python:对属性和方法使用 getattr

> 原文：<https://dev.to/adriennedomingus/python-using-getattr-for-properties-and-methods-13ca>

<figure>[![](img/cfff64cbe407f403d5d721038fa644d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cOFs_f14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9xv8X9df2wojULtL4Gx9sg.jpeg) 

<figcaption>照片由[émile Perron](https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

### 使用 getattr

在我们开始讨论 getattr 如何成为一个有用的工具来干燥我们的代码之前，让我们先了解一下它是如何工作的(docs [here](https://docs.python.org/3/library/functions.html#getattr) )！简而言之，它接受一个对象和一个属性名的字符串版本，并返回该属性的值(如果它存在于对象上)。如果在对象上找不到属性，它可以选择返回一个默认值，否则将返回一个 AttributeError。

好的，让我们看一个例子，使用一个基本的类。给定如下定义的人员类别:

```
class Person(object):
    def __init__(self, first_name, last_name):
    self.first_name = first_name
    self.last_name = last_name

    def say_hello(name):
        print "Hello, {}!".format(name) 
```

好的，让我们得到一个人的实例:

```
adrienne = Person("Adrienne", "Domingus") 
```

一旦我被实例化，我们就可以使用更熟悉的点符号来访问我的`first_name`(和`last_name`)属性，但是我们也可以使用`getattr`来访问*属性，结果是一样的:* 

```
>>> adrienne.first_name
'Adrienne'
>>> getattr(adrienne, ‘first_name’)
‘Adrienne’ 
```

这是有意义的，因为 _name 属性是…属性。现在有趣的部分来了:你知道类的方法也是属性吗？例如:

```
>>> getattr(adrienne, 'say_hello')
<bound method Person.say_hello of <Person object at 0x10640de50>> 
```

现在，这看起来不太有趣，它实际上没有做任何事情。但它的强大之处在于，我们可以将返回值存储在一个变量中，然后调用它。像这样:

```
>>> method = getattr(adrienne, 'say_hello')
>>> method("Someone else")
Hello, Someone else! 
```

### 投入使用:一般化方法调用

好吧，你可以使用`getattr`来获得方法和属性，这很酷，但是这对我们有什么帮助呢？如果你对分支方法调用有一些共同的逻辑，这对于保持你的代码干爽肯定是有用的。

例如，如果我们需要进行 API 调用，但是我们使用哪种方法取决于上下文，而不是这样做:

```
if http_method == 'post':
    requests.post(url, data=data)
elif http_method == 'put':
    requests.put(url, data=data) 
```

我们可以这样做:

```
method = getattr(requests, http_method)
method(url, data=data) 
```

这个例子中的差别可能看起来有点微不足道，但是对于具有比这个例子多得多的方法选项的对象，依赖于 if 和 elif 语句的初始方法伸缩性特别差。`getattr`来救援了！*