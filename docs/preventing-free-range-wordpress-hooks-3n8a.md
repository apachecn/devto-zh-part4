# 防止自由范围的 Wordpress 挂钩

> 原文：<https://dev.to/thisleenoble/preventing-free-range-wordpress-hooks-3n8a>

我在为 WordPress 开发时遇到的一个主要挫折是大量的功能依赖于编写不透明的字符串。其中最常见的是操作和过滤器。你可能找到的几乎所有关于创建和应用过滤器的教程或文档都会向你展示这样的代码:

```
add_filter('post_class', 'my_post_class_modifier', 10);

function my_post_class_modifier($classes){
    if($myConditionIsMet){
      $classes[] = 'another-css-class';
    }
    return $classes;
} 
```

我一直对这种方法不屑一顾。它要求:

*   在函数名前面加上笨拙的名称空间以避免潜在的冲突
*   写函数名两次，其中一次在字符串中，所以 Intellisense 不会有任何帮助

您将无法按住 option 键点按函数名称并找出它被调用的位置，或者通过点按调用代码来找到该函数。当您需要对多个过滤器使用相同的函数时，这是一个更大的问题。你的函数离调用代码越来越远，除非你非常有条理，否则你的 functions.php 文件(默认情况下是你放代码的地方)将会变成一个无法导航的充满讨厌的不透明字符串函数调用的转储区。Ack！

### 对象方法挂钩

好的，所以大部分时间我在我的方法中稍微干净一点，我会试着把彼此相关的或者与特定的自定义帖子类型相关的钩子聚集到一个类中。你当然可以在钩子中使用对象方法。

```
class myClass {
    public function applyHooks(){
        add_action('hook_string', array($this, 'hookForThisThing'), 10);
    }

    public function hookForThisThing($arg){
        // Perform an action here
    }
} 
```

这种方法意味着您不必在函数名前面加上仿名称空间的前缀，并且它至少允许您更容易地找到原始的优先级值——您甚至可以将它作为类的一个属性。

您还可以在函数体中使用类属性，这是将任意数据传递给函数体中的条件的好方法。但是你仍然必须使用不透明的字符串，坦率地说，我只是反对。我希望我的 IDE 尽可能多地编写我的代码，尽可能减少打字错误。记住函数名也是一个问题。一个合适的 IDE 将帮助您找到它，即使您只知道它的名字中的一个单词(这是一个很好的理由，如果必要的话，编写清晰而长的函数名)。

### 匿名函数

我开始更频繁使用的一种方法是将一个匿名函数传递给过滤器:

```
add_filter('post_class', function($classes){
    if($myConditionIsMet){
      $classes[] = 'another-css-class';
    }
    return $classes;
}, 10); 
```

这有几个优点:

*   在函数文件中没有笨拙命名的松散函数
*   正在使用的钩子与函数相邻。
*   如果需要，可以传入局部变量，如下所示:

```
$mySpecialValue = 87;
add_filter('post_class', function($classes) use ($mySpecialValue) {
    if(count($classes) == $mySpecialValue){
      $classes[] = 'has-88-css-classes';
    }
    return $classes;
}, 10); 
```

但是缺点包括不能在多个钩子上使用这个函数，最重要的是，如果以后需要的话，可以移除被钩住的函数。**你不能使用匿名函数**来使用 WordPress remove_filter 方法。

然而，对于绝大多数情况来说，这对我来说效果很好。如果我需要在多个过滤器上使用匿名函数，我总是可以将它定义为一个变量，但是移除它成了我需要解决的问题。

### 问题

我有一个小过滤器，需要在**‘posts _ where’**上运行来修改主查询。然而，一旦应用于主查询，它随后会应用于页面构建过程中使用的任何和所有其他查询。在过去，我通过使用过于复杂的方法来移除这种类型的过滤器，这种方法是在运行主查询之后，应用一个动作来移除某个任意过滤器上的过滤器。这导致函数文件中有更多的 cruft。它还需要调用优先级值，因为移除参数必须匹配。如果在将来的某个时候，您或者另一个开发人员(未来的您是另一个开发人员)改变了原始钩子的优先级，那么您的 remove 操作(它被隐藏在某个不相关的函数体中)将不再工作。你的 IDE 也不会告诉你，因为这个代码是不可理解的。

我决定看看我是否能制造自动移除过滤器。

## 引入闭包类

```
<?php
/*
 *
 */
namespace MyNamespace;

class closure {
    public $priority = 10;
    public $method;
    public $args = 1;
    public $hook;
    public $persistent = false;
    public $hookType;

    const TYPE_ACTION = 1;
    const TYPE_FILTER = 2;

    // Instantiators
    public static function createFilter($hook, $method, $priority=10, $args=1, $persistent=false, $hookType=self::TYPE_FILTER){
        $closure = new static();
        if(is_callable($method)) {
            $closure->hookType = $hookType;
            $closure->hook = $hook;
            $closure->method = $method;
            $closure->priority = $priority;
            $closure->args = $args;
            $closure->persistent = $persistent;
            static::apply($closure);
        } else {
            $closure = false;
        }
        return $closure;
    }

    public static function createAction($hook, $method, $priority=10, $args=1, $persistent=false){
        return static::createFilter($hook, $method, $priority, $args, $persistent, self::TYPE_ACTION);
    }

    public static function createPersistentFilter($hook, $method, $priority=10, $args=1){
        return static::createFilter($hook, $method, $priority, $args, true);
    }

    public static function createPersistentAction($hook, $method, $priority=10, $args=1){
        return static::createAction($hook, $method, $priority, $args, true);
    }

    // public API [WordPress needs public visibility]
    public function filter(){
        $args = func_get_args();
        $val = $args[0];
        if(is_callable($this->method)){
            $val = call_user_func_array($this->method, $args);
        }
        if(!$this->persistent) {
            static::remove($this);
        }
        if($this->hookType == self::TYPE_FILTER) {
            return $val;
        }
        return true;
    }

    // Utility methods
    public static function apply(closure $closure){
        if($closure->hookType == self::TYPE_ACTION){
            add_action($closure->hook, array($closure, 'filter'), $closure->priority, $closure->args);
        } else {
            add_filter($closure->hook, array($closure, 'filter'), $closure->priority, $closure->args);
        }
    }

    public static function remove(closure $closure){
        if($closure->hookType == self::TYPE_ACTION){
            remove_action($closure->hook, array($closure, 'filter'), $closure->priority);
        } else {
            remove_filter($closure->hook, array($closure, 'filter'), $closure->priority);
        }

    }
} 
```

只要它是使用 createFilter 或 createAction 实例化方法创建的，那么默认情况下，闭包对象将在第一次被使用时移除它自己的钩子。如果你不想让它这样做，你可以使用持久化方法，但是我不打算对每一个动作和过滤器都使用持久化方法，只对我的 **posts_where** 这样的应用使用持久化方法，在第一次应用之后我需要删除持久化方法，那么我很高兴这是默认的。

下面是一个使用闭包类的例子:

```
use MyNamespace\closure;

$mySpecialVar = 4077;
closure::createFilter('posts_where', function($where) use ($mySpecialVar) {
  $currentWhere = preg_replace("'^\s?AND\s'i", '', $where);
  $where = ' AND wp_posts.ID IN (SELECT 
     DISTINCT mp_a.meta_value
     FROM wp_postmeta mp_a
     WHERE mp_a.meta_key = \'my_meta_key_im_looking_for\'
       AND mp_a.post_id = '.(1*$mySpecialVar).') 
     OR (' . $currentWhere . ') ';
  return $where;
}); 
```

如果需要，我仍然可以将闭包对象存储为变量，并在多个过滤器上使用它。事实上，我可能会在类中添加一个方法来接受多个过滤器字符串，从而将所有内容放在一起。

因为闭包对象记住了自己的参数，所以如果你保留了闭包的副本，你可以在任何时候使用这个方法:

```
closure::remove($myClosure); 
```

取下钩子。更改原始闭合创建中的优先级现在不会影响移除。