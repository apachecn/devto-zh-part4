# Javascript 比 Kotlin 好在哪里？

> 原文：<https://dev.to/jmfayard/what-does-javascript-better-than-kotlin-3l6d>

严肃的问题，不是巨魔。Javascript 和 Kotlin 是两种非常不同的语言，但是看看其他开发者社区在做什么总是很有趣的。

如果你懂这两种语言，你觉得 Typescript 在哪些方面比 Kotlin 做得更好？

或者如果你是前端开发人员，不了解 Kotlin，你觉得 Javascript/Typescript 有哪些地方是语言最好的，简洁，安全，雄辩？

* * *

这个问题的灵感来自于我的上一篇文章，我写了一个我在[科特林](https://kotlinlang.org)
发现的提取常量的好模式

```
with(ConfigObject) {
    println("Language $KOTLIN is sparking $JOY")
} 
```

Enter fullscreen mode Exit fullscreen mode

我被 KIP 的评论震惊了，他提醒我几乎同样的事情在 Javascript 中也是可能的

```
with(ConfigObject) {
    console.log(`Language ${TYPESCRIPT} is sparking ${JOY}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 是一种非常好的语言，但也是一种相对较新的语言，仍然有新的有用模式有待发现。所以我想:我能不能从 Javascript 的优秀部分中获得灵感来加速这个过程呢？

我看了前端同事出的代码，我喜欢的一点是[对象析构](https://www.typescriptlang.org/docs/handbook/variable-declarations.html#object-destructuring)

```
const {id, firstName, lastName} = person 
```

Enter fullscreen mode Exit fullscreen mode

科特林也有[对象析构](https://kotlinlang.org/docs/reference/multi-declarations.html#destructuring-declarations)

```
val (id, firstName, lastName) = person 
```

Enter fullscreen mode Exit fullscreen mode

但以我的经验来看，用的不多。一个问题是它是基于**位置的**。上面的代码可能正在运行，但是如果有人改变了`Person`类中属性的顺序，代码将会失败。基于 Typescript 命名的方法显然更好。意识到这一点后，我发现了一个在科特林[https://youtrack.jetbrains.com/issue/KT-19627](https://youtrack.jetbrains.com/issue/KT-19627)实施相同解决方案的提议

从长远来看是个好办法。但从长远来看，我们都死了！

我能找到一种模式来实现类似 Typescript 的功能吗？事实证明:是的

[https://pl.kotl.in/xXmq1WpKi?theme=darcula](https://pl.kotl.in/xXmq1WpKi?theme=darcula)

在这里，我再次使用了`with`来将我需要的东西从我的对象转换成我可以轻易销毁的东西。它比 Kotlin 默认提供的要通用得多，后者只适用于“数据类或元组的属性”。