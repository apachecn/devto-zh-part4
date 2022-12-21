# PHP 中带有特征的多重继承

> 原文：<https://dev.to/supunkavinda/multiple-inheritance-with-traits-in-php-2ba3>

PHP 中的是一种减少代码重复的强大方法。

在[继承](https://developer.hyvor.com/tutorials/php/oop-inheritance)中，我们只能继承一个类。这里有一个例子。

```
class Object {
    public $width;
    public function setWidth($width) {
         $this -> width = $width;
    }
}

class Box extends Object {

}
$box = new Box;
$box -> setWidth(10); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用了[单继承](https://www.php.net/manual/en/language.oop5.inheritance.php)。如果我们需要使用多重继承，我们不能通过扩展来实现。为此，我们需要使用[特征](https://developer.hyvor.com/tutorials/php/oop-traits)。

# 什么是特质？

特征是包含方法的类。特征不能被实例化。

这里有一个简单的特点。

```
Trait ObjectToArray {
    public function getArray() {
         return get_object_vars($this);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们在课堂上运用我们的特质。

```
class User {
    use ObjectToArray;
    public function __construct($name, $country) {
        $this -> name = $name;
        $this -> country = $country;
    }
}

$user = new User('John', 'USA');
var_dump($user -> getArray()); 
```

Enter fullscreen mode Exit fullscreen mode

同样，你可以在一个类中使用多个特征。

```
class User {
    use ObjectToArray;
    use ObjectToJSON;
    // and more...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

如果你需要在多个类中使用相同的方法，特征是非常有用的。Traits 有助于让代码更有条理。但是，我没见过开发者频繁使用 Traits。他们倾向于使用继承和接口。

我不确定原因，但是，我认为大多数 PHP 开发人员并不知道**特征**。

感谢您的阅读！