# 把任何不流畅的 API 变成流畅的一键点击 tap tap

> 原文：<https://dev.to/michi/turn-any-non-fluent-api-into-a-fluent-one-tap-tap-tap-24i1>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/tap-tap-tap)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

受 Laravel 的 tap 助手的启发，我最近用 JavaScript 创建了一个很小的 tap 库。感谢 ES6 代理，这是我们可以做的另一件有趣的事情。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[MZanggl](https://github.com/MZanggl)/[taptaptap](https://github.com/MZanggl/taptaptap)

### 将不流畅的 API 转换成流畅的 API

<article class="markdown-body entry-content container-lg" itemprop="text">

## 将非流畅的 API 转换为流畅的 API

> `npm install taptaptap`

## 例子

以`Array.prototype.push`为例。它返回数组的新长度，使得链接不可能。

```
const numbers = []
numbers.push(1)
numbers.push(2)
```

Enter fullscreen mode Exit fullscreen mode

将数组包装在“tap”中允许我们将所有东西很好地链接在一起。

```
const { tap } = require('taptaptap')
const numbers = tap([])
    .push(1)
    .push(2)
```

Enter fullscreen mode Exit fullscreen mode

`tap`使用 ES6 代理来确保每个函数得到执行，但是返回最初传递的值(在本例中，是数字数组)。

* * *

另一个使用类的例子

```
class User {
    name = null
    setName(name) {
        this.name = name
    }
    getId() {
        return this.id
    }
    save() {
        // persist data
        this.id = this.createUUID
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/MZanggl/taptaptap)

以`Array.prototype.push`为例。它返回数组的新长度，使得链接不可能。

```
const numbers = []
numbers.push(1)
numbers.push(2) 
```

Enter fullscreen mode Exit fullscreen mode

将数组包装在“tap”中允许我们将所有东西很好地链接在一起。

```
const { tap } = require('taptaptap')

const numbers = tap([])
    .push(1)
    .push(2) 
```

Enter fullscreen mode Exit fullscreen mode

每个被执行的函数只是再次返回最初传递的值。

* * *

tap 还有另一个用例，它允许对公共逻辑进行分组。

想象你有一个这样的测试

```
const user = await User.query().latest().first()

expect(user.name).toBe('test name')
expect(user.bio).toBe('test bio') 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很好地将所有东西组合在一起，所以很明显`user`变量只在这里使用。

```
tap(await User.query().latest().first(), user => {
    expect(user.name).toBe('test name')
    expect(user.bio).toBe('test bio')
}) 
```

Enter fullscreen mode Exit fullscreen mode