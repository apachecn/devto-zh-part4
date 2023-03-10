# CommonJS 和 ES6 模块导入的完整和部分模仿/窥探

> 原文：<https://dev.to/hugo__df/jest-full-and-partial-mock-spy-of-commonjs-and-es6-module-imports-53gf>

> JavaScript 导入/要求模块测试用 Jest 做什么和不做什么

范例库可在[github.com/HugoDF/mock-spy-module-import](https://github.com/HugoDF/mock-spy-module-import)获得。

这篇文章讲述了如何用 Jest 实现不同类型的模块模仿场景。

从简单的导入拦截，到如何终止内部函数调用或只模仿模块的一部分(通过监视…)。

这些方法在比 Jest 官方文档显示的更具体的情况下有效。

严格来说，这不是一个 Jest 测试指南，同样的原则可以应用于任何需要模拟 CommonJS 或 es 模块的应用程序/测试。

## 为上下文:导入类型

现代 JavaScript 有两种类型的导入:

*   CommonJS:node . js’内置的导入系统使用对全局`require('module-y')`函数的调用，npm 上的包公开了一个 CommonJS 兼容的入口文件。
*   ES 模块(ESM):由 ECMAScript 标准定义的模块。它使用`import x from 'module-y'`语法。

也有像 RequireJS 和 AMD 这样的(传统)模块加载器，但是 CommonJS 和 ESM 是当前和未来最广泛的 JavaScript 模块定义格式。

ES 模块有两种类型的导出:命名导出和默认导出。

一个命名的导出看起来像这样:`export function myFunc() {}`或`export const a = 1`。

默认导出如下所示:`export default somethingAlreadyDefined`。

命名的导出可以使用看起来(和工作起来)有点像对象析构的语法来单独导入:`import { myFunc, a } from './some-module'`。

也可以导入为名称空间:`import * as moduleY from './module-y'`(现在可以用`moduleY.myFunc()`和`moduleY.a`)。

默认导出只能用默认导入:`import whateverIsDefault from './moduleY'`导入。

这两种进口也可以混合搭配，[参见 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 上的`import`文档。

## 拦截进口

当进行单元测试时，您可能想要存根/模拟出拥有自己的单元测试的模块。

开玩笑的说，这是用`jest.mock('./path/of/module/to/mock', () => ({ /* fake module */ }))`完成的。

在这种情况下，CommonJS 和 ES6 模块模拟看起来非常相似。有一些一般性的问题。

首先，你嘲笑的(第二个参数`jest.mock`)是模块的工厂。即。这是一个返回模拟模块对象的函数。

其次，如果您想从`jest.mock`的父作用域中引用一个变量(例如，您想定义您的模拟模块实例)，您需要在变量名前面加上前缀`mock`。比如:

```
const mockDb = {
get: jest.fn(),
set: jest.fn()
};
const db = mockDb
// This works
jest.mock('./db', () => mockDb);
// This doesn't work
jest.mock('./db', () => db); 
```

最后，您应该在导入被测模块之前调用`jest.mock` *(它本身导入了我们刚刚嘲笑的模块)。*

实际上，Babel ESM-> CommonJS trans pilation 会提升`jest.mock`调用，所以这通常不是问题🤷‍♀.

### 普通案件

完整的测试和测试代码在[examples/intercept-imports-cjs](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/intercept-imports-cjs)中。

相关片段如下:

```
jest.mock('./db', () => ({
get: jest.fn(),
set: jest.fn()
}));
const mockDb = require('./db');
const {addTodo, getTodo} = require('./lib');
test('CommonJS > addTodo > inserts with new id', async () => {
await addTodo({name: 'new todo'});
expect(mockDb.set).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('CommonJS > getTodo > returns output of db.get', async () => {
mockDb.get.mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(mockDb.get).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

### ES 模块默认导出

完整的测试和测试代码在[examples/intercept-imports-ESM-default](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/intercept-imports-esm-default)中。

```
import mockDb from './db';
import lib from './lib';
jest.mock('./db', () => ({
get: jest.fn(),
set: jest.fn()
}));
const {addTodo, getTodo} = lib;
test('ESM Default Export > addTodo > inserts with new id', async () => {
await addTodo({name: 'new todo'});
expect(mockDb.set).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('ESM Default Export > getTodo > returns output of db.get', async () => {
mockDb.get.mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(mockDb.get).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

### ES 模块命名导出

完整的测试和被测代码位于[examples/intercept-imports-ESM-named](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/intercept-imports-esm-named)。

```
import * as mockDb from './db';
import {addTodo, getTodo} from './lib';
jest.mock('./db', () => ({
get: jest.fn(),
set: jest.fn()
}));
test('ESM named export > addTodo > inserts with new id', async () => {
await addTodo({name: 'new todo'});
expect(mockDb.set).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('ESM named export > getTodo > returns output of db.get', async () => {
mockDb.get.mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(mockDb.get).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

## 刺探/存根对内部模块函数的调用

> 警告:你不应该刺探/存根模块内部，那是你的测试深入到实现中，这意味着测试和被测代码是紧密耦合的

概念:“呼通”(与嘲讽相对)。

没有被导出的内部/私有/辅助函数应该通过它的公共接口来测试，例如。不是通过调用它，因为它没有被导出，而是通过调用调用它的函数。

测试其功能是消费所述助手的功能的测试的责任。

这适用于以下情况:

*   你没有时间提取函数，但是复杂度太高，无法测试(从被测函数到内部函数)
    *   解决方案:你可能应该*利用*时间
*   内部函数属于所述模块，但是它的复杂性使它难以测试。
    *   解决方案:您可能应该提取它
*   这个函数不是严格意义上的内部函数，它被导出并经过单元测试，force 调用会重复测试。
    *   解决方案:你一定要把它提取出来

在下面的例子中，我们将寻找 stub/mock/spy 内部的`makeKey`函数。这纯粹是出于学术目的，因为我们在上一节已经展示了如何通过调用`getTodo`进行**测试。**

在这种情况下，我们正在测试`expect(mockDb.get).toHaveBeenCalledWith('todos:1');`(参见[examples/intercept-imports-cjs/lib . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/intercept-imports-cjs/lib.jest-test.js))。`todos:1`键的生成是`makeKey`的功能，这是通过**调用**进行测试的一个例子。

### CommonJS

#### 内部函数不可模仿的模块

代码清单摘自[examples/spy-internal-calls-cjs/lib . fail . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-cjs/lib.fail.js)。

```
const db = require('./db');
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
function getTodo(id) {
return db.get(makeKey(id));
}
module.exports = {
makeKey,
getTodo
}; 
```

正如您在运行[examples/spy-internal-calls-cjs/lib . fail . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-cjs/lib.fail.jest-test.js)测试时所看到的，没有办法拦截对`makeKey`的调用。

#### 一个内部函数*可以被*模仿的模块

代码清单摘自[examples/spy-internal-calls-cjs/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-cjs/lib.js)。

```
const db = require('./db');
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
const lib = {
// Could also define makeKey inline like so:
 // makeKey(key) { return `${keyPrefix}:${key}` },
 makeKey,
getTodo(id) {
return db.get(lib.makeKey(id));
}
};
module.exports = lib; 
```

#### 嘲讽/刺探内部功能

代码清单摘自[examples/spy-internal-calls-cjs/lib . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-cjs/lib.jest-test.js)

```
// ignore setup code
test("CommonJS > Mocking destructured makeKey doesn't work", async () => {
const mockMakeKey = jest.fn(() => 'mock-key');
makeKey = mockMakeKey;
await getTodo(1);
expect(makeKey).not.toHaveBeenCalled();
expect(mockDb.get).not.toHaveBeenCalledWith('mock-key');
});
test('CommonJS > Mocking lib.makeKey works', async () => {
const mockMakeKey = jest.fn(() => 'mock-key');
lib.makeKey = mockMakeKey;
await getTodo(1);
expect(mockMakeKey).toHaveBeenCalledWith(1);
expect(mockDb.get).toHaveBeenCalledWith('mock-key');
});
test('CommonJS > Spying lib.makeKey works', async () => {
const makeKeySpy = jest
.spyOn(lib, 'makeKey')
.mockImplementationOnce(() => 'mock-key');
await getTodo(1);
expect(makeKeySpy).toHaveBeenCalled();
expect(mockDb.get).toHaveBeenCalledWith('mock-key');
}); 
```

从上面我们可以看到，有了上一节的设置(参见[examples/spy-internal-calls-cjs/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-cjs/lib.js))，我们既可以用一个 mock 替换`lib.makeKey`的实现，又可以对其进行窥探。

我们仍然无法替换对它的引用。这是因为当我们析构`lib`来提取`makeKey`时，我们创建了引用 ie 的副本。`makeKey = newValue`改变了我们测试文件中的`makeKey`变量的实现，但是没有替换`lib.makeKey`的行为(这是`getTodo`正在调用的)。

举例说明:

```
const lib = require('./lib');
let {makeKey} = lib;
makeKey = 'something';
// `lib.makeKey` and `makeKey` are now different... 
```

### ES 模块

#### 命名出口的难度

在 ES6 模块的情况下，从语义上来说，很难用命名导出的方式设置代码，下面的代码不太适用:

```
import db from './db';
const keyPrefix = 'todos';
export const makeKey = key => `${keyPrefix}:${key}`;
export function getTodo(id) {
return db.get(makeKey(id));
} 
```

代码清单摘自[examples/spy-internal-calls-ESM/lib . named-export . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.named-export.js)，测试显示没有简单的方法来模仿/窥探`makeKey`位于[examples/spy-internal-calls-ESM/lib . named-export . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.named-export.jest-test.js)

#### 默认导出的不可拆模块

代码列表摘自[examples/spy-internal-calls-ESM/lib . default-export . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.default-export.js)。

```
import db from './db';
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
function getTodo(id) {
return db.get(makeKey(id));
}
const lib = {
makeKey,
getTodo
};
export default lib; 
```

测试显示没有简单的方法来模仿/窥探`makeKey`在[examples/spy-internal-calls-ESM/lib . default-export . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.default-export.jest-test.js)。

这不起作用的原因与 CommonJS 示例相同，`makeKey`被直接引用，并且该引用不能从模块外部修改。

任何试图导入它的东西都会复制一份，因此不会修改内部引用。

#### ESM 模拟/间谍设置

代码清单摘自[示例/spy-internal-calls-ESM/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.js)

```
import db from './db';
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
const lib = {
// Could also define makeKey inline like so:
 // makeKey(key) { return `${keyPrefix}:${key}` },
 makeKey,
getTodo(id) {
return db.get(lib.makeKey(id));
}
};
export default lib; 
```

通过上述测试位于[examples/spy-internal-calls-ESM/lib . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-internal-calls-esm/lib.jest-test.js)

**注意**，可以对指定的出口做类似的事情:

```
import db from './db';
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
export const lib = {
// Could also define makeKey inline like so:
 // makeKey(key) { return `${keyPrefix}:${key}` },
 makeKey,
getTodo(id) {
return db.get(lib.makeKey(id));
}
}; 
```

关键点在于导出一个`lib`对象，并在调用`makeKey`时引用同一个对象。

### 模拟内部与 ESM/CommonJS 相同

能够模仿模块的一部分完全是关于引用的。

如果一个函数调用另一个函数时使用了一个从模块外部(更确切地说是从我们的测试中)无法访问的引用，那么它是无法被模仿的。

## 通过“引用模块”来窥探模块的导入或模仿部分

> 警告:这将导致你改变你写代码的方式来适应特定类型的测试。
> 
> 如果有人决定获取模块函数的副本，而不是直接调用`module.fn()`，这将会被打破。

### CommonJS:用 Jest 窥探导入/模仿模块的一部分

代码清单摘自[examples/spy-module-cjs/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-cjs/lib.js)。

请注意`db`模块是如何在没有析构的情况下导入的，以及对它的任何调用是如何使用`db.method()`调用完成的。

```
const db = require('./db');
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
let autoId = 1;
async function addTodo(todo) {
const id = autoId++;
const insertable = {
...todo,
id
};
await db.set(makeKey(id), insertable);
}
function getTodo(id) {
return db.get(makeKey(id));
}
module.exports = {
addTodo,
getTodo
}; 
```

我们现在可以使用下面的方法来监视`db.method`:

```
const db = require('./db');
const {addTodo, getTodo} = require('./lib');
beforeEach(() => jest.clearAllMocks());
test('CommonJS > addTodo > inserts with new id', async () => {
const dbSetSpy = jest.spyOn(db, 'set').mockImplementation(() => {});
await addTodo({name: 'new todo'});
expect(dbSetSpy).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('CommonJS > getTodo > returns output of db.get', async () => {
const dbGetSpy = jest.spyOn(db, 'get').mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(dbGetSpy).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

注意我们没有调用`jest.mock()`。相反，当我们需要修改`db`模块实现时，我们只是模仿/监视模块的特定功能。

### ES6 模块:用 Jest 窥探导入/模仿模块的一部分

#### 默认出口

假设我们的`db.js`模块以如下方式导出(参见[examples/spy-module-ESM-default/db . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-default/db.js)):

```
const data = {};
async function get(k) {
return data[k];
}
async function set(k, v) {
data[k] = v;
}
const db = {
get,
set
};
export default db; 
```

然后我们可以如下导入它(代码清单摘自[examples/spy-module-ESM-default/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-default/lib.js)):

```
import db from './db';
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
let autoId = 1;
function addTodo(todo) {
const id = autoId++;
const insertable = {
...todo,
id
};
return db.set(makeKey(id), insertable);
}
function getTodo(id) {
return db.get(makeKey(id));
}
const lib = {
addTodo,
getTodo
};
export default lib; 
```

窥探模块的导入/模仿部分可以通过以下方式实现(完整代码在[examples/spy-module-ESM-default/lib . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-default/lib.jest-test.js)):

```
import db from './db';
import lib from './lib';
const {addTodo, getTodo} = lib;
beforeEach(() => jest.clearAllMocks());
test('ESM Default Export > addTodo > inserts with new id', async () => {
const dbSetSpy = jest.spyOn(db, 'set').mockImplementationOnce(() => {});
await addTodo({name: 'new todo'});
expect(dbSetSpy).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('ESM Default Export > getTodo > returns output of db.get', async () => {
const dbGetSpy = jest.spyOn(db, 'get').mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(dbGetSpy).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

注意我们没有用一个`jest.mock()`调用来模拟`db`模块。我们再次监视我们对特定测试感兴趣的方法。

我们利用`mockImplementationOnce()`来避免调用真正的函数(你可能不总是想这么做)。

#### 命名 exports+" import * as alias from ' module-name ' "

> 注意:我没有阅读完整的规范，事实上这可能是巴别塔 ES2015 模块翻译的一个怪癖

假设我们已经如下定义了`db.js`(使用命名导出，参见[examples/spy-module-ESM-named/db . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-named/db.js)中的文件):

```
const data = {};
export async function get(k) {
return data[k];
}
export async function set(k, v) {
data[k] = v;
} 
```

我们可以使用别名`import * as db from './db'`(代码清单摘自[examples/spy-module-ESM-named/lib . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-named/lib.js)):
导入所有命名的导出

```
import * as db from './db';
const keyPrefix = 'todos';
const makeKey = key => `${keyPrefix}:${key}`;
let autoId = 1;
export function addTodo(todo) {
const id = autoId++;
const insertable = {
...todo,
id
};
return db.set(makeKey(id), insertable);
}
export function getTodo(id) {
return db.get(makeKey(id));
} 
```

对 db.set 和 db.get 的调用可以使用下面的方法进行窥探/模仿(完整代码测试文件位于[examples/spy-module-ESM-named/lib . jest-test . js](https://github.com/HugoDF/mock-spy-module-import/tree/master/examples/spy-module-esm-named/lib.jest-test.js)):

```
import * as db from './db';
import {addTodo, getTodo} from './lib';
beforeEach(() => jest.clearAllMocks());
test('ESM named export > addTodo > inserts with new id', async () => {
const dbSetSpy = jest.spyOn(db, 'set').mockImplementationOnce(() => {});
await addTodo({name: 'new todo'});
expect(dbSetSpy).toHaveBeenCalledWith('todos:1', {name: 'new todo', id: 1});
});
test('ESM named export > getTodo > returns output of db.get', async () => {
const dbGetSpy = jest.spyOn(db, 'get').mockResolvedValueOnce({
id: 1,
name: 'todo-1'
});
const expected = {
id: 1,
name: 'todo-1'
};
const actual = await getTodo(1);
expect(dbGetSpy).toHaveBeenCalledWith('todos:1');
expect(actual).toEqual(expected);
}); 
```

## 进一步阅读

带有示例的存储库位于[github.com/HugoDF/mock-spy-module-import](https://github.com/HugoDF/mock-spy-module-import)。

查看更多关于 Hugo 代码的[测试](https://codewithhugo.com/tags/testing)和[笑话](https://codewithhugo.com/tags/jest)帖子。

你可以在[enterprise node . js 和 JavaScript 时事通讯档案](https://buttondown.email/hugo/archive)中找到更多 Jest/testing/JavaScript 内容。

克里斯·贝克尔