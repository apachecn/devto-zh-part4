# 无限滚动 con Rxjs

> 原文：<https://dev.to/leoalipazaga/infinite-scroll-con-rxjs-3mlg>

我最近开始研究著名的 xjs 书店，我觉得它在解决我们经常作为开发商面临的功能方面的巨大潜力确实令人吃惊。无限滚动就是其中之一。在这篇文章中，我将向大家介绍如何使用 rxjs 逐步进行无限滚动。

### 那么，我们需要吗？

我特别喜欢 jsfiddle，因为它很轻，但我让您自由选择您最喜欢的文本编辑器(VSCode、superscript text、code code code、repl.it 等)。页:1。Psdta:必须安装 xjs 库。

### 添加一些 HTML 和 CSS

我不会花太多时间在 css 或 html 上，因为它不是 post 的中心点，所以您可以将样式添加到 CSS 或 html 中，使其保持关闭状态。在这种情况下，我只会在 HTML 中添加一个容器[https://jsfiddle.net/jestea/v582hg6e/3//embedded/html,css//dark](https://jsfiddle.net/jestea/v582hg6e/3//embedded/html,css//dark)

### 存储容器节点并导入 xjs

我们要做的第一件事是导入 xjs 库并存储容器节点。没什么难的。

```
const Observable = Rx.Observable;
const container = document.getElementById('container'); 
```

### 现在如果好的来了，一步一步来。

我们感兴趣的是用户滚动时的滑动，所以我们需要听听这个事件，scroll。使用 rxjs 相当简单。

```
Observable
  .fromEvent(container, 'scroll') 
```

太好了，现在是时候“思考”并决定每次用户滚动时我们需要哪些值来消费服务了。这有两个标准。

1.  仅当用户向下滑动时才应消耗服务。也就是说，目前的位置必须大于先前的位置。太好了
2.  现在，我们不能消费服务直到达到某个特定的点，一个限度。

为了达到这些标准，我们需要 scroll 事件返回给我们的对象中的三个属性。`clientHeight, scrollHeight, scrollTop`。
所以我会简单地描述一下每一个特性所代表的价值。

*   **客户端高度**:容器高度，不包括可滚动部分。起始(固定)高度。
*   **scrollTop** 栏在 y 轴上的位置。
*   **滚动高度**:容器的总高度，包括滚动部分。子代成员增加时动态。

```
Observable
  .fromEvent(container, 'scroll')
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  })) 
```

*完美，为我们服务的每一个属性？*

#### 数学

如果用户向下滑动，当前位置和以前位置之间的差异将为我们提供信息。

```
function isScrollDown(beforePosition, currentPosition) {
  beforePosition.scrollTop < currentPosition.scrollTop;
} 
```

而钢筋位置与高度差异(scrollHeight 和 clienteheight)之间的关系会告诉我们是否超过了的限制(我们将定义限制)。

```
function setThreshold(threshold) {
  return function hasPassedThreshold(currentPosition) {
    return currentPosition.scrollTop * 100 /
      (currentPosition.scrollHeight -
       currentPosition.clientHeight) > threshold;
  }
} 
```

有了我们定义的两个标准，我们就可以开始筛选我们感兴趣的立场。

```
Observable
  .fromEvent(container, 'scroll')
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  }))
  .pairwise() // emite el valor anterior y el actual en un array. 
  .filter(positions => isScrollDown(positions[0], positions[1]) && 
  setThreshold(80)(positions[1])) 
```

### 装载机

在容器的末端添加一个简单的加载程序。

```
const toogleLoading = (function (container) {
  const loading = document.createElement('p');
  loading.classList.add('bold', 'text-center');
  loading.innerText = 'Loading...';
  return function toogleLoading(showLoader) {
  showLoader ? container.appendChild(loading) : loading.remove();
}
})(container); 
```

现在，我们将根据所设置的条件，每次显示 loader 时，scrollbar 的位置都返回 true。为此，我们使用 do 运算符。

```
Observable
  .fromEvent(container, 'scroll')
  .takeWhile(res => nextUrl)
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  }))
  .pairwise()
  .filter(positions => isScrollDown(positions[0], positions[1]) && setThreshold(80)(positions[1]))
  .do(() => toogleLoading(true)) // show loader 
```

### 消费服务

服务消费必须伴随着加载程序的显示。我要说的是，一项服务可能很快，也可能相当缓慢。在前端，我们必须向用户显示实际上正在加载数据，而我们是通过加载程序加载数据的。但是，当服务响应快时加载器只显示一会儿，看起来不太好。为了获得更多信息，我发现了这个大型的 [post](https://codeburst.io/rxjs-show-spinner-for-a-minimum-amount-of-time-807ac6b23227) 处理如何在最短的时间内添加一个加载器。

```
Observable
  .fromEvent(container, 'scroll')
  .takeWhile(res => nextUrl)
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  }))
  .pairwise()
  .filter(positions => isScrollDown(positions[0], positions[1]) && setThreshold(80)(positions[1]))
  .do(() => toogleLoading(true)) // show loader
  .switchMap(() => Observable.combineLatest(Observable.timer(1000), Observable.ajax({
    url: nextUrl,
    method: 'GET'
  })))
  .map(combine => combine[1])
  .catch(console.error) 
```

#### 大脑较慢

*   **switchMap** 让我们订阅从内部可观察到的新观测值(在本例中为组合测试)。当新的观测到时，旧的将被取消。
*   **组合测试**，发出每个观测值的最后值。每个观测值发出的值存储在一个阵列中。
*   **定时器**按规定的时间顺序发射多个
*   **ajax** 按照观测者的概念创建 ajax 请求
*   **map** 根据作为参数传递的项目函数转换每个输出值
*   **catch** 处理可能出现的错误

#### 马内埃尔回应

我们使用运算子*do 来执行*侧效应*(变更任何变数的值或执行任何功能)。服务响应将返回一个包含以下 url 的扩展对象，以便与所有 pokemones 一起查询。在这种情况下，我们使用运算子 **do** 来更新我们的端点。另一方面，我们使用运算子 **map** 只获取响应对象的属性 *results* 。* 

```
Observable
  .fromEvent(container, 'scroll')
  .takeWhile(res => nextUrl)
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  }))
  .pairwise()
  .filter(positions => isScrollDown(positions[0], positions[1]) && setThreshold(80)(positions[1]))
  .do(() => toogleLoading(true)) // show loader
  .switchMap(() => Observable.combineLatest(Observable.timer(1000), Observable.ajax({
    url: nextUrl,
    method: 'GET'
  })))
  .map(combine => combine[1])
  .catch(console.error)
  .do(res => (nextUrl = res.response.next))
  .map(res => res.response.results) 
```

### 订户编号

我们终于要订阅我们引人注目的卷轴了。而对于我们的*成功，我们必须停止显示装载，并在容器中加入所有 pokemones。* 

```
Observable
  .fromEvent(container, 'scroll')
  .takeWhile(res => nextUrl)
  .map(e => ({
    scrollTop: e.target.scrollTop,
    scrollHeight: e.target.scrollHeight,
    clientHeight: e.target.clientHeight
  }))
  .pairwise()
  .filter(positions => isScrollDown(positions[0], positions[1]) && setThreshold(80)(positions[1]))
  .do(() => toogleLoading(true)) // show loader
  .switchMap(() => Observable.combineLatest(Observable.timer(1000), Observable.ajax({
    url: nextUrl,
    method: 'GET'
  })))
  .map(combine => combine[1])
  .catch(console.error)
  .do(res => (nextUrl = res.response.next))
  .map(res => res.response.results)
  .subscribe(pokemons => {
    toogleLoading(false);
    container.innerHTML += pokemons.map(pokemon =>
                                                pokemon.name).join('<br>')
  }); 
```

### Código completo

任何疑问、问题或反馈都可以留下您的意见。不要忘记学习和**分享**下次见。
[https://jsfiddle.net/jestea/v582hg6e/4//embedded//dark](https://jsfiddle.net/jestea/v582hg6e/4//embedded//dark)*