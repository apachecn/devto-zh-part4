# 用 Vanilla JS 创建一个简单的确认模型

> 原文：<https://dev.to/boyum/creating-a-simple-confirm-modal-in-vanilla-js-2lcl>

以前试过使用`window.confirm()`吗？这是一个非凡的方法，无论何时你想让你的用户*真正*确定他们在做什么，它都非常方便。但是，你试过造型吗？就像用`window.alert()`不可能一样，所以我们需要创建自己的确认模型。我会告诉你怎么做！

## 解决什么

首先，列出我们试图解决的问题是有用的。重要的是，我们的模态可以做三件事:

*   询问用户他们应该回答的问题(例如，“您真的要删除您的用户帐户吗？”)
*   让用户说“是”
*   让用户说“不”

还有，对于开发者来说，`window.confirm()`是*所以*好用。我们不想让开发人员使用我们的自定义确认比使用`const theyAreSure = window.confirm('Are you sure');`更加困难。

原生模态附带的另一个东西是模态本身。我们不希望使用我们的组件的开发人员每次需要让他们的用户确认某些事情时都创建大量的标记，这意味着我们的自定义模块需要自动生成这些标记。

最终，它应该

*   易于使用
*   在用户说“是”之前不运行任何代码

## 如何解决

### Markup

出于本教程的考虑，指定一个令人费解的标记并不太重要，所以让我们只使用这个简单的代码作为我们的 HTML 基础:

```
<dialog class="confirm-dialog">
  <div class="confirm-dialog-question">Do you really want to delete your user account?</div>
  <div class="confirm-dialog-button-group">
    <button class="confirm-dialog-button confirm-dialog-button--false" type="button">Noo</button>
    <button class="confirm-dialog-button confirm-dialog-button--true" type="button">Yes!</button>
  </div>
</dialog> 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉`<dialog>`元素，去看看 [MDN 关于它的文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog)！作为一个简短的介绍，它是 Chrome、Firefox 和 Opera 支持的原生元素([也有一个 polyfill](https://github.com/GoogleChrome/dialog-polyfill))，你可以用`showModal()`方法显示一个模态，比如:

```
function createDialog() {
  const dialog = document.createElement('dialog');
  dialog.textContent = '✨✨✨';

  document.body.appendChild(dialog);

  dialog.showModal();
} 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript API

通过使用 [Promise API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 和 [`async` / `await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) ，我们可以解决我们之前列出的两个问题:我们可以使代码易于使用，我们可以等待一个信号，以确定何时(或是否)实际运行删除数据库中每个用户的代码。

最终，我们希望组件的使用看起来像这样:

```
async function deleteUsers() {
  const dialog = new ConfirmModal({ 
    questionText: 'Are you sure you want to delete every user?' 
  });

  const deleteEveryUser = await dialog.confirm();
  if (deleteEveryUser) {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得一个易于使用的组件，但这将工作吗？

JavaScript 的`await`停止代码执行，直到它等待的承诺被**解决**或**拒绝**。这个承诺可以通过一个事件触发的函数来解决，这就是我们构建代码的方式。当创建一个新的承诺时，我们将为这两个按钮添加一个事件监听器，根据哪个按钮被点击，将承诺解析为`true`或`false`——无论用户是否确认。

## 解决它

让我们从为组件创建一个`ConfirmDialog`类开始。它的构造器需要三样东西:

*   问题文本
*   “是”按钮的文本
*   “否”按钮的文本

```
class ConfirmDialog {
  constructor({
    questionText,
    trueButtonText,
    falseButtonText
  }) {
    this.questionText = questionText || 'Are you sure?';
    this.trueButtonText = trueButtonText || 'Yes';
    this.falseButtonText = falseButtonText || 'No';

    this.dialog = undefined;
    this.trueButton = undefined;
    this.falseButton = undefined;
    this.parent = document.body;

    this._createDialog();
    this._appendDialog();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个方法来创建`<dialog>`元素及其子元素，一个方法将它附加到`<body>`中，另一个方法将它从主体中移除，然后删除我们的`ConfirmDialog`对象。他们看起来是这样的:

```
 _createDialog() {
    this.dialog = document.createElement("dialog");
    this.dialog.classList.add("confirm-dialog");

    const question = document.createElement("div");
    question.textContent = this.questionText;
    question.classList.add("confirm-dialog-question");
    this.dialog.appendChild(question);

    const buttonGroup = document.createElement("div");
    buttonGroup.classList.add("confirm-dialog-button-group");
    this.dialog.appendChild(buttonGroup);

    this.falseButton = document.createElement("button");
    this.falseButton.classList.add(
      "confirm-dialog-button",
      "confirm-dialog-button--false"
    );
    this.falseButton.type = "button";
    this.falseButton.textContent = this.falseButtonText;
    buttonGroup.appendChild(this.falseButton);

    this.trueButton = document.createElement("button");
    this.trueButton.classList.add(
      "confirm-dialog-button",
      "confirm-dialog-button--true"
    );
    this.trueButton.type = "button";
    this.trueButton.textContent = this.trueButtonText;
    buttonGroup.appendChild(this.trueButton);
  }

  _appendDialog() {
    this.parent.appendChild(this.dialog);
  }

  _destroy() {
    this.parent.removeChild(this.dialog);
    delete this;
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在是最后一部分。让我们创建`confirm()`方法。在它里面，我们需要显示模态，并为两个是/否按钮创建事件监听器，让它们解析为`true`或`false`，然后删除组件本身的所有痕迹。

```
confirm() {
  return new Promise((resolve, reject) => {
    const somethingWentWrongUponCreation = 
      !this.dialog || !this.trueButton || !this.falseButton;
    if (somethingWentWrongUponCreation) {
      reject("Something went wrong upon modal creation");
    }

    this.dialog.showModal();

    this.trueButton.addEventListener("click", () => {
      resolve(true);
      this._destroy();
    });

    this.falseButton.addEventListener("click", () => {
      resolve(false);
      this._destroy();
    });
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！我在这里测试过:
[https://codepen.io/sindre/embed/RwbvObK?height=600&default-tab=result&embed-version=2](https://codepen.io/sindre/embed/RwbvObK?height=600&default-tab=result&embed-version=2)