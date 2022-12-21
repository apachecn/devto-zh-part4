# PHP 中的箭头函数

> 原文：<https://dev.to/ri5hirajp/arrow-functions-in-php-3c2c>

灵感来源于 Javascript 的箭头函数，我猜？
[es6-深度箭头-功能](https://hacks.mozilla.org/2015/06/es6-in-depth-arrow-functions/)

被称为箭头函数的短闭包是`PHP 7.4`将带来的长期功能之一。它是由 Nikita Popov，Levi Morrison 和 Bob Weinand 提出的，你可以[在这里](https://wiki.php.net/rfc/arrow_functions_v2)阅读 RFC 原文

**快速举例摘自[主义 DBAL](https://github.com/doctrine/dbal/blob/master/lib/Doctrine/DBAL/Schema/PostgreSqlSchemaManager.php)**

```
//old way
$this->existingSchemaPaths = array_filter($paths, function ($v) use ($names) {
    return in_array($v, $names);
});

//new way with arrow function
$this->existingSchemaPaths = array_filter($paths, fn($v) => in_array($v, $names)); 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们过一遍规则

1.  `fn`是关键字，不是保留的函数名。
2.  它只能有一个表达式，那就是 return 语句。
3.  不需要使用`return`和`use`关键字。
4.  `$this`变量，作用域和 LSB 作用域自动绑定。
5.  您可以对参数和返回类型进行类型提示。
6.  你甚至可以使用引用`&`和[展开运算符](https://www.php.net/manual/en/functions.arguments.php#functions.variable-arg-list)和`...`

## 几个例子

```
 //scope example
$discount = 5;
$items = array_map(fn($item) => $item - $discount, $items);

//type hinting
$users = array_map(fn(User $user): int => $user->id, $users);

//spread operator
function complement(callable $f) {
    return fn(...$args) => !$f(...$args);
}

//nesting
$z = 1;
$fn = fn($x) => fn($y) => $x * $y + $z;

//valid function signatures
fn(array $x) => $x;
fn(): int => $x;
fn($x = 42) => $x;
fn(&$x) => $x;
fn&($x) => $x;
fn($x, ...$rest) => $rest; 
```

Enter fullscreen mode Exit fullscreen mode

## 未来范围

1.  多行箭头功能
2.  允许类中的实函数使用箭头符号。

```
 //valid now
class Test {
    public function method() {
        $fn = fn() => var_dump($this);
        $fn(); // object(Test)#1 { ... }

        $fn = static fn() => var_dump($this);
        $fn(); // Error: Using $this when not in object context
    }
}

//maybe someday in future
class Test {
    private $foo;
    private $bar;

    fn getFoo() => $this->foo;
    fn getBar() => $this->bar;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我最爱的外卖

1.  回调时间可以更短
2.  不需要`use`关键字，可以访问变量。

> 让我知道你对这些更新的看法，以及你最喜欢的收获是什么？

下次再见， [rishiraj purohit](https://rishirajpurohit.in)