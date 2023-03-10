# 在 React 中创建单选按钮组件

> 原文：<https://dev.to/mrscx/creating-a-radio-button-component-in-react-1l1a>

# 简介

样式化和使用单选按钮一直是棘手的事情。要定制一个乏味的默认 HTML 单选按钮，你必须使用大量的 CSS，并且拥有相当高级的 CSS 知识。操纵 CSS 伪选择器(::before/::after)之类的。

除此之外，在 React 中使用单选按钮可能更加棘手。在 React 中，处理更改、跟踪哪个值被选中、单选按钮被选中并不那么明显。我们将在 React 中创建自定义的 RadioButton 组件，在该组件中，我将尝试阐明单选按钮的所有方面。

# 设置

我们将使用 create-react-app 来建立一个空白的 react 项目。注意:本教程假设您之前已经使用 create-react-app 创建了 React 项目，并且安装了 NodeJS 和 npm。此外，如果您还没有安装 create-react-app，请使用 npm 安装它。在您的终端中运行以下命令

```
create-react-app radio_button_tut 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个名为 radio_button_tut 的文件夹，并用样板代码在其中初始化一个新的 React 应用程序。其中一些我们会移除。我们还将安装 node-sass，因为我们将在项目中使用 sass。SASS 现在可以在 React 应用程序中使用，无需任何额外的设置，也无需从 create-react-app 中退出。运行以下命令

```
npm install — save node-sass 
```

Enter fullscreen mode Exit fullscreen mode

完成后，用您喜欢的代码编辑器打开项目文件夹。让我们从删除由 create-react-app 创建的样板文件开始。将 App.css 和 index.css 文件重命名为 App.scss 和 index.scss。除了表示 SASS 文件的扩展名之外，这里实际上没有什么变化。删除 App.scss 中的所有内容，并替换为以下代码

```
*, *:before, *:after {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

body{
  font-family: "Robot", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

只是一些基本的 CSS 重置和字体设置。我们还重置了伪选择器，:before 和::after，否则单选按钮样式将不会正确生效。尤其是在单选按钮选择圆居中的情况下。在我们为单选按钮创建 CSS 之后，尝试删除这些代码。

现在，让我们在 src 文件夹中创建一个新组件。创建一个名为 RadioButton 的文件夹。在中再创建两个文件，RadioButton.js 和 RadioButton.scss。scss 文件并复制粘贴以下代码。

```
@import url(https://fonts.googleapis.com/css?family=Roboto);

$md-radio-checked-color: rgb(51, 122, 183);
$md-radio-border-color: rgba(0, 0, 0, 0.54);
$md-radio-size: 20px;
$md-radio-checked-size: 10px; 
$md-radio-ripple-size: 15px;

@keyframes ripple {

  0% {
    box-shadow: 0px 0px 0px 1px rgba(0, 0, 0, 0.0);
  }

  50% { 
    box-shadow: 0px 0px 0px $md-radio-ripple-size rgba(0, 0, 0, 0.1);
  }

  100% {
    box-shadow: 0px 0px 0px $md-radio-ripple-size rgba(0, 0, 0, 0);
  }
}

.RadioButton {
    margin: 16px 0;

    input[type="radio"] {
        display: none;

        &:checked + label:before {
            border-color: $md-radio-checked-color;
            animation: ripple 0.2s linear forwards;   
        }

        &:checked + label:after {
            transform: scale(1);
        }
    }

    label {
        display: inline-block;
        height:$md-radio-size;
        position: relative;
        padding: 0 ($md-radio-size + 10px);
        margin-bottom: 0;
        cursor: pointer;
        vertical-align: bottom;

        &:before, &:after {
            position: absolute;            
            content: '';  
            border-radius: 50%;
            transition: all .3s ease;
            transition-property: transform, border-color;
        }

        &:before {
            left: 0;
            top: 0;
            width: $md-radio-size;
            height: $md-radio-size;
            border: 2px solid $md-radio-border-color;
        }

        &:after {
            top: $md-radio-size / 2 - $md-radio-checked-size / 2;
            left: $md-radio-size / 2 - $md-radio-checked-size / 2;
            width:$md-radio-checked-size;
            height:$md-radio-checked-size;
            transform: scale(0);
            background:$md-radio-checked-color;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些花哨的 CSS 不是我写的。我从 CodePen 上的汉尼斯·卡梅克那里借来的。他的笔= >[https://codepen.io/hansmaad/pen/zBdzxp](https://codepen.io/hansmaad/pen/zBdzxp)的链接

如果你的 CSS 游戏不是很强大，那么 CSS 看起来会很复杂。它使用了一些很酷的 CSS 特性，比如动画的关键帧。关键帧用于选择单选按钮时的涟漪效果。::before 和，::after 用于设计单选按钮圆的样式。

现在打开 RadioButton.js 文件并粘贴以下代码

```
import React from "react";
import "./RadioButton.scss";

const RadioButton = (props) => {
    return (
        <div className="RadioButton">
            <input id={props.id} onChange={props.changed} value={props.value} type="radio" checked={props.isSelected} />
            <label htmlFor={props.id}>{props.label}</label>
        </div>
    );
}

export default RadioButton; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的功能反应组件。JSX 的结构非常简单，一个 div 包装着单选输入元素和标签元素。我们将 5 个道具传递给这个组件。标签、值、id、isSelected 和已更改的属性。

被改变的道具是一个函数。该函数将在每次单选按钮改变时触发。在普通 JS 中，这将是我们传递给 addEventListener 的回调函数。我们在这里监听的事件是 onChange。您可以看到我们正在分配道具。更改为 onChange 事件。

值属性是不言自明的，它设置输入元素的值。需要此值来评估选择的内容以及基于此选择我们应该采取的操作。当需要保存值时，它甚至被传递给数据库。

标签属性只是标签文本。这使得组件可以在我们的应用程序中重用。因为我们没有硬编码任何东西。

isSelected 是一个布尔值。它控制应该选择哪个单选按钮。在使用 RadioButton 组件的情况下，我们根据 radio 输入的值对此进行动态评估。

id 部分很有意思。没有它，这里的设置将无法工作。id 主要由 label 元素使用。它需要知道这个标签是针对哪个输入的。如果我们不使用它，无论你怎么按单选按钮，它都不会被选中，这意味着与之相关的事件 onChange 永远不会触发。请注意 htmlFor 属性。在普通的 HTML 中，这个属性只是为命名的。但是，至于是 JS 中的一个关键字，我们不能用那个。React 用 htmlFor 替换了它。在构建期间，它将它更改为 plain for。我们设置输入字段的 id，并用 htmlFor 告诉标签。如果单选按钮是用 for 循环生成的，则 id 可以是循环的索引。

这就是 RadioButton 组件。现在就用吧。将下面的代码转储到 App.js

```
import React, { Component } from "react";
import RadioButton from "./RadioButton/RadioButton";

class App extends Component {

    state = {
        paymentMethod: "COD"
    }

    radioChangeHandler = (event) => {

        this.setState({
            paymentMethod: event.target.value
        });
    }

    render() {

        const quickpay = this.state.paymentMethod === "QuickPay" ? <input type="text" placeholder="Enter transaction id"> : null;

        return (
            <div className="Apps">
                <div className="radio-btn-container" style={{ display: "flex" }}>

                    <RadioButton 
                        changed={ this.radioChangeHandler } 
                        id="1" 
                        isSelected={ this.state.paymentMethod === "QuickPay" } 
                        label="QuickPay" 
                        value="QuickPay" 
                    />

                    <RadioButton 
                        changed={ this.radioChangeHandler } 
                        id="2" 
                        isSelected={ this.state.paymentMethod === "COD" } 
                        label="Cash On Delivery" 
                        value="COD" 
                    />

                </div>

                { quickpay }

                <h2 style={{ marginTop: "50px" }}>
                    The selected radio button value is => { this.state.paymentMethod }
                </h2>
            </div>
        );
    }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

忽略此处的内联样式。它们只是为了展示的目的。一个是设置边距，另一个是使用 FlexBox 而不是 columns 在一行(row)中制作单选按钮。

App 组件是一个类组件。因此，我们可以在这里使用状态，并定义将作为道具传递的事件侦听器。状态很简单。它只有一个属性 paymentMethod。这是所选单选按钮的值。最初，货到付款。这里的设置是一个虚构的电子商务网站。在结账时，您将看到使用单选按钮的付款选项。为什么是单选按钮？因为您只能选择一种付款方式。您可以选择货到付款或另一种虚构的支付方式 QuickPay。如果您选择快速支付选项，将出现一个输入字段，您可以在其中输入交易 id。当选择另一个选项时，输入将消失。我们在这一行
的 render 方法中做这件事

```
const quickpay = this.state.paymentMethod === “QuickPay” ? <input type=”text” placeholder=”Enter transaction id”> : null; 
```

Enter fullscreen mode Exit fullscreen mode

它检查状态，如果值是 QuickPay，意味着选择了该选项，变量将保存输入元素，否则为 null。这确保了我们只有在选择了适当的选项时才能看到它。

我们在这里创建 2 个 RadioButton 组件，并传入适当的道具。请注意 isSelected 道具。此处的值基于状态持有的值计算为布尔值。

我们传递一个改变了的道具，这是一个功能。这是事件监听器。我们创建了 radioChangeHandler 方法，每次切换单选按钮时都会触发该方法。它使用 event.target.value 将状态设置为单选按钮的值。没什么特别的反应。一旦事件侦听器被激发，它将被传递给所有事件侦听器，其中包含与该事件相关的信息。当状态改变时，我们可以在 UI 中看到它的效果。

原来如此！希望有帮助。