# 很棒——但也大错特错

> 原文：<https://dev.to/andreasjakof/brilliant-but-also-horribly-wrong-50de>

在第一年里，我们可能都至少有过一次这样的时刻:我们想出了一个主意，这个主意非常棒，但却被其他人否决了，他们随后向我们展示了这个主意有多么错误。我们从中学到了很多。

### 那么你在编码中想到的最聪明、最优雅的想法是什么，也是完全错误的？

我的第一份工作是刚从大学毕业。我有大约 6 个月的专业编码经验，后面还有一段非常陡峭的学习曲线。我们使用 C++并在我的团队领导创建的框架内工作，我们使用插件并发送/接收一个“实体”,其中有一些东西，作为插件间的通信。

这让我很烦恼，要写很多支票，要弄清楚我到底在那里得到了什么。但是因为它是一个插件系统，基类必须保持通用性。

我的队长真的很酷。我喜欢和他交换想法。他很开放，即使是最疯狂的想法。我天真地提议使用异常机制来“捕捉”实体并拥有隐式类型信息。他让我去做。

如果我没记错(嘿，那是 9 年前)在插件的基类中有一个主循环，运行在一个单独的线程上，检查缓冲区中是否有新的实体。如果有，它将启动一个新的线程执行一个每个插件都必须实现的方法。

插件里有:

```
public virtual override HandleNewEntity()
{
  try
  {
    GetNewEntity(); //from base class, throws the new entity as exception
  }
  catch (EntityA_Type a)
  {
   //do something with Type A
  }
  catch (EntityB_Type b)
  {
   //do something with Type B
  }
  catch(...) 
  {
   // ignore, what you don't know
  }
} 
```

我解决了...至少我是这么认为的。我的其他团队成员对此有所不同。

您不能使用异常！例外是——因此得名——用于特殊情况。发生了不该发生的事情。
然后我学习了 C++异常处理中的栈展开和隐藏的机制，这使得我的方法**严重**错误。

即使有了近 10 年的经验和知识，我仍然喜欢这个想法，即像我这样滥用异常机制显然是错误的。

作为旁注:
当我重新创建整个东西，学习 C#时，我使用了一种不同的方法。向某个类型注册一个处理程序，然后在该类型出现时执行该处理程序。

```
//Base class for Plugins, without multithreading overhead
private static Dictionary<Type,List<Action<IEntity>>> _typeHandlerDic = new ...
protected void Register<T>(Action<T> handler) where T : IEntity
{
   List<Action<IEntity>> handlers;
   if (_typeHandlerDic.TryGetValue(typeof(T), out handlers)
      handlers.Add(new Action<IEntity>(x => handler(x as T)));
   else
    _typeHandlerDic[typeof(T)] = new List<Action<IEntity>> { new Action<IEntity>(x => handler(x as T)) };
}

//in the main loop, after determining, there is a newEntity

List<Action<IEntity>> handlers;
...
if (_typeHandlerDic.TryGetValue(newEntity.GetType(), out handlers))
{
   handlers.ForEach(handler=> handler.BeginInvoke(newEntity,null,null)); //execute on separate thread
} 
```