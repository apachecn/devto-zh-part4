# 在 Angular、Typescript、Javascript 中深度克隆对象

> 原文：<https://dev.to/leonelngande/deep-cloning-objects-in-angular-typescript-javascript-1ome>

Javascript 中克隆对象的一些技术要么使用[对象析构](https://javascript.info/destructuring-assignment)，要么结合使用 [JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 和 [JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 。

```
const objectA = { foo: { bar: 'baz' } }; // initial value of objectA
const cloneOfObjectA = {...objectA}; // objectA destructured to create cloneOfObjectA

const objectB = { foo: { bar: 'baz' } }; // initial value of objectB
const cloneOfObjectB = JSON.parse(JSON.stringify(objectB)); // create a clone of objectB using JSON.parse() and JSON.stringify() 
```

如果你想深度克隆你的对象，上面的每种方法都有局限性。

使用对象`const a = {...b}`的扩展操作符(即 Object.assign)进行对象析构只会对那些对象进行浅层复制，同时使用 JSON 方法会转储原型链和对象上的任何方法(更多[此处](http://www.zsoltnagy.eu/cloning-objects-in-javascript/))。

我目前正在学习 Pluralsight 上的[Angular Architecture and Best Practices](https://www.pluralsight.com/courses/angular-architecture-best-practices)课程， [@DanWahlin](https://twitter.com/DanWahlin) (它的作者)提到了一个非常酷的轻量级库来实现这一点:[克隆](https://github.com/pvorb/clone)。

它提供了对象、数组、数字、字符串、地图、集合、承诺等简单的深度克隆。在 JavaScript 中，有 0 个依赖项，在撰写本文时有超过 6.07 亿次下载！

要安装它，只需运行`npm install clone`，按照[自述文件](https://github.com/pvorb/clone)获取更多使用说明🙂。

这里有一个在使用 Angular 和 Typescript 时用于库的包装器服务:

```
import {Injectable} from '@angular/core';
import * as clone from 'clone';

@Injectable({providedIn: 'root'})
export class ClonerService {

    deepClone<T>(value): T {
        return clone<T>(value);
    }

} 
```

还有一个(相当琐碎的)用法示例:

```
 const objectA = { foo: { bar: 'baz' } }; // initial value of objectA
    constructor(private clonerService: ClonerService) {
        const cloneOfObjectA = this.clonerService.deepClone(this.objectA);
    } 
```

希望这有助于你深入的克隆冒险，快乐编码！