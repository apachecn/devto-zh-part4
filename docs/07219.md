# 将数据保存在浏览器的存储器中—摘要

> 原文：<https://dev.to/runtime-revolution/saving-data-inside-the-browser-s-storage-a-summary-1b4m>

### 将数据保存在浏览器的存储器中—摘要

[![](img/d6773e3a8ee7d33e93455b9e2cab76e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m94xmI0Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Akz4cKjgM2rdsLISh) 

<figcaption>照片由[塞缪尔·泽勒](https://unsplash.com/@samuelzeller?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

每次你访问一个网站，你可能会注意到，在重复访问时，有一些元素不再出现或加载速度更快。可能是缓存发挥了它的魔力，也可能是你的浏览器内部有什么东西触发了这些行为。

一个非常简单的例子:modals 请求您同意使用 cookies。在欧盟批准 GDPR 的规定后，许多网站开始显示一个模态警告用户，为了改善他们浏览网页的体验，他们使用了 *cookies。*接受使用这些需要存储在某个地方，以便当你第二次访问该网站时，它会以不同的方式运行(在这种情况下，同意模式将不再出现)。这可以存储在服务器数据库中，但是会很复杂。取而代之的是，浏览器有自己的存储空间，而 *cookies* 只是其中一种存储类型。你的选择会被存储在你浏览器的 *cookies* 商店里。

在这篇文章中，我将尝试用一种非常简单的方式向您解释您可以使用的不同类型的浏览器存储，同时提供一些代码片段来说明它们的用法。最后，我会列出关于这个主题的更多细节的帖子的参考！

### 饼干

美味的老饼干。这可能是最著名的浏览器存储了。每个上网的人都听说过。从这个[链接](http://www.whatarecookies.com/):

> 每个 cookie 实际上是一个包含成对(键，数据)值的小型查找表，例如(名字，约翰)(姓氏，史密斯)。一旦 cookie 被服务器或客户端计算机上的代码读取，数据就可以被检索并用于适当地定制网页。

创建或获取一个 *cookie* 很容易。文档元素包含一个 API 来操作它们。下面是一些代码示例:

```
// get all cookies
cookies = document.cookie;

// add a cookie
// must be a string with the format "key=value"
document.cookie = "pokemon=bulbasaur"; 
```

### 本地存储

本地存储是一种 web 存储类型，它将数据存储为键/值对结构，没有到期日期。即使用户结束会话，本地存储中的数据也不会消失。存储的值只能是一个字符串，如果用户想要存储一个对象，他们需要在存储时*stringy 它*，在检索值时 *JSON 解析它*。

```
// get the storage
pokedex = window.localStorage;

// insert into store
pokedex.setItem('1', 'Bulbasaur');
pokedex.setItem('25', 'Pikachu');

// get from store
let pikachu = pokedex.getItem('25');
console.log(pikachu); // _Pikachu_

// remove from store
pokedex.removeItem('1');
let bulbasaur = pokedex.getItem('1');
console.log(bulbasaur); // _null_

// clear the store _aka_ reset pokedex
pokedex.clear(); 
```

### 会话存储

会话存储非常类似于本地存储。主要区别在于，在页面会话结束后，会话存储中的数据将被清除。本地存储和会话存储具有相同的 API。只需输入 pokedex = window.sessionStorage 即可访问。

### 索引编制

IndexedDB 是一种更复杂的存储类型，适用于存储大量数据和复杂对象。与 Cookies 和本地/会话存储提供的 API 相比，API 使用起来要复杂得多。正因为如此，有一些库使它更容易使用。像其他类型的存储一样，您可以使用一个**键**来存储和检索元素。为此，您需要指定数据库模式，打开一个连接并用**事务**操作数据。

下面是使用浏览器 IndexedDB:
的一个非常简单的流程片段

```
// init IndexedDB
let request= window.indexedDB.open('POKEDEX', 1);

// to create an new object store, you must define
// a function to onupgradeneeded
request.onupgradeneeded = function(event) {
  let db = request.result;
  let store = db.createObjectStore('POKEMONS', { keyPath: 'pk'});
};

// store on DB
request= window.indexedDB.open('POKEDEX', 1);
request.onsuccess = function(event) {
  let db = request.result;
  let transaction = db.transaction('POKEMONS', 'readwrite');

  let store = transaction.objectStore('POKEMONS');

  store. **put** ({
   pk: 'pikachu',
   value: {
     number: 25,
     name: 'Pikachu',
     moves: {
       "move\_1": "Volt Tackle",
       "move\_2": "Tail Whip"
     }
   }
  });

  transaction.oncomplete = function() {
    db.close();
  }
}

// get object from the store
request= window.indexedDB.open('POKEDEX', 1);
request.onsuccess = function(event) {
  let db = request.result;
  let transaction = db.transaction('POKEMONS', 'read');
  let store = transaction.objectStore('POKEMONS');

  let pokemon = store.get('pikachu');
  pokemon.onsuccess = function(event) {
    console.log(pokemon.result.value);
  }
} 
```

因为这是一个复杂的过程，即使是像上面这样的小例子，也有像 [dexie.js](https://dexie.org/) 这样的库可以帮助你使用 IndexedDB。

### 结论

正如您所看到的，浏览器提供了各种工具来为非常特殊的情况存储数据。还有另一个叫做 Web SQL 的，但是这个已经被弃用好几年了。

这只是这些工具的一个非常小的总结，所以请看看下面的一些参考资料以了解更多信息。

### 参考文献

*   [索引编制 API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)
*   [Window.sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)
*   [Window.localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage#Syntax)
*   [什么是 Cookie-计算机 Cookie-什么是 Cookie](http://www.whatarecookies.com/)
*   阅读茶叶

> ***神奇宝贝 2002–2019 神奇宝贝。1995–2019 Nintendo/Creatures Inc ./GAME FREAK Inc . TM 和神奇宝贝角色名称是任天堂的商标。*T3】**
> 
> 在这篇文章中使用神奇宝贝的内容没有侵犯版权或商标的意图。

我目前是 Runtime Revolution 的一名 Ruby-on-Rails 开发人员，仍然喜欢为 GameBoy 玩传统的口袋妖怪游戏。去查看我们的 [*网站*](https://www.runtime-revolution.com/?source=post_page---------------------------) *了解我们更多。*

* * *