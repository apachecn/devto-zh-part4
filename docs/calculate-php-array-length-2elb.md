# 计算 PHP 数组长度

> 原文：<https://dev.to/techyhunger/calculate-php-array-length-2elb>

如何计算 **PHP 数组长度**？

如果您想计算数组中元素或值的总数，您可以使用 PHP 内置函数 ***count()*** 或 ***sizeof()*** 轻松完成。

你可以在下面的例子中找到如何计算 PHP 数组长度的详细解释。

## count():循环的 PHP 数组长度

PHP ***count()*** 函数的语法非常简单。

```
count(var array_or_countable, mode)
```

> 如果你不知道[什么是数组](https://www.php.net/manual/en/language.types.array.php)，那么请看 PHP 中如何创建和使用数组的详细解释。

### 数组或可数的

第一个参数是计算 PHP 数组长度所需的数组变量。如果第一个参数为空，或者没有设置空数组，那么***【count()】函数将返回 0。***

 **而且，如果指定了变量但其数据类型不是数组，那么，***【count()】函数将返回 1。***

 **为了避免这种情况，您可以使用 PHP 内置函数 isset()并检查是否设置了数组变量。

### 方式

第二个参数是可选的，可以接受两个值 COUNT_NORMAL 或 COUNT_RECURSIVE。您可以传递值 0 和 1，而不是 COUNT_NORMAL 和 COUNT_RECURSIVE，这将产生相同的效果。

如果没有值作为第二个参数传递，则第二个参数的默认值是 COUNT_NORMAL。

**例#1 计数()例**

```
$food = array('fruits' => array('orange', 'banana', 'apple'),
              'veggie' => array('carrot', 'collard', 'pea'));

// recursive count
echo count($food, COUNT_RECURSIVE); // output 8

// normal count
echo count($food); or count($food, COUNT_NORMAL ); // output 2
```

在上面的代码块中，我们创建了一个多维数组。

当我们在递归模式下使用 count 函数作为第二个参数时，输出是 8。

并且，当我们在正常模式下使用 count 函数作为第二个参数或默认模式时，输出是 2。

## sizeof()— PHP 数组长度？

sizeof()是 PHP count()函数的别名，接受与 count()相同的参数。

如果我们在上面的例子中用 sizeof()替换 count()，那么两个函数调用的结果将是相同的。

很多来自 C 语言背景的程序员都期望 sizeof()返回分配的内存量。但是，如上所述，sizeof()是 PHP 中 count()的别名。

建议始终使用 count()而不是 sizeof()。因为不能保证别名功能在将来的升级中是否存在。

因此，您应该始终考虑维护代码标准以及避免由于升级版本而中断应用程序。

#### 想要更多这样的教程？在这里了解[。](https://www.techyhunger.com/)****