# 你不需要 jQuery

> 原文：<https://dev.to/kdinnypaul/you-don-t-need-jquery-1a18>

想知道为什么是 jQuery 吗？是世界上使用最多的 Javascript 库，同时也是最受批评的一个。

嗯，jQuery 有很多优点，比如它很容易使用，它可以用很少的代码操作网页，并且它可以跨浏览器兼容，那么我们为什么不应该使用它呢？唯一的问题是，它对 web 性能有很大的影响。

即使是一秒钟的延迟也会严重影响你的网站，研究表明，网站加载时间的一秒钟延迟将导致 7%的转化率损失，对于亚马逊来说，一秒钟的延迟将导致每年 16 亿美元的损失。

## jQuery 为什么慢？

首先，为了使用 jQuery，您需要添加一个外部 javascript 文件，如 jquery.min.js，大小为 30 kb，gzipped，这将导致 7 毫秒的延迟，这看起来并不多，但它会增加，因为您必须在 javascript 代码之前添加它，大多数人会将它添加到 header 中，所以即使 7 毫秒的延迟也很多，如果您使用移动连接或慢速互联网，则延迟会急剧增加。

当然，如果你已经添加了 google 的 jQuery url，比如 https://Ajax . Google APIs . com/Ajax/libs/jQuery/3 . 1 . 0/jQuery . min . js，那么它可能会被浏览器缓存，因为许多网站使用相同的 url，加载速度会更快，但它仍然是对其他域的外部调用，而不是你自己的网站，这将增加一点延迟。

除了由外部文件引起的延迟，jQuery 比纯 JavaScript 慢得多，例如，当将一个节点附加到 DOM 时，只需要对直接与 DOM API 交互的 Vanilla javscript 进行一次调用，而 jQuery 运行许多操作来操作 DOM
,这让我告诉你很多事情，没有人喜欢使用缓慢的网站，我的意思是用户可以接受最初的延迟，但他们不会容忍他们与网站交互时的延迟。

## 结论

那么我们该怎么办呢？，我们应该从每个项目中删除 jQuery 吗？，用纯 javascript 替换 jQuery 并不容易。

我们可以避免使用它，或者用一些轻量级的 jQuery 替代它，如 [Cash](https://github.com/kenwheeler/cash) 或 [Zepto.js](https://github.com/madrobby/zepto) ，或者用纯 javascript 完全替代 jQuery，因为它们受所有现代浏览器(IE9+)支持，并且比 jQuery 更快。

如果您想用纯 javascript 替换 jQuery，下面是纯 javascript 中 jQuery 的一些重要替代代码。

## JavaScript 获取请求

```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onload = function() {
  if (request.status >= 200 && request.status < 400) {
    // Success!
    var resp = request.responseText;
  } else {
    // We reached our target server, but it returned an error
  }
};
request.onerror = function() {
  // There was a connection error of some sort
};
request.send(); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 帖子

```
var request = new XMLHttpRequest();
request.open('POST', '/my/url', true);
request.setRequestHeader('Content-Type',
'application/x-www-form-urlencoded; charset=UTF-8');
request.send(data); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript JSONP 跨域调用请求

```
function onFetchComplete(data) {console.log(data);}

var script = document.createElement('script');

script.src = 'https://en.wikipedia.org/w/api.php?action=query&generator=random'+
'&grnnamespace=0&prop=extracts'+
'&exchars=500&format=json&callback=onFetchComplete';

document.body.appendChild(script); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 隐藏&显示

```
el.style.display = 'none';//HIDE
el.style.display = '';//SHOW 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 查找

```
el.querySelectorAll(selector); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 后

```
el.insertAdjacentHTML('afterend', htmlString); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 之前

```
el.insertAdjacentHTML('beforebegin', htmlString); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 追加

```
parent.appendChild(el); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 前置

```
parent.insertBefore(el, parent.firstChild); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 删除

```
el.parentNode.removeChild(el); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 获取 Html

```
el.innerHTML 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 获取文本

```
el.textContent 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 获取属性

```
el.getAttribute('attributeName'); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 获取样式

```
window.getComputedStyle(el, null).getPropertyValue("background-color"); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 获取外层 Html

```
el.outerHTML 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 添加类

```
if (el.classList)
  el.classList.add(className);
else
  el.className += '  ' + className; 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 删除类

```
if (el.classList)
  el.classList.remove(className);
else
  el.className = el.className.replace
(new RegExp('(^|\\b)' + className.split('  ').join('|') 
+ '(\\b|$)', 'gi'), '  '); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 设置 Html

```
el.innerHTML = string; 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 设置文本

```
el.textContent = string; 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 设置属性

```
el.setAttribute('attributeName','attributeValue'); 
```

Enter fullscreen mode Exit fullscreen mode

## JavaScript 设置样式

```
// Use a class if possible
el.style.borderWidth = '20px'; 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

[http://youmightnotneedjquery.com/](http://youmightnotneedjquery.com/)
[https://www . fast company . com/1825005/how-one-second-could-cost-Amazon-160 亿-sales](https://www.fastcompany.com/1825005/how-one-second-could-cost-amazon-16-billion-sales)
[https://medium . com/@ trombino . Marco/you-may-not-need-jquery-a-2018-performance-case-study-aa 6531d 0 b 0c 3](https://medium.com/@trombino.marco/you-might-not-need-jquery-a-2018-performance-case-study-aa6531d0b0c3)