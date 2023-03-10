# GlobalScope.launch 和 launch 之间的区别

> 原文：<https://dev.to/frevib/difference-between-globalscope-launch-and-launch-96g>

协程构建器`GlobalScope.launch {}`和`launch {}`都用于启动新的协程。`GlobalScope.launch {}`在‘全局’范围内启动一个新的协同程序，而`launch {}`在`CoroutineScope`内启动一个新的协同程序。但这意味着什么呢？

为了理解正在发生的事情，我们看一看`runBlocking {}`。`runBlocking {}`将阻塞执行(执行`runBlocking {}`之后的下一行代码)，直到在其[作用域](%5Bhttps://medium.com/@elizarov/coroutine-context-and-scope-c8b255d59055%5D)内定义的所有协程都已完成。例如，在下面的代码片段中，`4.`处的代码行只有在`runBlocking {}`中定义的两个协程都已完成时才会执行:

```
fun main() {
    println("1\. Let's begin")
    runBlocking {
        launch { 
            delay(1000)
            println("3\. coroutine ONE")
        }

        launch { 
            delay(500)
            println("2\. coroutine TWO")
        }
    }

    println("4\. Only when the children inside runBlocking complete, execution follows on this line")
} 
```

[试试看](https://try.kotlinlang.org/#/UserProjects/ddsr38s4bjvt00cr7bagcebibj/1ejmadk62s9k2v6352ona5aq3f)

让我们试着用`GlobalScope.launch {}`而不是`launch {}` :
运行相同的代码

```
fun main() {
    println("1\. Let's start with GlobalScope.launch {}")
    runBlocking {
        GlobalScope.launch {
            delay(1000)
            println("3\. coroutine ONE")
        }

        GlobalScope.launch {
            delay(100)
            println("2\. coroutine TWO")
        }
    }

    println("4\. This line will execute even if the coroutines inside runBlocking did not complete.")
} 
```

[试试看](https://try.kotlinlang.org/#/UserProjects/t04ej0qngr4avtc3k09fc69gck/bn6ggp2ttmhig5unbagqlj8luq)

现在`runBlocking {}`范围内的协程没有完成，执行继续。这里发生了什么事？

`runBlocking {}`定义一个`CoroutineScope`,协程在其中运行。然而，上面例子中启动的协程运行在一个单独的“全局”范围内，在这里`runBlocking`没有控制权。据`runBlocking {}`所知，在其作用域内没有运行协程。