# 如何快速调试 JavaScript

> 原文：<https://dev.to/leewarrickjr/how-to-debug-javascript-quickly-27f9>

[![Photo of fire ants](img/ccd8a1f24116704c108a2ae670ee5c2c.png "Photo of fire ants")](///blog/static/d8b30a405f9f7076dc088249d765c2bf/96f4b/fireants.jpg)

**注意:**如果你想运行下面的代码片段，你可以在我的网站[这里](https://leewarrick.com/blog/debugging-javascript/)！

如果你曾经想过，*“咩……我跟* `console.log` *都挺好的，学习调试就像在火蚁里打滚一样痛苦”*，这篇文章送给你！

## 控制台告解

我和`console.log`是这样的:🤞。从一开始，它就是我解决所有奇怪事情的首选方案:`this`、反嵌套 API 调用数据、`async`、超时等等。

然而，曾几何时，我的 bootcamp 老师简单地给我们演示了调试(谢谢 James)，我想，*“这是… **真的很酷***。

当代码执行时，你可以看到代码内部发生的所有事情——多么有用！

不幸的是，我很快就进入了 node-land，然后是前端框架 valley。我很快就放弃了调试。(配置 VS 代码调试器有人吗？Webpack？不，谢谢。)

那就是👌一段时间。我会遇到一个问题，启动一个或三个`console.log`来解决它，然后返回代码删除它们。有时问题需要更多的控制台日志。有时当一个人陷入拉请求时，我的老板会傻笑；我会感觉很糟糕，删除/重新推送，然后回到`console.log` -ing。

在我编写 JavaScript 代码的时候，我输入的`console.log`可能比任何其他语法、单词或标点符号都多。我打字太多了，以至于我想安装一个[扩展](https://marketplace.visualstudio.com/items?itemName=ChakrounAnas.turbo-console-log)来节省我的击键次数。

还有很多有用的技巧。我用过`console.error`、`console.warn`、`console.table`、析构，在日志中用过 CSS，还有[更多的](https://kylegill.com/blog/2018-11-20-commanding-the-javascript-console/index)。

但是当这还不够的时候会发生什么呢？如果你厌倦了每次遇到困难都键入*" c o n s o l e l o g "*该怎么办？

## 控制台

在最近的工作中，我们意识到我们的生产代码有一个内存泄漏。我们精彩的产品代码是用 webpack 编译和传输的，被塞进 docker 容器，并在 AWS 的某个地方的 linux 环境中运行。

在一片混乱中，我的宝贝在哪里？

走了，朋友。不见了。

长话短说，我们深入了解了 Chrome 的性能工具(另一篇文章的主题？).通过这些，我终于找到了连接 node/webpack 和 chrome 开发工具的快捷方式。

从那以后，我发现自己在日常生活中使用了更多的语句。

## 用 Chrome 进行基本调试

好的。对于不熟悉的人来说，首先我们要从基础开始。你实际上是如何打开 chrome 调试器的？

1.  首先，**打开开发工具**
    *   按 f12，右键单击并选择“检查”，在 Windows 上按 ctrl-shift-i 或在 Mac 上按 cmd-clover-unnatural-hand-movement…只需像平常一样打开开发工具。
2.  **给你的代码**添加一个 `debugger` **语句。(下面我已经这样做了)**
    *   Chrome 将获取任何调试器语句，暂停代码，并在代码运行时打开调试工具**，如果(且仅当)**开发工具已经打开。
3.  **运行代码！**(将下面的片段复制并粘贴到 chrome 控制台中，然后点击 enter 键)
    *   点按“↗↘”或“➡”来逐行浏览代码
    *   单击继续运行脚本

```
function doStuff(thing) {
    thing = thing / 4 * -1
    thing = Math.pow(thing, 1)
    const thing2 = Math.trunc(thing) + 5
    debugger // <-- this makes the magic happen
    return thing2
}

const thingamajig = Math.random()
const result = doStuff(thingamajig)
console.log("And the result is..." + result) 
```

#### 挺酷的吧？😎👍

请注意调试器是如何在您单步执行代码时打印代码值的。非常非常好！如果你想深入研究一个对象，这可以帮你节省很多按键。

为什么我们不看看一些更基本的调试例子呢？

## 检出变量值

您可以将鼠标悬停在调试器中的变量上，并在单步执行代码时查看它们的值。

首先让我们来看一些 HTML:

```
<div id="magical">Boooooring</div> 
```

现在让我们看看上面的 html 中的一些 JavaScript 代码。尝试单步执行“awesome”变量，然后将鼠标悬停在它上面，查看 DOM 对象附带的所有神奇属性和方法。

(不要忘记使用↗↘来单步执行，并且当您完成时)

```
debugger // Let's watch the magic happen~ ✨👏
const div = document.getElementById('magical')
const awesome = document.createElement('marquee')
//I'm I-remember-marquee-tags years old. 🤫
awesome.innerHTML = '<h2>✨🚀~MAGICAL~ 🦄🌈\</h2\>'
div.textContent = "" //boring no more...
div.appendChild(awesome) 
```

## 嵌套属性

试图在对象上查找嵌套属性？调试有帮助！

让我们试着得到…哦，让我们说下面这个肉丸对象的甜度水平。

我可能要经历*次*次`console.log`次才能最终深入到那个问题……但是有一个更好的方法！

调试，悬停变量，然后展开属性，直到你找到你要找的键，然后**右击**并选择**复制属性路径**

```
const meatball = {
    ingredients: {
        spices: ["Oregano", "Italian", "Mystery"],
        meats: {
            beef: {
                freeRange: true,
                happy: "yes, until it was time to make meatballs",
            }
        },
    },
    flavors: [
        {
            umami: {
                level: "???"
            }
        },
        {
            savory: {
                level: 8
            }
        },
        {
            sweet: {
                level: Math.floor(Math.random() * 9)
            }
        },
    ]
}
//look up where we assign the meatball variable
//hover over meatball, and start opening up properties until you get to sweetness
//then right-click on "level" and choose "copy property path"
//paste it after meatball below:
debugger
console.log("Mama makes a meatball that's "
 + /*meatball.<paste here and uncomment>*/ 
 + "/9 in sweetness")
//don't forget to erase the debugger when you run it! 
```

## 黑框📦和⚛的反应

想调试 React？没问题！即使使用 React 或 Vue 这样的框架，也可以使用与上面相同的工作流程。只需放一个**调试器**语句进去，**打开开发工具**，然后**运行**它！

#### 一个小抓手

好吧，有一个小问题我必须提一下。我们要调试，*我们的*代码，而不是 React 的代码，对吗？然而，调试器将单步执行所有正在运行的**代码，而不仅仅是你的。因此，当您单步执行组件时，您可能会发现自己处于一些奇怪的 React 代码中。**

没问题！有办法解决的。试试这些建议:

*   迷失在一个诡异的剧本里？点击⬆“跳出”一个函数，并希望回到一个可识别的脚本。
*   还是迷路了？查看**调用栈**面板。这里的脚本是您当前正在逐步执行的脚本。在一些奇怪的剧本里？**右击**，选择**“黑盒脚本”**，跳过调试你所在的脚本。

下面试试！

```
function Counter() {
    const [count, setCount] = React.useState(0)
    function countUp(count) {
        debugger
        const newCount = count + 1
        setCount(newCount)
    }
    return (
        <div>
            <h2>Count is {count}</h2>
            <button onClick={() => countUp(count)}>Increment</button>
        </div>
    )
} 
```

## 但是 Node 呢？

啊，是的…永恒的问题。如何调试节点？

你可以使用 [VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) ，你可以使用一个叫做 [NDB](https://github.com/GoogleChromeLabs/ndb) 的简洁工具，但我认为我们只是使用 chrome，继续我们的生活。

(注意:我个人总是混淆“ndb”和“nbd ”,就像在“没什么大不了的”中一样，而不是“节点调试器”,这让我抓狂。我总是输错，我不能谷歌它，所以每次我想用它的时候它都让我出丑。)

1.  打开 chrome 并键入`chrome://inspect`
2.  然后用`--inspect`标志启动你的应用。
    *   例:`node --inspect server.js`
    *   您应该会看到类似这样的内容:`Debugger listening on ws://127.0.0.1:9229/dfcsd4c63-123e-234-adsd-123lkdfgk`
3.  回到 Chrome，你应该会看到“远程目标”和你的文件的文件名/路径，还有一个小小的`inspect`链接。点击它！这应该弹出一个单独的开发工具窗口。
4.  添加一些调试器到你的代码中去吧！

**注意**如果 chrome 第一次没有检测到你的调试器语句，你可能需要关闭并重新打开你的应用程序的调试面板。我也在使用最新版本的 chrome，你也应该如此😉

## 离别的思念

调试并不总是我对付奇怪 JavaScript 的第一道防线，但是现在我已经有了一个不错的工作流程，所以我会更经常地使用它。

你如何调试代码？在推特上给我打电话，让我知道！

*喜欢这个帖子？[请订阅我的简讯](https://tinyletter.com/leewarrick)和[查看我的播客！](https://techjr.dev)*