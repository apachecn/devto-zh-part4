# 用 JavaScript 和 NewBot 框架创建一个通用聊天机器人

> 原文：<https://dev.to/rsamaium/create-a-universal-chatbot-with-javascript-and-newbot-framework-324>

我们将创建一个模块化的快速聊天机器人。我们在没有外部平台(如 DialogFlow)的情况下利用 NLP

# 先决条件

*   在你的机器上安装 NodeJS 和 NPM
*   Javascript 知识

# 为什么要 NewBot 框架？

*   通用的，在任何地方都适用的单一代码
*   对话网站的创建
*   模块化和结构化
*   使用 NLP(自然语言处理)
*   轻松快速地编写用户聊天机器人对话的对话脚本语法
*   国际化
*   单元测试
*   带有 NewBot CLI 的仿真器

# 兼容性

所有平台，即

*   脸书信使
*   谷歌助手
*   亚马逊 Alexa
*   松弛的
*   电报
*   Viber
*   网络电话
*   等等。

并且直接在你的浏览器中(离线)

# 高于一切

安装 NewBot CLI 以利用命令行

```
npm install -g newbot-cli 
```

Enter fullscreen mode Exit fullscreen mode

创建一个项目

```
newbot new <your directory name> 
```

Enter fullscreen mode Exit fullscreen mode

转到生成的文件夹

您可以使用命令`newbot serve`测试聊天机器人。见下文

# 创造主要技能

`main.converse`是主技能的对话脚本。这只是离别时的谈话

> 使用的语言是特定于 NewBot 的。它叫做 ConverseScript，允许你创建语法接近 Javascript 的对话脚本
> More:[https://newbot.io/docs/syntax/variables.html](https://newbot.io/docs/syntax/variables.html)T3】

```
@Event('start')
start() {
    > I am PizzaBot, and your name ?
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？聊天机器人从第一次与用户交互时就给出了它的名字

*   `start()`是表示对话框的函数
*   `@Event('start')`是一个装饰符，指示函数应该如何被触发。这里,“开始”事件表示一旦与聊天机器人的第一次交互发生，就触发该功能

## 输入文本

让我们完成`main.converse`文件

```
$name = ''

@Event('start')
start() {
    > I am PizzaBot, and your name ?
    Prompt()
    $name = :text
    > Welcome, { $name }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `$name`是特定于用户的全局变量
*   `Prompt()`是等待用户输入的原生函数
*   `:text`一个神奇的变量。它是由系统创造的。它包含用户条目。我们将该值存储在全局变量`$name`中

## Javascript 文件

`main.js`文件包含一个代表技能
的对象

```
import code from './main.converse'

export default {
    code
} 
```

Enter fullscreen mode Exit fullscreen mode

# 创建技能下单

首先，创造一个新技能

```
newbot generate skill order 
```

Enter fullscreen mode Exit fullscreen mode

在`bot/skills`文件夹中创建了一个`order`文件夹

`order.converse`

```
@Intent('order', [
    'order a pizza',
    'get pizza',
    'buy pizza'
])
order() {
    date = :intent.date.value
    callApi(date)
    > I have noted an order for { date }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一个`@Intent`装饰器来使用 NLP

*   `@Intent()`是定义意图的装饰者。第一个参数:意图的名称。第二个参数:训练短语数组
*   `:intent.date.value`是一个神奇的变量，用于检索用户输入的日期
*   `callApi()`是我们创建的函数，存在于 Javascript 中

## 和 JS 技能

```
import code from './order.converse'

export default {
    code,
    functions: {
        callApi(date) {
            console.log(`call yout custom api, date is ${date}`)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

属性允许您声明可以在对话脚本中使用的函数

## 将创建的技能链接到聊天机器人

现在技能`order`已经被创建，将其添加到`main.js`文件中的属性`skills`

`order.js`

```
import code from './main.converse'
import orderSkill from './skills/order/order'

export default {
    code,
    skills: {
        orderSkill
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 全码

[https://codesandbox.io/embed/pizzabot-with-newbot-framework-tssc5](https://codesandbox.io/embed/pizzabot-with-newbot-framework-tssc5)

# 在仿真器中测试

在项目的根目录下键入以下命令

```
newbot serve 
```

Enter fullscreen mode Exit fullscreen mode

转到`localhost:3000`并在仿真器中测试项目

# 在理解上更进一步

*   在浏览器中部署:[https://newbot.io/en/docs/essential/browser.html](https://newbot.io/en/docs/essential/browser.html)
*   开发 ExpressJS 服务器，执行聊天机器人:[https://newbot.io/en/docs/engine/express.html](https://newbot.io/en/docs/engine/express.html)