# 答:组件呈现前事件处理程序不设置状态

> 原文：<https://dev.to/christocarr/answer-event-handler-not-setting-state-before-component-renders-594i>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答回复:组件呈现前事件处理程序未设置状态](https://stackoverflow.com/questions/57962219/event-handler-not-setting-state-before-component-renders/57962294#57962294)

Sep 16 '19[![](img/e3f0373ec76330150a340eacd410b600.png)0![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/57962219/event-handler-not-setting-state-before-component-renders/57962294#57962294) </header>

你不应该在`setState`回调函数中使用`setState`。相反，它应该返回新的状态。这个:

```
onHandlePrint = (pdf) => {
    this.setState({pdf}, () => {
      this.setState({pdfStatus: true})
    });
  }; 
```

应该是:

```
onHandlePrint = (pdf) => {
    this.setState(() => {pdf, pdfStatus: true});
  }; 
```

但是如果你真的不需要…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/57962219/event-handler-not-setting-state-before-component-renders/57962294#57962294)</button>