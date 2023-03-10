# 创建 JavaScript UI 模块的简单模式

> 原文：<https://dev.to/pnodev/a-simple-pattern-for-creating-javascript-ui-modules-o9f>

在过去 6 年左右的时间里，我花了很多时间编写小的 JavaScript 模块来控制 UI 组件。你可以在大多数现代网页上找到的元素，比如标签页、手风琴、图片库——应有尽有。

我一次又一次看到(也是我自己犯的)的一个常见错误是在组件的 JavaScript 中对 DOM 引用的弱处理。

## 一个对象来统治他们所有人——或者:让那里有混乱

这是我在上一份工作中得到的许多遗留代码库中发现的一种模式(同样，我也对过去编写这样的代码感到内疚)。考虑下面这个简单 UI 元素的标记，比如手风琴:

```
<div class="m-accordion" id="m-accoridon-1">
    <h2 class="m-accordion__header" id="m-accordion-header-1">
        <button class="m-accordion__toggle" type="button" data-toggle="collapse" data-target="#accordion-panel-1" aria-expanded="true" aria-controls="accordion-panel-1">
            First item
        </button>
    </h2>

    <div id="accordion-panel-1" class="m-accordion__panel" aria-labelledby="m-accordion-header-1" data-parent="#m-accoridon-1">
        <!--    some accorion content    -->
    </div>
</div> 
```

现在，如你所见，这里有一些我们需要参考的元素。我们至少需要对折叠开关(添加点击事件监听器)和相关面板(点击时显示或隐藏它们)的引用。这里有一种方法可以解决这个问题:

```
const toggles = document.querySelectorAll('.m-accordion__header');
const panels = document.querySelectorAll('.m-accordion__panel');

toggles.forEach(toggle => {
    toggle.addEventListener('click', openPanel);
});

[…] 
```

在这种方法中，我们将页面上的所有切换和面板存储在一个常量中。这在简化的用例中工作得很好。假设我们在同一个页面上有这个元素的多个实例。代码仍然可以工作，但是事情已经很难跟踪了。

所以，让我们增加另一个层次的复杂性。假设我们想通过数据属性为 accordion 设置不同的选项。现在，您必须跟踪所有的`.m-accordion`元素，存储它们不同的选项集，甚至更糟:将所有这些选项映射到切换和面板。

这很快就失控了。所以让我们试着找到一个更好的方法。

## 每个元素一个模块

模块是让这种混乱变得有序的好方法。让我们试着用一种方式组织我们的代码，一个模块的一个实例只负责一个元素的一个实例。

第一个例子中痛苦的主要原因是，对于每次交互(用户点击一个开关)，我们需要弄清楚用户正在与哪个手风琴交互。所以让我们试着把它抽象出来。理想情况下，我们甚至不想被同一个页面上存在其他实例的可能性所困扰。

首先，让我们将代码移到一个类中。如果您熟悉 PHP 或 Java 之类的语言，这已经是您的第二天性了(但矛盾的是，我看到特别是专注于这些语言的开发人员努力将相同的范例应用于 JavaScript)。

想法很简单:我们将为页面上的每个元素创建一个类实例。

```
class Accordion {

    constructor(_root) {
        this._root = _root;
        this._toggles = this._root.querySelectorAll('.m-accordion__header');
        this._panels = this._root.querySelectorAll('.m-accordion__panel');
        this._setupEventListeners();
    }

    _setupEventListeners() {
        this._toggles.forEach(toggle => {
            toggle.addEventListener('click', this.open.bind(this));
        });
    }

} 
```

```
// instantiation of all accordion elements
const accordionElements = document.querySelectorAll('.m-accordion');
accordionElements.forEach(accordionElement => {
    new Accordion(accordionElement);
}); 
```

那么，我们到底做了什么？

先把重点放在第二部分。我们使用`document.querySelectorAll`从当前页面获取每个 accordion 容器元素。所以这给了我们一个包含所有折叠容器的 DOMNodes 的`NodeList`。然后，我们迭代这些元素，实例化一个新的`Accordion`类实例，并将 accordion-container 作为参数传递。

然后，在类本身中，我们在构造函数中接收这个元素作为`_root`，并将其保存为类变量`this._root`。

仅此而已。从现在开始，我们再也不用在我们的类中引用`document`来获取属于手风琴的元素。每当我们需要抓取组件的一部分时，我们只需在`this._root`上启动`querySelector`。

这实质上意味着我们组件内部的所有查询现在都被限定了范围。此时，我们不必再考虑同一页面上的其他 accordion 实例。只要我们不超出这个范围，手风琴的两个实例就不会互相干扰。如果我们现在想获取前面提到的配置选项，我们只需从`this._root`中获取它们并完成它。

用这种模型思考会让编写 UI 代码变得容易很多，并且有助于保持对网站所有活动部分的概述。这些年来它确实帮了我很多。