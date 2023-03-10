# 如何使用反射测试私有和受保护的方法

> 原文：<https://dev.to/daniel_werner/how-to-use-reflection-to-test-private-and-protected-methods-3339>

使用 TDD 方法开发应用程序时，我们倾向于实现尽可能高的代码覆盖率，包括私有和受保护的方法。但是为非公共方法编写单元测试并不简单。为了单元测试而改变可见性并不是一个好主意。在这些情况下，我们可以使用 php 的反射来使这些方法变得可访问，让我们看看如何实现。

## 方法

从我们的类创建新的反射类:

```
$class = new \ReflectionClass('MyNamespace\MyClass'); 
```

Enter fullscreen mode Exit fullscreen mode

获取私有/受保护的方法:

```
$myProtectedMethod = $class->getMethod('myProtectedMethod'); 
```

Enter fullscreen mode Exit fullscreen mode

使其可访问:

```
$myProtectedMethod->setAccessible(true); 
```

Enter fullscreen mode Exit fullscreen mode

从我们的类中创建实例:

```
$myInstance = new MyClass(); 
```

Enter fullscreen mode Exit fullscreen mode

使用上面创建的实例的参数调用方法:

```
$result = $myProtectedMethod->invokeArgs($myInstance, [$argument1, $argument2]); 
```

Enter fullscreen mode Exit fullscreen mode

## 属性

同样，我们可以改变一个类的非公共属性的可见性。当我们想要单元测试边缘案例或错误处理，并且我们想要改变实例的内部状态来模拟错误行为时，这可能是有用的。

从我们的类创建新的反射类:

```
$class = new \ReflectionClass('MyNamespace\MyClass'); 
```

Enter fullscreen mode Exit fullscreen mode

获取私有/受保护的财产:

```
$myProtectedProperty = $class->getProperty(myProtectedProperty); 
```

Enter fullscreen mode Exit fullscreen mode

使其可访问:

```
$myProtectedProperty->setAccessible(true); 
```

Enter fullscreen mode Exit fullscreen mode

从我们的类中创建实例:

```
$myInstance = new MyClass(); 
```

Enter fullscreen mode Exit fullscreen mode

在创建的实例上设置属性的值:

```
$myProtectedProperty->setValue($myInstance, 'value'); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 php 文档中找到更多关于反射的信息:[https://www.php.net/manual/en/book.reflection.php](https://www.php.net/manual/en/book.reflection.php)

帖子[如何使用反射测试私有和受保护的方法](https://42coders.com/how-to-use-reflection-to-test-private-and-protected-methods/)最早出现在 [42 Coders](https://42coders.com) 上。