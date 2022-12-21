# 看看 PHP 的 isset()

> 原文：<https://dev.to/liamhammett/a-look-at-php-s-isset-2bc6>

[`isset()`](https://www.php.net/manual/en/function.isset.php) 是 PHP 中验证数据的最重要的工具之一。顾名思义，它被设计用来验证给它的变量是否被设置，并根据结果返回一个布尔值。

然而，它有一些非常值得了解的怪癖和行为，因为它们可以很容易地抓住甚至有经验的开发人员。

让我们来看看它是如何表现的，它有什么特别之处。即使你是一个经验丰富的 PHP 开发人员，希望你能在这里学到一些新东西。

# 这是一种语言结构，不是一种功能

尽管看起来很像，但`isset()`实际上并不像你想象的那样是一个典型的函数。

就像 die()、array()、print()和其他函数一样，它是一个“语言构造”，这是一个有趣的术语，用外行人的话来说，意味着它直接内置在 PHP 引擎中，可以有一些不同于典型内置或用户定义函数的特殊行为，我们接下来将讨论这些行为。

# 它不能作为可调用的

任何内置或用户定义的函数都可以用作“可调用”函数指针，以便动态调用和用于 currying 之类的模式。

```
is_callable('strtoupper');
// true

array_map('strtoupper', ['a', 'b', null, 'd']);
// ['A', 'B', '', 'D'] 
```

因为它是一种语言构造，而不是真正的函数，所以它是不可调用的，也不能以这种方式使用。

```
is_callable('isset');
// false

array_map('isset', ['a', 'b', null, 'd']);
// PHP Warning:  array_map() expects parameter 1 to be a valid callback, function 'isset' not found or invalid function name… 
```

# 它不接受表达式

虽然常规函数和其他语言构造可以接受任何表达式的结果，但由于其独特的性质，`isset()`只能接受变量、数组键或对象属性作为参数。

试图以任何其他方式使用它都会导致致命错误。

```
if (isset('Hello world')) {
    // Fatal error: Cannot use isset() on the result of an expression (you can use "null !== expression" instead)
}

if (isset($a->b())) {
    // Fatal error: Cannot use isset() on the result of an expression (you can use "null !== expression" instead)
}

if (isset(! $a)) {
    // Fatal error: Cannot use isset() on the result of an expression (you can use "null !== expression" instead)
}

if (isset(CONSTANT)) {
    // Fatal error: Cannot use isset() on the result of an expression (you can use "null !== expression" instead)
} 
```

# 它还检查一个值是否为`null`

如果变量未定义或其值为`null`，则`isset()`将返回`false`。如果`null`是一个你已经*设定*并且想要允许的合适值，这可能会让你迷惑。

```
$value = null;

if (isset($value)) {
    // ...
} 
```

根据您的需求，检查这一点的一种方法是用`get_defined_vars()`检查变量是否在当前作用域中定义，并检查结果数组的键。

```
$value = null;

if (array_key_exists('value', get_defined_vars())) {
    // ...
} 
```

# 它可以接受多个参数

经常可以看到人们将调用链接在一起，逐个检查多个值的设置。

```
if (isset($a) && isset($b) && isset($c)) {
    // ...
} 
```

然而，`isset()`是一个变量函数，它可以一次接受任意数量的参数以达到相同的效果，确认是否设置了所有传递变量的*和*。

这是缩短长条件句的好方法。

```
if (isset($a, $b, $c)) {
    // ...
} 
```

# 不触发“未定义变量/索引/属性”通知

如果您正在检索嵌套多层的值，您可能希望确保链的每一步都存在。

```
if (isset($response, $response->list, $response->list['results'], $response->list['results'][0])) {
    // ...
}

if (isset($arr[$key], $otherArr[$arr[$key]], $otherArr[$arr[$key]][$otherKey])) {
    // ...
} 
```

然而，`isset()`不会触发任何“未定义的变量”、“未定义的索引”或“未定义的属性”通知，无论你通过多少层。

这意味着无需在每个单独的步骤中确认价值，所有这些都可以在一次检查中完成:

```
if (isset($response->list['results'][0])) {
    // ...
}

if (isset($otherArr[$arr[$key]][$otherKey])) {
    // ...
} 
```

# 【未定义方法】错误*做*得到触发

如果一个被检查的链恰好在中途包含一个方法调用，PHP 将尝试调用这个方法。

这意味着，如果链的早期部分不存在，或者链中的最后一个值是一个不具有此方法的对象，仍然会触发错误。

```
$a = new stdClass();

if (isset($a->b()->c)) {
    // Fatal error: Uncaught Error: Call to undefined method A::b()…
}

if (isset($a->b->c())) {
    // Fatal error: Uncaught Error: Call to a member function d() on null…
} 
```

处理这个问题的一个方法是在你的条件检查中显式化，停止这个链并在每次需要的时候调用`method_exists()`来验证这个方法是否存在。

```
if (isset($a) && method_exists($a, 'b') && isset($a->b()->c)) {
    // ...
} 
```

缩短这样一个表达式的一种方法是使用[错误控制操作符](https://www.php.net/manual/en/language.operators.errorcontrol.php)，它抑制单个表达式的任何错误。如果触发了错误，操作符将使表达式返回`null`并继续执行。

```
if (@$a->b()->c !== null) {
    // ...
} 
```

然而，虽然这可能很方便，但是您应该知道错误控制操作符非常低效，并且还会隐藏在您调用的被调用方法中触发的错误，而您并不打算隐藏这些错误。它不是对`isset()`的彻底替代。

# **！空()不太一样**

`[empty()](https://www.php.net/manual/en/function.empty.php)`也是一个与`isset()`行为相似的语言结构，因为它不会触发未定义的通知。

```
$a = [];

if (empty($a['b']->c)) {
   // ...
} 
```

看起来好像是`isset()`的正逆，但事实并非如此。`empty()`也可以接受表达式作为它的参数，但更重要的是，**它会键入杂耍**，这样任何 falsey 值都会被这样处理。

```
$a = '0';

if (isset($a)) {
    // It IS set
}

if (empty($a)) {
    // It IS empty
} 
```

# Null 合并运算符

在没有设置变量的情况下，希望提供一个回退值是非常常见的。这通常是通过一个简短的条件语句或三元子句来完成的。

```
$result = isset($value) ? $value : 'fallback'; 
```

从 PHP 7.0 开始，可以使用 null coalesce 操作符(`??`)来缩短这个值，如果设置了这个操作符，它将返回第一个值，如果没有设置，则返回第二个值。

```
$result = $value ?? 'fallback'; 
```

如果不是返回一个新值，而是你不想设置一个新的变量来做这件事，这也包括在内。从 PHP 7.4 开始，null coalesce 赋值操作符(`??=`)允许以更短的方式将变量设置为回退，如果它还没有被设置的话。

```
$value ??= 'fallback'; 
```

# 它不评价`__get()`的魔法方法

让我们假设我们有一个非常典型的类，它可以通过使用神奇的方法`__get()`检索一个值来动态获取属性。

```
class Person
{
    protected $attributes = [];

    public function __get($name)
    {
        return $this->attributes[$name] ?? null;
    }

    public function __set($name, $value)
    {
        $this->attributes[$name] = $value;
    }
} 
```

如果我们使用这个类来设置一个属性，我们可以像平常一样使用它。但是，如果我们检查值是否被设置，它将返回`false`

```
$person = new Person();
$person->name = 'Liam';

echo $person->name; // 'Liam'

isset($person->name); // false 
```

等等，这是怎么回事？！

因为`isset()`是一个语言构造，而不是一个常规函数，所以表达式在传递给它之前不会被求值。因为`name`不是对象上的真实属性，所以*并不真正*存在。

然而，当`isset()`在一个不存在的或者对于当前作用域不可访问的属性(比如受保护的或者私有的)上被调用时，它将调用一个神奇的`__isset()`方法，如果这个类已经定义了一个方法的话。这允许自定义逻辑来确定我们是否认为我们正在检查的属性是根据我们自己的规则设置的。

```
class Person
{
    // ...

    public function __isset($name)
    {
        return isset($this->attributes[$name]);
    }
} 
```

随着这一点的实现，一切都像预期的那样工作。

```
$person = new Person();
$person->name = 'Liam';

isset($person->name); // true
isset($person->somethingElse); // false 
```

需要注意的是，如果您正在检查嵌套的属性，那么对于链中的每个属性，`__isset()`将被适当地调用。

# 你*可以*把不存在的变量传递给 userland 函数

正如我们已经讨论过的，因为`isset()`实际上是一个语言构造，它因为 PHP 内核而具有特殊的行为，因此它的行为不像我们自己定义的函数。

然而，通过使用*引用*，我们可以在用户域函数中实现类似的效果。这样做，我们就有可能在常规语言结构的基础上公开我们自己选择的附加功能。

一个实际的例子可能是将任何实现了空对象模式的对象视为假值。

```
interface NullObject {}

class Logger {
    // ...
}

class NullLogger extends Logger implements NullObject {
    // ...
}

function is_truthy(&$value)
{
    if ($value instanceof NullObject) {
        return false;
    }

    return (bool) $value;
}

is_truthy($a);
// false

$b = '';
is_truthy($b);
// false

$c = '1';
is_truthy($c);
// true

$logger = new Logger();
is_truthy($logger);
// true

$nullLogger = new NullLogger();
is_truthy($nullLogger);
// false 
```

然而，引用使用起来并不总是那么安全，因为简单地使用它们*会*影响原始值，即使函数没有显式地这样做。

例如，任何未定义的数组键或属性将被自动赋值，它们的值被设置为`null`

```
$a = [];

is _ truthy($ a[' b '][' c '])；
 //假

var _ dump($ a)；
//[
//' b ' =>[
//' c ' =>null，
 // ]，
 // ] 
```

# 
 [T3】
结论](#conclusion) 

希望通过查看`isset()`、它的行为和其他相关的东西，你会学到一些东西，帮助你使你的代码更干净、更明确，并且当你需要检查一个变量是否已经被设置时，不会陷入边缘情况。