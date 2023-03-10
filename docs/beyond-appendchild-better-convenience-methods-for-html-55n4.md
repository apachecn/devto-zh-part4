# 超越 appendChild:HTML 的更好的便利方法

> 原文：<https://dev.to/chromiumdev/beyond-appendchild-better-convenience-methods-for-html-55n4>

我已经在网上建立了一段时间了。以至于当我编写普通的 HTML/JS 时，我总是使用`.appendChild()`来添加新的 HTML 元素，以及随之而来的一系列`createElement`调用。

但是实际上有一些我们现在可以使用的鲜为人知的便利方法(嗯，在后 IE11 时代，所有的开发人员都应该在那里)。🌎👍我不是反对你的框架或组件，但是有时候，你必须写普通的 JS。🍦

## 单行元素创建

我承认😅这并不是真正的一行，而是一个*语句* :

```
const el = Object.assign(document.createElement('div'), {
  textContent: `Your div now has text`,
  className: 'and-is-classy',
}); 
```

Enter fullscreen mode Exit fullscreen mode

IE11 中没有助手`Object.assign`。

## 清除自我

这个非常有名。

```
const el = document.getElementById('something');
el.remove();   // instead of el.parentNode.removeChild(el) 
```

Enter fullscreen mode Exit fullscreen mode

## 插入元素*或*文本

`.append()`方法可以附加任何真实的元素，或者如果你给它传递一个字符串，它会自动创建一个文本节点。它需要任意数量的参数。

```
el.append(document.createElement('hr'), 'I get upgraded to a text node!'); 
```

Enter fullscreen mode Exit fullscreen mode

还有与`.append()`相反的`.prepend()`。它将所有元素按顺序插入到元素的开头:

```
const heading = Object.assign(document.createElement('h2', {
  textContent: 'List Of Awesome HTML Methods',
});
list.prepend(heading, `You Won't Believe How Many We Found!`); 
```

Enter fullscreen mode Exit fullscreen mode

## 相对于元素插入

每个元素都有方法`.before()`和`.after()`。这些操作会在当前节点旁边直接插入新的 HTML 节点。和上面的方法一样，它们接受任意数量的其他元素或字符串。

```
myHeading.before(superHeading);
myHeading.after(`Here's a list of awesome stuff`, theList); 
```

Enter fullscreen mode Exit fullscreen mode

⚠️:有一个警告:在我们的例子中，如果`myHeading`实际上不在页面上——它是一个临时元素——这些方法将会无声地失败而不会抛出`Error`。

## 替换自我

我们现在可以自毁一个元素，用新的东西代替它，而不是跳一支`parentNode.replaceChild`舞。同样，我们可以用任意数量的其他元素或字符串替换我们自己(甚至没有！).

```
const fancyItem = Object.assign(document.createElement('strong'), {
  textContent: 'fancy',
});
someFancyHeading.replaceWith('Less', fancyItem, 'heading');
someFancyHeading.replaceWith();  // although you could just use .remove 🤷 
```

Enter fullscreen mode Exit fullscreen mode

## 班力设定

如果您想将类的状态设置为变量 true 或 false，您可以将第二个参数传递给`.classList.toggle` :

```
const someState = false;
theDiv.classList.toggle('foo', !someState);  // forces foo on
theDev.classList.toggle('bar', someState);   // forces bar off
// result e.g. <div id="theDiv" class="foo"> 
```

Enter fullscreen mode Exit fullscreen mode

这一点*大概*众所周知。但是如果你明确地不支持 IE11，确信它现在工作是很好的。🎉

# 搞定！

我错过了什么？让我知道你最近是否发现了其他可以放弃的旧习惯。

nine