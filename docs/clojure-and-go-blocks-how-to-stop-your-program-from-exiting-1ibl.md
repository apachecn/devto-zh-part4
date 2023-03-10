# Clojure 和 go 块:如何阻止程序退出

> 原文：<https://dev.to/bretthancox/clojure-and-go-blocks-how-to-stop-your-program-from-exiting-1ibl>

这是我的第一篇帖子，可能是关于一个已经做得很烂的主题，但这是我第一次使用 Clojure 的 core.async 特性时绊倒的一个主题。

Clojure 的 core.async 使用 go 块。在 go 块中包装一些东西，它将异步发生。简单吧？当你第一次尝试时，你可能会写这样的东西，看看你是否理解了这个原则

```
(defn  counter  [start  end]  (loop  [count  start]  (if  (>  count  end)  1  (do  (println  count)  (recur  (inc  count))))))  (defn  -main  "I don't do a whole lot ... yet."  [&  args]  (go  (counter  0  100))) 
```

Enter fullscreen mode Exit fullscreen mode

什么都没发生。也许你很幸运，它会数到“1”，但是程序会退出。为什么没有完成？

的确如此。当您将某个东西放入 go 块时，您告诉 Clojure 您希望它出现在另一个线程中。程序将 go 块中的动作推给另一个线程，然后**立即返回当前线程。**
最后一部分很关键。主线程在 go 块之后不包含任何内容。所以程序完成并退出。没什么可做的了。上面的例子计数到 100 的唯一方法是，如果主线程有足够的工作要做，另一个线程可以完成。

那么，如何确保这个简单的例子完成呢？首先，重要的是要理解任何 go 块都构成一个通道。我不会详细介绍通道，但任何学习 core.async 库的人可能都见过类似这样的东西:

```
(def  my_channel_name  (chan)) 
```

Enter fullscreen mode Exit fullscreen mode

还有其他方法来定义通道，但通常你会使用`chan`，无论是在`def`语句中，还是在`let`或其他绑定中。

go 块也是一个通道。从 clojuredocs，去”...**返回一个通道，该通道将在完成后接收主体的结果**... "

无论在 go 块中放入什么，它的返回值都放在 go 块本身的通道上。在我的例子中，当`if`语句为真时，go 块将以值`1`退出。这个值是什么并不重要，因为我没有使用它，但是通道的存在和它上面放置的东西是确保你的程序在 go 程序块完成后结束*的关键。*

Core.async 有多种方法从一个通道获取值，但是在这个例子中我们将使用`<!!`。这是 core.async 中两个相似的函数之一，目的相似。另一个是`<!`。

`<!`只能在 go 程序块中使用。这是对主线程的非阻塞处理。非阻塞只是意味着程序不会等待它从相关的通道中获取任何东西。

`<!!`是一个阻塞 take，可以在 go 阻塞之外使用。如果它没有什么可获取的(即，它所获取的通道上没有任何东西)，那么它*会阻塞它所在的线程。*

因此，在更新我们的示例之前，让我们回顾一下:

1.  Go 块发生在主线程之外的其他线程上。
2.  主线程结束，因为它没有更多的工作要执行。
3.  go 块的作用就像一个通道，它将提供发生在其中的任何事情的返回值。
4.  `<!!`阻塞一个线程，直到它可以从通道中获取一个值。

所以，我们可以对我们的例子做一个非常简单的修改:

```
(defn  counter  [start  end]  (loop  [count  start]  (if  (>  count  end)  1  (do  (println  count)  (recur  (inc  count))))))  (defn  -main  "I don't do a whole lot ... yet."  [&  args]  ;;(go (counter 0 100))) ;;not used  (<!!  (go  (counter  0  100))))  ;;new code 
```

Enter fullscreen mode Exit fullscreen mode

现在程序打印 0 到 100。
添加`<!!`会阻止主线程完成，直到它可以从通道中获取一个值。在这种情况下，通道是我们的 go 块，其中的计数器函数直到完成从`start`到`end`的计数才返回值。

另一种选择是使用一个通道来告诉主线程其他线程的工作何时完成。
当使用`close!`显式关闭通道时，它返回`nil`作为其最后一个值。由于`nil`是一个假值，下面的代码可以通过监视假返回值来检查指定的“最后动作”通道的关闭:

```
(defn  -main  "I don't do a whole lot ... yet."  [&  args]  (let  [close_on_last_action_chan  (chan)]  (go-loop  [counter  1]  (if  (>  counter  1000)  (close!  close_on_last_action_chan)  (recur  (inc  counter))))  (while  (<!!  close_on_last_action_chan)))) 
```

Enter fullscreen mode Exit fullscreen mode

请确保您仅使用此通道*来监控程序的结束。在此通道上设置其他“假”值可能会使程序提前结束。*

#### 结论

这些都是人为的例子。在 go 块中做简单的`incr`循环是绝对没有价值的。它表明，如果主线程可能在异步事件完成之前结束，就需要“捕获”主线程。

我希望这对某些人有所帮助。我毫不怀疑有更好的方法来做我上面描述的事情，但是这种方法至少帮助我解决了我在理解 core.async 库时遇到的一些问题。

感谢阅读！