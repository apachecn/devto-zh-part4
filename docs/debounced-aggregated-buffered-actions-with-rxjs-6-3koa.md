# RxJS 6 的去抖聚合(缓冲)动作或如何停止让用户等待

> 原文：<https://dev.to/datadeer/debounced-aggregated-buffered-actions-with-rxjs-6-3koa>

## TLDR；

[完整代码沙盒示例](https://codesandbox.io/s/debounced-aggregated-request-osse7)

```
// (1) Add products on click
const AddProductSubject = new Subject(); 
const onClick = (event, product) => {
    AddProductSubject.next(product); 
};

// (2) Create debounce Observable as notifier for buffer
const debounceAddProduct$ = AddProductSubject.pipe(debounceTime(1500)); 

// (3) Buffer values until notifier emits a value
const AddProduct$ = AddProductSubject.pipe(
    buffer(debounceAddProduct$)
); 

// (4) Receive results
const AddProduct$Subscriber = AddProduct$.subscribe(result => {
    console.log("subscriber", result);
    saveSelection(); // triggers the loading screen
}, console.error); 
```

Enter fullscreen mode Exit fullscreen mode

## 我会学到什么？

有时候，你不想立即对用户的输入做出反应，而是等到他完成多次交互后再保存他们的进度。找出如何**缓冲**这些**与 **RxJS 6** 的交互**。

## 有什么帮助？

这可以改变乏味的用户体验。在那里你被迫等待每一个动作完成。[演示](https://codesandbox.io/s/debounced-aggregated-request-negative-example-jkwz8)

[![Negative Example - Non Debounced](img/343f54f132e01432381e06a541bedb53.png)](https://i.giphy.com/media/JUYHukWUkNHJ4CCcRj/giphy.gif)

和愉快的用户体验。其中应用程序只在你停止动作一段时间后才保存。[演示](https://codesandbox.io/s/debounced-aggregated-request-osse7)

[![Positive Example - Debounced](img/27209bf590a86245252e914ccfebc783.png)](https://i.giphy.com/media/IzLraZEVFbwFS7H5Jz/giphy.gif)

## 它是如何工作的？

(1)创建一个能够发出和接收值的新主体。
每当调用`onClick`功能时，将选定的产品推入`AddProductSubject`。(在这种情况下；每当有人点击一个产品)

```
const AddProductSubject = new Subject(); 
const onClick = (event, product) => {
    AddProductSubject.next(product); 
}; 
```

Enter fullscreen mode Exit fullscreen mode

(2)创建`debounceAddProduct$`可观察值，该可观察值仅在没有额外的值被添加到`1500`女士
的`AddProductSubject`中时发出一个值

```
const debounceAddProduct$ = AddProductSubject.pipe(debounceTime(1500)); 
```

Enter fullscreen mode Exit fullscreen mode

(3)创建`AddProduct$`可观察值，它缓冲任何发出的值，直到`debounceAddProduct$`发出一个值，并因此关闭缓冲区。

```
const AddProduct$ = AddProductSubject.pipe(
    buffer(debounceAddProduct$)
); 
```

Enter fullscreen mode Exit fullscreen mode

(4)每当用户停止选择产品至少`1500`毫秒
时，订阅`AddProduct$`可观察值并接收缓冲值

```
const AddProduct$Subscriber = AddProduct$.subscribe(result => {
    console.log("subscriber", result);
    saveSelection(); // triggers the loading screen
}, console.error); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/osse7](https://codesandbox.io/embed/osse7)

对如何改进这篇文章有什么想法吗？告诉我！我总是感谢有价值的反馈；)