# NodeJs 单一注射器

> 原文：<https://dev.to/elasticrash/nodejs-singleton-injector-24n5>

我要讲一个我最常用的模式。

当被赋予使用 NodeJs 和 Express 编写 API 的任务时，我喜欢实现下面的模式，我倾向于称之为 Singleton Injector(是的，我喜欢编造名字),它允许我轻松地管理 Singleton。

这种模式当然是单例模式，混合了依赖注入模式。

我的应用程序入口点如下所示

```
(async () => {
    await addAndInitDepedencies();
    // express stuff. configure and start server
})();

async function addAndInitDepedencies() {
    injector.addService(MySql, DatabaseConnection.MySql);
    const mysql = injector.getService<MySql>(MySql);
    mysql.init();

    // other singletons that follow the same pattern
} 
```

Enter fullscreen mode Exit fullscreen mode

所以当我需要使用 mysql 连接池时，我可以简单地做

```
 this.mysql = Injector.getInstance().getService<MySql>(MySql);
 this.mysql.executeQuery(sql, []); 
```

Enter fullscreen mode Exit fullscreen mode

这个臭名昭著的单例注入器就是下面这个非常简单的类

```
export class Injector {
    private static instance: Injector;
    private _di: any = {};

    private constructor() { }

    public static getInstance() {
        if (!Injector.instance) {
            Injector.instance = new Injector();
        }
        return Injector.instance;
    }

    public addService(dependency, args) {
        if (this.serviceExists(dependency.name)) { return; }
        const construction = new dependency.prototype.constructor(args);
        this._di[dependency.name] = construction;
    }

    public getInjector() {
        return this._di;
    }

    public getService<T>(dependency): T {
        if (!this.serviceExists(dependency.name)) { return {} as T; }
        const di: any = this._di as T;
        return this._di[dependency.name];
    }

    private serviceExists(name: string) {
        return this._di[name] ? true : false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，你提供你希望使用的`class`以及任何可能需要的参数，它调用它的`constructor`并返回初始化的对象。你可以随时通过使用`class`作为`type`和`parameter`来找回任何一个单身者。

没有花哨的东西，只是让你的生活更轻松的小东西。

老实说，直到两年前，我只使用 Node 编写命令行工具(比 bash 好得多)，因为我的背景主要是 c#和 c++。这是一次有趣的旅行。