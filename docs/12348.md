# 提高 Java 性能的 9 个技巧☕️🚀 🚶‍♂️

> 原文：<https://dev.to/sendilkumarn/9-tips-to-increase-your-java-performance-1l4d>

> 任何傻瓜都能写出计算机能理解的代码。优秀的程序员编写人类能够理解的代码。马丁·福勒

但是任何开发人员都渴望编写高性能的代码。让我们看看如何让 Java 代码运行得更快。

> 注意:JVM 有效地优化了代码。因此，您不需要针对一般用例进行优化。但是如果你想让 JVM 发挥出最大的性能。开始了。

[![](img/7a6e3cbf9d167cfb8740fb5d5d21e51f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N4teI6Sv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/15159pnizjj24gjuimqr.gif)

> 所有测试都是在 Macbook Pro 2017 笔记本电脑中的 OpenJDK 12.0.1 中进行的。

# 1。在构造函数中实例化

如果您的集合只初始化一次，最好初始化集合`constructor`本身的值，而不是实例化集合并使用`addAll`设置值。

```
// Slower 🚶‍♂️
Set<String> set = new HashSet<>();
set.addAll(Arrays.asList("one", "two", "three"));

// Faster 🚀
Set<String> set = new HashSet<>(Arrays.asList("one", "two", "three")); 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用 [JMH 基准](http://tutorials.jenkov.com/java-performance/jmh.html)来验证这一点。

> 结果单位为`operations/second` ( `op/s`)。数字越大，性能越高。

```
@State(Scope.Thread)
public static class MyState {

    @Setup(Level.Trial)
    public void doSetup() {
        var arr = new Integer[100000];
        for (var i = 0; i < 100000; i++) {
            arr[i] = i;
        }
        list = Arrays.asList(arr);
    }

    public List list;
}

// Faster 🚀 > ~148,344 op/s
@Benchmark
public HashSet usingConstructor() {
    var set = new HashSet<>(list);
    return set;
}

// Slower 🚶‍♂️ > ~112,061 op/s
@Benchmark
public HashSet usingAddAll() {
    var set = new HashSet<>();
    set.addAll(list);
    return set;
} 
```

Enter fullscreen mode Exit fullscreen mode

> `construtor`版本比`addAll`版本多提供约 36000 op/s。

* * *

# 2。AddAll 比 Add 快

类似地，与`add`相比，`addAll`提供了更高的每秒操作数。所以下一次当你向一个数组中添加东西的时候，确保你把它们堆起来，并且使用`addAll`来添加。

```
// Slower 🚶‍♂️ ~116116op/s
@Benchmark
public ArrayList<Integer> usingAdd() {
    var a = new int[1000];
    for (var i = 0; i < 1000; i++) {
        a[i] = i;
    }

    var arr = new ArrayList<Integer>();
    for (var i = 0; i < 1000; i++) {
        arr.add(a[i]);
    }

    return arr;
}

// Faster 🚀 ~299130 op/s
@Benchmark
public ArrayList<Integer> usingAddAll() {
    var a = new Integer[1000];
    for (var i = 0; i < 1000; i++) {
        a[i] = i;
    }

    var arr = new ArrayList<Integer>();
    arr.addAll(Arrays.asList(a));
    return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

`addAll`的速度几乎是`add`版本的两倍。

* * *

# 3。将`EntrySet`用于`KeySet`上方的地图

你在地图上迭代了很多吗？然后在`keySet`上使用`entrySet`。

```
 // Slower 🚶‍♂️ ~37000 op/s
@Benchmark
public HashMap<Integer, Integer> keySetIteration(Blackhole blackhole) {
    var someMap = new HashMap<Integer, Integer>();

    for (var i = 0; i < 1000; i++) {
        someMap.put(i, i);
    }

    var sum = 0;
    for(Integer i: someMap.keySet()) {
        sum += i;
        sum += someMap.get(i);
    }
    blackhole.consume(sum);
    return someMap;
}

// Faster 🚀 ~45000 op/s
@Benchmark
public HashMap<Integer, Integer> entrySetIteration(Blackhole blackhole) {
    var someMap = new HashMap<Integer, Integer>();

    for (var i = 0; i < 1000; i++) {
        someMap.put(i, i);
    }

    var sum = 0;
    for(Map.Entry<Integer, Integer> e: someMap.entrySet()) {
        sum += e.getKey();
        sum += e.getValue();
    }

    blackhole.consume(sum);

    return someMap;
} 
```

Enter fullscreen mode Exit fullscreen mode

## `entrySet`能比它的`keySet`变种多运行`9000`运算一秒钟。

# [4](#4-use-raw-singletonlist-endraw-instead-of-an-raw-array-endraw-with-single-element)。使用`SingletonList`代替单元素的`array`。

```
// Faster 🚀
var list = Collections.singletonList("S"); 

// Slower 🚶‍♂️
var list = new ArrayList(Arrays.asList("S")); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 5。用`EnumSet`代替`HashSet`。`EnumSet`要快得多。

```
// Faster 🚀
public enum Color {
    RED, YELLOW, GREEN
}

var colors = EnumSet.allOf(Color.class);

// Slower 🚶‍♂️
var colors = new HashSet<>(Arrays.asList(Color.values())); 
```

Enter fullscreen mode Exit fullscreen mode

更多关于 EnumSet [的信息请点击这里](https://www.baeldung.com/java-enumset)。

* * *

# 6。不要随意初始化对象。尽量重复使用。

```
 // Faster 🚀
 var i = 0 ;
 i += addSomeNumber();
 i -= minusSomeNumber();
 return i;

 // Slower 🚶‍♂️
 var i = 0 ;
 var j = addSomeNumber();
 var k = minusSomeNumber();
 var l = i + j - k;
 return l; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# [7](#7-use-raw-stringisempty-endraw-method-to-check-whether-the-raw-string-endraw-is-empty)。使用`String.isEmpty()`方法检查`String`是否为空。

String 是一个`byte[]`，`isEmpty`只是检查一个`Array`的长度。所以要快很多。

```
public boolean isEmpty() {
        return value.length == 0;
    } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 8。如果你使用的字符串只有一个字符，用`Character`替换它们

```
 // Faster 🚀
 var r = 'R' ;
 var g = 'G' ;
 var b = 'B' ;

 // Slower 🚶‍♂️
 var r = "R" ;
 var g = "G" ;
 var b = "B" ; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 9。尽可能使用 [StringBuilder](https://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html) 。

```
 // Faster 🚀
StringBuilder str = new StringBuilder(); 
str.append("A"); 
str.append("B"); 
str.append("C"); 
str.append("D"); 
str.append("E"); 
....

// Slower 🚶‍♂️
var str = "";
str += "A";
str += "B";
str += "C";
str += "D";
str += "E";
.... 
```

Enter fullscreen mode Exit fullscreen mode

> 但是当你不得不做一个单独的连接时。使用`+`比使用 StringBuilder 更快。

* * *

如果你有一个有趣的表演技巧/有问题，请在评论中留下。

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️