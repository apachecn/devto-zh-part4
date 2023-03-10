# PHP 中静态的力量

> 原文：<https://dev.to/ryancco/the-power-of-static-in-php-4130>

# 静的力量

虽然大多数人可能都见过 PHP 中使用的`static`关键字，但您是否想过它有多少种不同的用法？或者，这些不同的用法可能有多少种方式可以为您的代码提供价值？让我们深入使用和例子并找出答案。

## 方法签名

可以说，static 关键字最常见的用法是 static 方法。虽然可以使用对象操作符(`->`)引用静态方法，但建议使用范围解析操作符(`::`)，因为这种替代方法已被弃用，将来可能会被删除。范围解析运算符允许您直接调用类的静态方法，而不是类的实例。这也导致`$this`关键字在静态方法体中不可用。

静态方法可以用来实现[工厂方法模式](https://designpatternsphp.readthedocs.io/en/latest/Creational/FactoryMethod/README.html)，它作为一个工厂，在每次调用方法时产生包含类的新实例。

在这个例子中，我们创建了一个工厂方法`fromArray`，它实例化了一个`User`对象，从数组中分配实例值并返回实例。

```
class User
{
    public static function fromArray($attributes)
    {
        $user = new User();
        $user->name = $attributes['first'] . ' ' . $attributes['last'];
        $user->password = password_hash($attributes['password'], PASSWORD_BCRYPT);

        return $user;
    }
}

$user = User::fromArray([
    'first' => 'ryan',
    'last' => 'cco',
    'password' => 'password'
]); 
```

Enter fullscreen mode Exit fullscreen mode

或者，这个逻辑可以提取到一个独立的类中，这个类也被称为[静态工厂模式](https://designpatternsphp.readthedocs.io/en/latest/Creational/StaticFactory/README.html)。

```
class UserFactory
{
    public static function build($attributes)
    {
        //
    }
}

$user = UserFactory::build($attributes); 
```

Enter fullscreen mode Exit fullscreen mode

## 属性

与常规属性不同，在程序执行期间更改静态属性的值会影响包含类的所有实例。即使是那些还没有被实例化的。这样，静态属性可以被认为是“可变的类常量”。静态属性只能使用范围解析运算符引用。

由于静态属性的性质，它们可以用来实现[单例模式](https://designpatternsphp.readthedocs.io/en/latest/Creational/Singleton/README.html)。单例模式在整个程序执行过程中维护一个给定类的单个实例。

在这个例子中，对`Queue::getInstance()`的初始调用将创建一个`Queue`的实例并将其分配给`Queue::$instance`并返回。每个后续调用都将返回先前分配给`Queue::$instance`的`Queue`的相同实例。

```
class Queue
{
    private static $instance;

    public static function getInstance()
    {
        if (static::$instance === null) {
            static::$instance = new Queue();
        }

        return static::$instance;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 变量

在函数的上下文中，即使程序已经移出了包含函数的范围，静态变量仍然保持它们的值。当在类方法的上下文中时，静态变量具有类似于静态属性的额外行为，因为它们的值的改变反映了包含类的所有实例。尽管静态变量的行为类似于静态属性，但它只能在函数或方法体中访问。

静态变量经常被用作被称为[记忆化](https://eddmann.com/posts/implementing-and-using-memoization-in-php)的优化技术的一部分。内存化旨在通过缓存结果并保存它们以供以后用相同的参数调用，来加速一个昂贵的操作。

在本例中，我们创建了一个散列，它对于所提供的参数是唯一的，并使用它来唯一地将调用标识为`$key`。如果在`$cache`上没有找到作为索引的`$key`的值，我们执行`preg_replace`并将其输出存储在`$cache`的`$key`索引上。使用相同参数对`replace`的每个后续调用都将绕过对`preg_replace`的调用，并返回上一次调用的值。

```
function replace($pattern, $replacement, $subject)
{
    static $cache = [];

    $key = md5(serialize(func_get_args()));

    if (! isset($cache[$key])) {
        $cache[$key] = preg_replace(
            $pattern, $replacement, $subject
        );
    }

    return $cache[$key];
} 
```

Enter fullscreen mode Exit fullscreen mode

## 匿名函数

像方法一样，当一个匿名函数在一个类的上下文中被定义时，它们不被绑定到包含类，并且没有可用的关键字`$this`。

静态匿名函数不一定比常规匿名函数有任何独特的用途。值得注意的是，在一个类的上下文中，静态匿名函数可以提高微性能，并且可以在任何不需要将包含类绑定到匿名函数的情况下使用。

```
class PostsCollectionFilter
{
    public function unpublished($posts)
    {
        return $posts->filter(static function ($post) {
            return ! $post->published;
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 后期静态绑定(LSB)

[后期静态绑定](https://www.php.net/manual/en/language.oop5.late-static-bindings.php)演示了静态关键字的另一种用法:在继承的上下文中。在这个上下文中，`static`指的是被调用的类，而不是定义该方法的类(用`self`或`__CLASS__`来表示)。

```
class A
{
    public function saySelfValue()
    {
        echo self::class;
    }

    public function sayClassValue()
    {
        echo __CLASS__;
    }

    public function sayStaticValue()
    {
        echo static::class;
    }
}

class B extends A
{

}

$a = new A();
$a->saySelfValue(); // 'A'
$a->sayStaticValue(); // 'A'
$a->sayClassValue(); // 'A'

$b = new B();
$b->saySelfValue(); // 'A'
$b->sayStaticValue(); // 'B'
$b->sayClassValue(); // 'A' 
```

Enter fullscreen mode Exit fullscreen mode

# 关闭

我们已经讨论了几个例子，其中一些比另一些更有创意，是关于关键字`static`在 PHP 中的不同用法。虽然是人为的，但是用例都是非常真实的，并且它们能够交付给你的代码的价值是非常有影响力的。至少，让自己熟悉这些用途和技术，让它们在你的工具箱中可用，会让你成为更强的开发人员。

如果你有更多真实世界的例子，可能来自开源项目，可以为其他读者提供见解，请在评论中分享链接！

*最初发布在我的个人网站上[ryanc.co/blog/the-power-of-static](https://ryanc.co/blog/the-power-of-static)T3】*