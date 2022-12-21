# 映射最佳实践

> 原文：<https://dev.to/henritremblay/map-best-practices-7ok>

今天的主题是关于`Map`和我在许多代码评审中看到的误用。

使用`Map`的想法是通过尽可能少的散列来做你需要的任何事情。
每次访问`Map`时都会产生一个哈希(例如`get`、`containsKey`、`put`)。

在 Java 8 中，增加了一些有用的新方法。
假设您想要检查某个东西是否在`Map`中:

*   如果是，就退回去
*   如果不是，添加并返回

经典的做法是:

```
if (map.containsKey(key)) { // one hash
    return map.get(key); // two hash
}
List<String> list = new ArrayList<>();
map.put(key, list); // three hash
return list; 
```

也是最慢的。
更好的办法是:

```
List<String> list = map.get(key); // one hash
if(list == null) {
    list = new ArrayList<>();
    map.put(key, list); // two hash
}
return list; 
```

这已经好多了。你保存一个散列。

**重要提示:**如果值可能是`null`，则该选项无效。
但是我强烈建议您不要使用空值

但是从 Java 8 开始，你有了三个更好的解决方案。

第一个是:

```
map.putIfAbsent(key, new ArrayList<>()); // one hash
return map.get(key); // two hash 
```

好一点，但不多。你还有两个哈希值。
和`ArrayList`被实例化，即使它已经在地图中。

你可以用更长的时间来提高:

```
List<String> list = new ArrayList<>();
List<String> result = map.putIfAbsent(key, list); // one hash only!
if(result == null) {
    return list;
}
return result; 
```

现在我们谈，只有一个哈希！但是仍然无用地实例化了`ArrayList`。

这让我们想到了另一个 Java 8 方法。

```
return map.computeIfAbsent(key, unused -> new ArrayList<>()); // one hash only! 
```

任务完成。
我们能得到的最快的一条线。
`ArrayList`只会在需要的时候被实例化。

**重要:**不要做`map.computeIfAbsent(key, ArrayList::new)`。
`computeIfAbsent`在参数中取一个`Function<KEY, VALUE>`。
所以这通常不会编译，除非`KEY`匹配一个数组列表构造函数的参数。
一个例子是当`KEY`是整数时。
传递一个构造函数方法引用实际上会调用`new ArrayList(KEY)` …这显然不是你想要的。

为了让你相信这是最好的解决方案，我用 JMH 做了一个小基准。结果如下:

```
Benchmark                               Mode  Cnt         Score        Error  Units
MapBenchmark.computeIfAbsent_there     thrpt   40  25134018.341 ± 687925.885  ops/s (the best!)
MapBenchmark.containsPut_there         thrpt   40  21459978.028 ± 401003.399  ops/s
MapBenchmark.getPut_there              thrpt   40  24268773.005 ± 690893.070  ops/s
MapBenchmark.putIfAbsentGet_there      thrpt   40  18230032.343 ± 238803.546  ops/s
MapBenchmark.putIfAbsent_there         thrpt   40  20579085.677 ± 527246.125  ops/s

MapBenchmark.computeIfAbsent_notThere  thrpt   40   8229212.547 ± 341295.641  ops/s (the best!)
MapBenchmark.containsPut_notThere      thrpt   40   6996790.450 ± 191176.603  ops/s
MapBenchmark.getPut_notThere           thrpt   40   8009163.041 ± 288765.384  ops/s
MapBenchmark.putIfAbsentGet_notThere   thrpt   40   6212712.165 ± 333023.068  ops/s
MapBenchmark.putIfAbsent_notThere      thrpt   40   7227880.072 ± 289581.816  ops/s 
```

直到下一次:快乐映射。