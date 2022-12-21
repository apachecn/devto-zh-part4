# 安装新的节点版本和迁移 npm 全局程序包

> 原文：<https://dev.to/andy/installing-a-new-node-version-and-migrating-npm-global-packages-4no3>

所以我最近不得不安装一个新的节点版本，因为我们(dev.to)升级了它。或者至少我认为这就是原因——我只是按照命令行告诉我的去做。

我知道我已经在使用 [`nvm`](https://github.com/nvm-sh/nvm) 或者节点版本管理器了。它非常好用，和我使用的 Ruby 版本管理器 [`rbenv`](https://github.com/rbenv/rbenv) 很相似。看着`nvm`的自述，我所要做的就是:

1.  找出我需要的节点版本
2.  运行安装命令

因此，我查看了 Node 主页上的可用版本，我选择了最新的 LTS(长期支持)版本 10.16.0。我当时可能会升级到最新的 12.8.1，但是因为我运行的是 8.11.3 版本，所以我认为只升级两个主要版本更安全。不过*可能没那么多*重要。

然后我运行安装命令:

```
nvm install 10.16.0

# I would put the output but I installed this like 3 weeks ago :( 
```

Enter fullscreen mode Exit fullscreen mode

...完成了，太好了！似乎没什么问题，似乎也没什么别的事可做。

我在本地 dev.to repo 上运行了`yarn`，但是失败了:

```
:yarn
yarn install v1.16.0

# [1/4] 🔍  Resolving packages...
# [2/4] 🚚  Fetching packages...
# error execa@2.0.3: The engine "node" is incompatible with this module.
# Expected version "^8.12.0 || >=9.7.0". Got "8.11.3"
# error Found incompatible module.
# Visit https://yarnpkg.com/en/docs/cli/install
# for documentation about this command. 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我想我还在用我的旧版本。再次阅读`nvm`的自述文件，我不得不实际开始使用我安装的新节点版本:

```
nvm use 10.16.0
# Now using node v10.16.0 (npm v6.9.0) 
```

Enter fullscreen mode Exit fullscreen mode

好极了。这似乎行得通。我运行了`yarn`，成功了！

快进到某个周末之后，我正在做一个 Gatsby 副业项目，我试着启动开发服务器:

```
gatsby dev
#-bash: gatsby: command not found 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我想这可能与我的新节点版本有关。我找到了一个命令来重新安装以前版本的包:

```
nvm reinstall-packages 8.11.3
# Reinstalling global packages from v8.11.3...
# No installed global packages found...
# Linking global packages from v8.11.3...
# No linked global packages found... 
```

Enter fullscreen mode Exit fullscreen mode

奇怪，不知道为什么会这样。大家看看我都装了哪些版本:

```
nvm list
#         v8.11.3
# ->     v10.16.0
#          system
# default -> node (-> v10.16.0)
# node -> stable (-> v10.16.0) (default)
# stable -> 10.16 (-> v10.16.0) (default)
# iojs -> N/A (default)
# lts/* -> lts/dubnium (-> v10.16.0)
# lts/argon -> v4.9.1 (-> N/A)
# lts/boron -> v6.17.1 (-> N/A)
# lts/carbon -> v8.16.0 (-> N/A)
# lts/dubnium -> v10.16.0 
```

Enter fullscreen mode Exit fullscreen mode

哦，嗯，我想知道我的软件包是从系统版本安装的，而不是 8.11.3。我再次运行了重新安装命令，除了系统:

```
nvm reinstall-packages system
# some successful output 
```

Enter fullscreen mode Exit fullscreen mode

太好了！让我们再试一次。不，没用。还在弄`command not found`。嗯，我有两个选择:或者每次我需要使用全局包时使用安装了全局包的版本，或者找出问题所在。

事实上，还有第三个选择:做第一个选择，直到我厌倦了四个星期，然后最终找出问题所在。作为一个普遍懒惰的人，我去了，在这里我写这篇文章。

咳咳，无论如何，我有一个总的感觉，这是因为一些`npm`和`nvm`链接不正确。运行`gatsby develop`会失败并返回`command not found`，以及:
这一事实(可能)证实了这一点

```
which npm
# /Users/andyzhao/.nvm/versions/node/v8.11.3/bin/npm 
```

Enter fullscreen mode Exit fullscreen mode

嗯，当我第一次试图修复它时，我花了很多时间寻找，但毫无结果。这一次，我从 Stack Overflow 找到了一个帮助我的答案:

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 答案回复:改变 Windows 中 node.js 模块默认全局安装目录？](https://stackoverflow.com/questions/19874582/change-default-global-installation-directory-for-node-js-modules-in-windows/23586069#23586069)

May 10 '14[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)71![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/19874582/change-default-global-installation-directory-for-node-js-modules-in-windows/23586069#23586069) </header>

试图将*全局*包安装到`C:\Program Files (x86)\nodejs\`中给我带来了**以管理员身份运行**的问题，因为 npm 试图安装到
T1 中

要解决这个问题，请将*全局*安装目录更改为`C:\Users\{username}\AppData\Roaming\npm`:

在`C:\Users\{username}\`中，创建`.npmrc`文件，内容为:

`prefix = "C:\\Users\\{username}\\AppData\\Roaming\\npm"`

*参考*

*   `npm install -g` …

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/19874582/change-default-global-installation-directory-for-node-js-modules-in-windows/23586069#23586069)</button>

具体来说，命令`npm config ls -l`给了我一些我需要的线索:

```
npm config ls -l
# ; cli configs
# long = true
# metrics-registry = "https://registry.npmjs.org/"
# scope = ""
# user-agent = "npm/6.9.0 node/v10.16.0 darwin x64"
# 
# ; userconfig /Users/andyzhao/.npmrc
# prefix = "/Users/andyzhao/.nvm/versions/node/v8.11.3"
# ...whole lots of other stuff
# globalconfig = "/Users/andyzhao/.nvm/versions/node/v8.11.3/etc/npmrc"
# ...etc. 
```

Enter fullscreen mode Exit fullscreen mode

所以我的`globalconfig`被设置为节点 v8.11.3，而不是我当前的版本 v10.16.0。看到我的前缀也是 8.11.3，我决定打开我的`userconfig`文件`.npmrc`,看看我是否可以从那里改变一些东西。

```
# .npmrc

prefix=/Users/andyzhao/.nvm/versions/node/v8.11.3 
```

Enter fullscreen mode Exit fullscreen mode

啊哈！一定是这样！对吗？我不知道，但值得一试！我更新到 v10.16.0，重启终端，运行`nvm reinstall-packages system`。`npm`开始运行并安装所有东西，我看到`gatsby-cli`已经安装好了。再次重启终端进行确认，并运行`gatsby develop`。成功！！！

唷，多棒的旅行啊！弄清楚这一点肯定比我想要的时间长，但我很高兴我设法得到了它。虽然我确实在做任何事情之前一遍又一遍地换了三个版本，但我认为从一个问题中解脱出来，带着新的视角回来总是有帮助的。希望没有其他人需要解决`nvm`和`npm`的安装问题。✌️