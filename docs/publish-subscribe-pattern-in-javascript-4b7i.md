# javascript 中的发布/订阅模式

> 原文：<https://dev.to/mfaghfoory/publish-subscribe-pattern-in-javascript-4b7i>

编程世界中有许多设计模式，每一种都是为了解决我们在编码过程中可能会遇到的一些特定问题而发明的。

其中一个有趣的是“发布/订阅”模式，它使我们能够将不同的模块/组件相互分离，并从它们那里发送/接收消息，而没有任何紧密的耦合关系，甚至不需要了解彼此。

通过使用上述概念和定义，我用 javascript 实现了一个非常简单的库，我认为它可以用作基于组件的应用程序中的一个小状态管理，我希望与大家分享我的经验和理解

**注意**:我知道有很多强大的库可以进行状态管理和发布/订阅模式，但是我相信基于概念创建一些东西可以帮助我们更深入地学习和理解它:)

那么让我们开始吧。

首先，我们需要一个中心对象来注册我们的订阅者或从订阅者列表中删除他们，并决定当发布者发送消息时哪些订阅者应该接收消息。此外，我们需要将我们的方法和订阅者的持有者定义为静态的，因为我们不想在需要使用它的时候创建一个新的中心对象。
类似这样的:

```
var PubSub = (function () {
    function PubSub() {
    }
    PubSub.subscribe = function (eventName, func) {
        this.initDic(eventName);
        this.dic[eventName].push(func);
    };
    PubSub.unsubscribe = function (eventName, func) {
        this.initDic(eventName);
        this.dic[eventName] = this.dic[eventName].filter(function (x) { return x != func; });
        if (!this.dic[eventName].length) {
            delete this.dic[eventName];
        }
    };
    PubSub.publish = function (eventName, data) {
        this.initDic();
        if (this.dic[eventName])
            for (var _i = 0, _a = this.dic[eventName]; _i < _a.length; _i++) {
                var func = _a[_i];
                func(data);
            }
    };
    PubSub.initDic = function (eventName) {
        if (!this.dic) {
            this.dic = {};
        }
        if (eventName && !this.dic[eventName]) {
            this.dic[eventName] = [];
        }
    };
    PubSub.dic = null;
    return PubSub;
}()); 
```

然后，假设我们有两个名为 customer 和 manager 的对象，客户想要收听这样一个特殊事件:

```
const pubsub = require('./PubSub');

const customer = function(name) {
    this.name = name;

    function emailReceiver(data) {
        alert(`${this.name} -- I received an email from ${data.sender}, content: ${data.content}`);
    }
    PubSub.subscribe(`receiveEmail${this.name}`, emailReceiver.bind(this));
}

const manager = function(name) {
    this.name = name;

    this.sendMessage = (customerName, content) => {
        PubSub.publish(`receiveEmail${customerName}`, {
            sender: this.name,
            content: content
        });
    }
} 
```

经理想给一个特定的客户发一封电子邮件，就像这样:

```
let jack = new customer('Jack');
let eli = new customer('Eli');

let manager1 = new manager('Boss');

manager1.sendMessage('Jack', 'you should prepare a report until tomorrow!');
manager1.sendMessage('Eli', 'call me!'); 
```

我已经创建了一个 JSFiddle，你可以看到结果
[https://jsfiddle.net/8o60fmtk//embedded//dark](https://jsfiddle.net/8o60fmtk//embedded//dark)

就是这样！我希望它能帮助你更好地理解这个模式的基本概念。