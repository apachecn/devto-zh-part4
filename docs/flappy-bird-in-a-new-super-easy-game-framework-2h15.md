# Flappy 鸟，在一个新的，超级简单的游戏框架🐦

> 原文：<https://dev.to/jajoosam/flappy-bird-in-a-new-super-easy-game-framework-2h15>

# 用蟒蛇做拍打鸟玩

[![](img/ed74674c64938f7ba329e3b6394e06bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DyHuz1mL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://storage.googleapis.com/replimg/1566633312709_d7fd16ea4dac17d138efb5825222e7e4.gif)

[演示和代码](https://repl.it/@jajoosam/flappy-bird)👨‍💻

Flappy bird 是经典之一——正如你将看到的，超级容易创建，尤其是当你使用在 [repl.it](https://repl.it) 开发的 [Python Play](https://github.com/replit/play) 库的时候！

如果你之前没玩过 flappy bird，那就在 [flappybird.io](https://flappybird.io/) 上试试吧！

## 🎓那么，flappy bird 到底是怎么工作的呢？

在游戏中有两个物体很重要——鸟和管子。在上面的演示中，鸟是黄色的圆圈，而管道是蓝色的矩形。

**小鸟**在整个游戏中保持在同一个`x`位置，但是当玩家按下一个键时可以向上移动，并且由于重力继续下落。

管道是两个长方形，中间有一个间隙——鸟儿需要在不接触管道的情况下穿过管道。

## 🛣️入门

让我们从分叉[repl.it/@jajoosam/play-start](https://repl.it/@jajoosam/play-start)开始——这只是一个安装了`play`依赖项的 python 环境。

让我们通过创建一个黄色的圆圈来代表鸟，开始创建 flappy bird

```
bird = play.new_circle(
    color='yellow',
    x=play.screen.left + 100,
    y=play.screen.top - 40,
    radius=30,
) 
```

运行该代码将产生一个`yellow`圆，靠近屏幕的`top` `left`角，半径为`30`:

[![](img/679d15838d4a5cfbcbbd303896db7961.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jqxViIzQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/replimg/1566633408040_02c2a37f0f00bccc75ba7bfabffe1e41.png)

## 🌎物理是...轻松点。

我们希望我们的鸟受到重力的影响，并在它落到屏幕底部时反弹回来。因此，让我们创建一个完整的物理引擎来做到这一点！

lol jk，就在你的代码里加上这一行:`bird.start_physics()`

对，就是这样。真的！

[![](img/10e767d7505d79a1c84b21cc452147db.png)](https://i.giphy.com/media/3oKIPlifLxdigaD2Y8/giphy.gif)

如果你现在运行你的代码，你会看到一个黄色的圆圈掉了下来，又弹了回来！我们希望能够让我们的鸟扇动翅膀——让它在我们想跳的时候跳。

我们可以使用`play.repeat_forever`在游戏的每一帧运行一个函数——在这种情况下，如果按下`up`箭头键，我们的`bird`的`y`坐标会增加`7.5`T5

```
@play.repeat_forever
def do():
    if play.key_is_pressed('up'):
        bird.y += 7.5 
```

## 🚬让我们滚动管道

我们将创建两个一组的管道，用于阻塞我们的鸟——并将它们存储在一个列表中。

在代码顶部附近，添加一个新行来创建这个空列表。

```
boxes = [] 
```

我们希望每次生成的两个管道具有不同的高度，并且中间有一个间隙。我们还希望在随机的持续时间后生成下一对管道，以使游戏更加不可预测。

在这里使用 python play 的`random_number`函数对我们来说会非常有用。

```
# Returns a integer between 300 and 500 play.random_number(lowest=300, highest=500)

# Returns a float between 1.0 and 3.0 play.random_number(1.0, 4.0) 
```

添加整个代码块，就在`play.start_program()`
之前

```
@play.repeat_forever
async def block():

    # height of the top block
    top = play.random_number(lowest=300, highest=500)
    # height of the bottom block
    bottom = play.random_number(lowest=300, highest=500)

    # creating the top box of width 50, emerging from behind the current screen
    boxes.append(play.new_box(color="blue", y=play.screen.top,
                              x=play.screen.right+50, width=50, height=top))

    # creating the bottom box of width 50, emerging from behind the current screen
    boxes.append(play.new_box(color="blue", y=play.screen.bottom,
                              x=play.screen.right+50, width=50, height=bottom))

    # creating the next box after a random duration between 1 and 4 seconds
    await play.timer(seconds=play.random_number(1.0, 4.0)) 
```

这段代码在一个`async`函数中——这就是为什么我们可以使用`await`关键字通过`play.timer`将每个调用延迟一段特定的时间——这让我们可以每隔一段时间创建新的盒子！

用`play.new_box` -我们创建一个新管道，然后把它添加到`boxes`列表中。尝试使用我们提供给这个函数的参数。他们应该是可以理解的！

但是如果你现在运行代码，你会发现一切都和以前一样😕

## 🖥️把管子拿到屏幕上

因为我们所有的管道都在同一个`boxes`列表中，所以我们可以很容易地通过循环列表来移动它们。

将这个块添加到你的代码中，在`def do():`下，使盒子在屏幕中移动！

```
for box in boxes:
        # make the box move to the left
        box.x -= 1 
```

这是你点击运行时应该看到的！

[![](img/ea472d7433d7fd2a5cf750e794be7fb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YkkE7h2b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://storage.googleapis.com/replimg/1566633365697_de64ae43d0249c4bf844465aef5aa4e9.gif)

## 🤔但是...什么时候输？！

我真的要通过解释我们如何检测冲突来向你推销 python play。

就这样。没有🧢帽

在同一个循环中，我们将管道向左移动，只需添加这个条件块:

```
if bird.is_touching(box):
            box.color = "red" 
```

如果这只鸟碰到管子，它的颜色应该是红色的！

也在循环中添加这个块:

```
if(box.x < (play.screen.left-50)):
            boxes.remove(box)
            box.remove() 
```

我们只是用这个来防止[内存泄漏](https://en.wikipedia.org/wiki/Memory_leak)——如果我们的管道在屏幕之外，我们就把它移除吧！

## 🔮可以黑进去的东西

*   使用一只鸟的像素艺术代替黄色圆圈👨‍🎨
*   当小鸟碰到烟斗☠️时，重新开始游戏
*   给游戏增加能量💪
*   向屏幕添加实时乐谱💯

请务必在评论中写下任何问题或改进💬

这是所有的代码，你可以再看一遍——我也给你做了注释😄

[https://repl.it/@jajoosam/flappy-bird?lite=true](https://repl.it/@jajoosam/flappy-bird?lite=true)