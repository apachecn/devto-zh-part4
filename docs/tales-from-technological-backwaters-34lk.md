# 来自技术落后地区的故事

> 原文：<https://dev.to/bugmagnet/tales-from-technological-backwaters-34lk>

一些有足够经验的人(或者视情况而定)会注意到对[是的](https://en.wikipedia.org/wiki/Yes_(band))的[海洋地形的故事](https://en.wikipedia.org/wiki/Tales_from_Topographic_Oceans)的脱帽致敬。除了名字之外，没有什么能把这个摇滚杰作和这篇文章联系起来。

据我所知，我(和数据库专家)在一个技术落后的地方工作。我是 dev.to 社区的一员，这很酷，但我在物理上远离几乎所有的东西(澳大利亚西澳大利亚珀斯郊区，UTC+08)，我不知道我正在做的事情是前沿还是刀刃上的脊骨。

话虽如此，我确实在公共交通工具上无意中听到了一些奇怪的 IT 对话(我们是一个只有一辆车的家庭，我不怎么开它)，绝大多数人都在谈论更老的技术，如 Excel 公式、 [SAS 脚本](https://en.wikipedia.org/wiki/SAS_language)和 [AutoLISP](https://en.wikipedia.org/wiki/AutoLISP) 。

因此，为了增加平凡的混合，这里有一个小 JavaScript 类，我已经使用了多年。我用它来构建 SQL 请求。

```
class SQLStoredProcedureCall {
    _sproc: string;
    _tail: string[];

    constructor(sprocName: string) {
        this._sproc = sprocName;
        this._tail = [];
    }
    boolean(key: string, val: boolean) {
        this._tail.push(key + " = " + (val ? "1" : "0"));
        return this;
    }
    number(key: string, val: number) {
        this._tail.push(key + " = " + val.toString());
        return this;
    }
    date(key: string, val: Date | string) {
        this._tail.push(key + " = '" + val.toString() + "'");
        return this;
    }
    string(key: string, val: string) {
        this._tail.push(key + " = '" + val.toString().replace(/'/g, "''") + "'");
        return this;
    }
    toString() {
        const result = this._sproc + "  " + this._tail.join(", ");
        return result;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是打字稿版本。作为大型项目的一部分，原始的 javascript 版本每天被执行数千次，这些大型项目与我们在两个独立的 Azure 服务器上的 SQLServer 数据库进行交互。我甚至有一个 C#实现，它也有很多活动。可能有更好的方法来做这件事，但这只是满足当前的需要。

对于那些不想把它转换成更简单的 JavaScript 方言的人来说，下面是 [ts2gas](https://www.npmjs.com/package/ts2gas) 的解释。

```
var SQLStoredProcedureCall = (function () {
    function SQLStoredProcedureCall(sprocName) {
        this._sproc = sprocName;
        this._tail = [];
    }
    SQLStoredProcedureCall.prototype.boolean = function (key, val) {
        this._tail.push(key + " = " + (val ? "1" : "0"));
        return this;
    };
    SQLStoredProcedureCall.prototype.number = function (key, val) {
        this._tail.push(key + " = " + val.toString());
        return this;
    };
    SQLStoredProcedureCall.prototype.date = function (key, val) {
        this._tail.push(key + " = '" + val.toString() + "'");
        return this;
    };
    SQLStoredProcedureCall.prototype.string = function (key, val) {
        this._tail.push(key + " = '" + val.toString().replace(/'/g, "''") + "'");
        return this;
    };
    SQLStoredProcedureCall.prototype.toString = function () {
        var result = this._sproc + "  " + this._tail.join(", ");
        return result;
    };
    return SQLStoredProcedureCall;
}()); 
```

Enter fullscreen mode Exit fullscreen mode

打电话是这样的

```
var age = 58;
var sql = new SQLStoredProcedureCall("Database.dbo.SomeStoredProcedure")
    .string("@P1", "Bruce's string")
    .number("@P2", age)
    .boolean("@P3", age < 60)
    .date("@P4", new Date())
    .toString(); 
```

Enter fullscreen mode Exit fullscreen mode

得到的 SQL 看起来像这样

```
Database.dbo.SomeStoredProcedure @P1 = 'Bruce''s string', @P2 = 58, @P3 = 1, @P4 = 'Thu Sep 19 16:30:05 UTC+0800 2019' 
```

Enter fullscreen mode Exit fullscreen mode

实际上，将它交给 SQLServer 进行评估，然后处理返回的内容，这是留给读者的练习。