# 疯狂:游戏开发日志#5:游戏结束

> 原文：<https://dev.to/nuculabs_dev/mad-game-devlog-5-game-over-177l>

你好，

我不认为我会再在这个项目上投入时间和资源，我从编程和学习游戏开发的不同方面和 Unity 游戏引擎中获得了乐趣。

制作游戏需要很多艰苦的工作，我最初开始做这个是为了逃离我平常的编程活动，做一些不同的事情。

接下来，我想我会把我的重点转移到一些有利于我职业发展的领域，比如，尝试学习更多的计算机安全知识，更好地解决问题。

如果你想检查游戏，你可以在 [itch.io](https://dnutiu.itch.io/snowpiercer-isog) 上进行

我要感谢 Andrei、Maxi 和所有支持这个项目的人！

在结束这篇文章之前，这里有一些我们在开发这个游戏时发现有用的 Unity 提示:

## 制作图集，让预置实例化

不要在编辑器中手动设置游戏对象的预设，这很容易出错，你可以创建一个静态类来保存每个预设，这种方式可以通过编程实例化你所有的预设。

```
 public static class PrefabAtlas
    {   
        /* Snow Walls */
        public static readonly GameObject DestructibleHighSnow =
            Resources.Load<GameObject>("Walls/destructible_high_snow");
        public static readonly GameObject DestructibleSnow = 
            Resources.Load<GameObject>("Walls/destructible_snow");
        public static readonly GameObject IndestructibleWoodCrate =
            Resources.Load<GameObject>("Walls/indestructible_crate");
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
Instantiate(PrefabAtlas.DestructibleSnow) 
```

Enter fullscreen mode Exit fullscreen mode

## 忌经典单衣。

如果你想在你的游戏逻辑或状态中使用经典的 singletons，那么你可能会把事情搞得一团糟，这取决于你的具体用例，因为 Unity 管理 MonoBehaviour 类的生命周期。

要在 Unity 中创建一个 singleton，你可以从 Singleton:

```
public class MySingleton : Singleton<MySingleton>
{
    // (Optional) Prevent non-singleton constructor use.
    protected MySingleton() { }

    // Then add whatever code to the class you need as you normally would.
    public string MyTestString = "Hello world!";
} 
```

Enter fullscreen mode Exit fullscreen mode

谢谢大家！

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1511866) 的[Steve buiss NNE](https://pixabay.com/users/stevepb-282134/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1511866)拍摄

参考资料:

*   [https://wiki.unity3d.com/index.php/Singleton](https://wiki.unity3d.com/index.php/Singleton)