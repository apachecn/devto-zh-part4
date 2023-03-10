# 康威的生活游戏-和朋友一起！

> 原文：<https://dev.to/johnmerchant/conway-s-game-of-life-with-friends-3jj9>

这是一个关于我如何利用电脑、电子设备、代码、创造力和好奇心创造出又酷又有趣的东西的故事。最终结果是[https://www.multilife.jmercha.dev/](https://www.multilife.jmercha.dev/)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ johnmerchant ](https://github.com/johnmerchant) / [多寿命](https://github.com/johnmerchant/multilife)

### 生命元胞自动机模拟的多人游戏

<article class="markdown-body entry-content container-lg" itemprop="text">

# MultiLife.live

实时细胞自动机的一个实验。

参见我的 [DEV 社区](https://dev.to/jmercha/conway-s-game-of-life-with-friends-3jj9)帖子，了解这背后的故事。

基本上，一个互动的多人数字熔岩灯。

[![](img/7cf62d4ba00c49d87cda7a9a94899836.png)](https://camo.githubusercontent.com/ecb197d94e10a4ed9ff3efab4038cacb2257b0fd8298003e4fc5c11fe0f26e1c/68747470733a2f2f6d656469612e67697068792e636f6d2f6d656469612f524a744a4152624959506a756f42725832322f67697068792e676966)

## MultiLife RGB

multilife 可以使用带有 [multilife-rgb](https://github.com/jmercha/multilife-rgb) 的 Raspberry Pi 渲染到 RGB LED 矩阵面板上。

## 属国

*   Node.js 13
*   故事
*   有操作系统的计算机。
*   或者码头工人

## 设计

前端在 React 中实现，使用 Redux 管理客户端状态。

游戏本身是用一个`<canvas>`渲染的。

游戏状态由服务器端管理，事件通过 websocket 在客户端和服务器之间实时推送

协议、模型和效用函数都是[同构的](https://en.wikipedia.org/wiki/Isomorphic_JavaScript)。也就是说，它是能够在服务器端和客户端执行的代码。

## 运转

*   `yarn dev-server` -构建并启动服务器
*   `yarn dev-client` -构建并启动前端

</article>

[View on GitHub](https://github.com/johnmerchant/multilife)

## 起源

我最近和几个朋友和同事谈论康威的人生游戏。基本可以解释如下。

细胞网格有两种状态:活的和死的。在游戏的每一次迭代中，有一组规则在网格上的每个单元格中进行评估:

1.  具有< 3 个活邻居的活细胞死亡
2.  具有 1 个以上活邻居的活单元继续进行下一次迭代
3.  具有超过 3 个邻居的活细胞死亡
4.  正好有 3 个邻居的死细胞变成活的

早在 2000 年，我就创建了一个运行在浏览器中的游戏人生 Java 小程序——这些小程序早就被弃用了，原始源代码也不幸地丢失了。我写下它并向人们展示它，从中获得了很多乐趣。

我开始对自己思考，我能在 2019 年用我最喜欢的网络技术再做一次吗？我能在 JSX 表演性地渲染细胞的动态网格吗？游戏状态如何表示和更新？

我最终走下了多个兔子洞和切线，最终学到了很多东西！

## 实验

在我实验的最初迭代中，我试图将网格渲染成一系列 JSX 元素。用'⬜'(白盒)表情符号代表活细胞，用'⬛'(黑盒)代表死细胞的元素。熟悉 React 的人可能知道，这不是一个好主意:DOM 更新极其缓慢，即使有 React 的[协调](https://reactjs.org/docs/reconciliation.html)，它仍然在每个节拍更新数百个 DOM 元素，导致无响应的体验。例如，单元点击事件将花费几乎 100 毫秒来更新整个网格。

那么，我该如何表演性地渲染生命网格游戏呢？答案是，用一张 [<画布>](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) 。我使用 React 的 [useEffect](https://reactjs.org/docs/hooks-effect.html) 钩子在画布元素的每次状态更新时绘制游戏状态。

## 多人游戏

我开始考虑在哪里存储和处理游戏状态，并决定在 Redux 中管理游戏状态，这样我就可以使用 actions 和 reducers 清楚地定义和处理游戏事件。简单地说，Redux 是一个“状态容器”,它允许您将应用程序引发的事件(也称为操作)减少到一个单一的、可预测的状态。

当我在实现 reducers 和 actions 时，我想:将游戏状态集中起来并广播给多个“玩家”不是很容易吗？然后，我将所有的游戏处理逻辑:游戏更新间隔、规则评估和玩家事件转移到 Node.js 服务器中，连接了一些 web socket 操作，从而创建了“multilife”。

虽然已经有使用 Redux 的 WebSockets 的框架，例如 [redux-websocket](https://github.com/giantmachines/redux-websocket) ，但是我决定自己编写，因为只需要 4 个简单的动作:

*   `WS_OPEN` -连接打开
*   `WS_SEND` -向服务器发送消息
*   `WS_RECEIVE` -接收来自服务器的消息
*   `WS_CLOSE` -连接关闭

我还需要更多地控制我发送和接收消息的格式，使用二进制而不是 JSON，正如我在优化一节中描述的那样。

## 颜色

我想，这很有趣！我现在可以在多个浏览器上实时播放游戏状态了！但是...我怎样才能让它变得更有趣呢？我决定给每个细胞一种颜色，因为它看起来很漂亮！每个玩家连接时都会被分配一个随机的颜色。这些细胞在繁殖时也会混合颜色，创造出一些有趣的图案。

## 优化

我发现用明文 JSON 序列化整个游戏状态和事件在计算上非常昂贵，并且使用了大量带宽。我和一个同事聊天，他们建议创建一个二进制协议，我就这么做了！我也考虑过[协议缓冲区](https://developers.google.com/protocol-buffers/)，但是我更喜欢自己序列化数据。

我知道二进制协议将特别难以实现，所以我使用了一种[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)的方法:我为二进制协议编写了最初失败的`serialize`和`deserialize`测试，每个测试都声称它可以成功地序列化和反序列化一个协议模型，然后为每个方法编写代码，直到它们都通过。当处理复杂的逻辑时，单元测试是非常宝贵的。

我使用了[颜色命名模块](https://github.com/colorjs/color-namer)来命名游戏状态中的每种颜色。然而，它起初是低效的——每次它查找一种颜色时，它遍历整个颜色名称列表来比较颜色距离，这是一个`O(n)`(或线性时间)操作，并且它不缓存每次颜色查找的结果。为了提高性能，我分叉了存储库，并通过在一个 [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 中缓存结果来实现[记忆](https://en.wikipedia.org/wiki/Memoization)。我使用了一个 WeakMap，这样垃圾收集器会间歇性地清除缓存，而不是永远填满缓存(有`2^24 - 1`，或者 16，777，215 种可能的颜色可以查找)...).我还实现了对 [Delta-E](http://zschuessler.github.io/DeltaE/learn/) 颜色距离函数的支持，以便更准确地命名颜色。我向模块维护者提交了这两个变更请求，它们最终被接受并发布了。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 添加记忆，deltaE 距离支持 #9](https://github.com/colorjs/color-namer/pull/9) 

[![jmercha avatar](img/d7e18b21595e814eeaa6e4516b55c6eb.png)](https://github.com/jmercha) **[jmercha](https://github.com/jmercha)** posted on [<time datetime="2019-07-13T11:15:23Z">Jul 13, 2019</time>](https://github.com/colorjs/color-namer/pull/9)

你好，我在这个模块中增加了两个功能

*   增加了内存化/缓存，从而显著提高了性能
*   可选地允许使用颜色距离的 [Delta-E](http://zschuessler.github.io/DeltaE/learn/) 函数

[View on GitHub](https://github.com/colorjs/color-namer/pull/9)

## 部署和发布

现在是时候向世界展示我创造的东西了！但是怎么做呢？嗯，要管理一个网站，我需要一台服务器。我在[数字海洋](https://digitalocean.com)上创建了一个液滴来托管 multilife。我还购买了一个域名:multilife.live(编辑:我没有续费，它已经过期了，现在被别人停着！)

我设置了 [Nginx](https://nginx.com) 来托管网站，设置了 [pm2](http://pm2.keymetrics.io/) 来运行应用程序，设置了 [LetsEncrypt](https://letsencrypt.com) 来提供 SSL。

我还使用 [CircleCI](https://circleci.com) 设置了 CI/CD，这样每当我将新代码合并到 master 中时，就不必手动部署到生产环境中。CircleCI 在部署前也会运行我的测试。

在多次尝试让 CI/CD 工作(许多许多“修复 CI”提交)之后，multilife 发布了，我与我的朋友们分享了它。我们四处点击，观察模式的形成，玩得很开心。该网站还采用了响应式设计，所以每个人都把手机放在屏幕上触摸！

## 多寿命 RGB

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【johnmerchant】](https://github.com/johnmerchant)/[【多用途 RGB】](https://github.com/johnmerchant/multilife-rgb)

### 将 multilife.live 渲染到连接到 Raspberry Pi 的 RGB LED 矩阵硬件

<article class="markdown-body entry-content container-lg" itemprop="text">

# MultiLife RGB

将 [https://multilife.live](https://multilife.live) 游戏状态渲染到连接到 Raspberry Pi 的 LED RGB 矩阵面板上

## 属国

*   [rpi-rgb-led 矩阵](https://github.com/hzeller/rpi-rgb-led-matrix)

## 建筑物

```
# clone repos
cd ~
git clone https://github.com/jmercha/multilife-rgb
git clone https://github.com/hzeller/rpi-rgb-led-matrix

# build librgbmatrix
cd ~/rpi-rgb-led-matrix/lib
make
sudo cp librgbmatrix.so.1 /usr/lib

# build multilife-rgb
cd ~/multilife-rgb
make
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/johnmerchant/multilife-rgb)

然后我想更进一步:如果我能把游戏状态渲染到一个 RGB LED 面板上会怎么样？那不是很酷吗？于是我货比三家，买了一个 [32x32 RGB LED 矩阵面板](https://core-electronics.com.au/32x32-rgb-led-matrix-panel-4mm-pitch.html)和一个[树莓派 Zero W](https://core-electronics.com.au/raspberry-pi-zero-w-wireless.html)

当我在给 RGB LED 面板接线时，我不小心接错了引脚，弄坏了面板上所有的电子元件——我把 5 伏的电压接入了接地引脚。哎呀！在我开始工作之前，我不得不又等了一个星期，等待一封新邮件的到来。

我在这里学到了宝贵的一课:坏掉的软件很容易修复，但你不容易修复坏掉的晶体管和电容。

[![](img/034ef27541da0a1fa6c21df83502b10a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e5TZYCD3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/AS1TCfw.jpg)

我用 C - [multilife-rgb](https://github.com/jmercha/multilife-rgb) 编写了一个程序，使用我的二进制协议通过 UDP 监听来自服务器的游戏事件，并使用 [rpi-rgb-led-matrix](https://github.com/hzeller/rpi-rgb-led-matrix) 库渲染单元。我选择 UDP 而不是 TCP，因为它对我使用它的情况更有意义——TCP 是基于流的协议，而 UDP 是基于数据报或消息的。我也不介意信息偶尔收不到或者顺序不对。

我发现用 C 语言阅读多生命二进制协议非常方便，我只是将一个指向协议模型结构的指针分配给接收消息缓冲区。

```
message.data = buffer + 1; 
```

Enter fullscreen mode Exit fullscreen mode

尽管它确实需要在`Cell`结构上使用[打包属性](https://gcc.gnu.org/onlinedocs/gcc-3.3/gcc/Type-Attributes.html)来正确对齐数据。

最终结果在视觉上很吸引人，尤其是在黑暗中。我喜欢能够从我的手机或桌面浏览器点击细胞，并看到它立即出现在矩阵上。

## 结论

也许在未来，如果“mutlilife”以某种方式流行开来(我对此表示怀疑)，我可以通过使用 [Redis](https://redis.io/) 和 [Hashlife](https://en.wikipedia.org/wiki/Hashlife) 算法来扩展它，并在前端和协议中支持缩放和平移。

通过构建东西来学习是很有趣的，即使你正在构建一些实际上无用的东西。我希望这能启发其他人通过建造多余的东西来学习！