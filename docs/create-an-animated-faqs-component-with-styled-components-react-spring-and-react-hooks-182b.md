# 用样式组件、反应弹簧和反应挂钩创建一个动画 FAQ 组件

> 原文：<https://dev.to/stoutlabs/create-an-animated-faqs-component-with-styled-components-react-spring-and-react-hooks-182b>

在这篇文章中，我们将构建我最近为一个客户站点创建的东西:一个动画 FAQ(常见问题)组件。它非常简单，可以编写一个教程，同时还展示了一些非常强大的工具，可以在您的项目中使用。这将是一个很长的帖子，所以让我们开始吧！

这里有一个我们将要制作的快速演示:
[常见问题演示](https://stoutlabs-faqs-demo.netlify.com/)

注意:要跟随本教程，您需要一个基于 React 的站点，并准备好进行编辑。基于这篇文章的主题，我假设你不需要帮助就能达到那个目的。😂(我只是用了一个 [Gatsby.js](https://www.gatsby.org/) 默认启动器进行演示。)

## 安装库

为了创建这个 FAQ 组件，我们将利用两个出色的 React 库，`react-spring`和`styled-components`:

*   react-spring 是一个强大且易于实现的动画库，为 react 内部使用而构建。在这篇文章中，我们只是勉强使用它，但是它能够制作非常高级的动画序列。请务必[查看他们的文档](https://www.react-spring.io/docs/)和[示例](https://www.react-spring.io/docs/hooks/examples)。

*   [styled-components](https://www.styled-components.com/) 是一个令人惊叹的 CSS-in-JS 库，我几乎在我参与的每个 React 项目中都使用它。还有其他类似的解决方案，我已经不止一次尝试过其中的大部分...但是`styled-components`仍然是我的最爱。

现在让我们安装这两个软件:

```
$ yarn add react-spring styled-components 
```

之后，您可能需要配置`styled-components`来使用您的 React 站点。例如，在一个 Gatsby 站点中，我们需要安装一个额外的 Gatsby 插件，并修改`gatsby-config.js`文件。如果你感兴趣的话，我在 Alligator.io 上写了一篇关于《T2》使用盖茨比风格组件的完整文章。

我们还将利用 React 的新特性`Hooks`，所以请确保您使用的是 React 版本`16.8.0`或更高版本。(在写这篇文章的时候，React 的版本是`16.9.0`。)

## 创建基本组件

让我们首先在`/src/components/Faqs/`的项目中建立一个新目录。在这个目录中，让我们创建两个新文件:

### Faq.js

这个文件是一个 React 组件，作为一个单独的 FAQ 问题/答案对。

#### /src/components/FAQ/FAQ . js

```
import React, { useState } from "react";

const Faq = props => {
  const { question, answer } = props;
  const [isOpen, toggleOpen] = useState(false);

  return (
    <div onClick={() => toggleOpen(!isOpen)}>
      <div className="faq-question">
        <span>
          Q: {question}
        </span>
      </div>

      <div 
        className="faq-answer" 
        style={isOpen ? { display: "block"} : { display: "none" }}
      >
        <span>
          A: {answer}
        </span>
      </div>
    </div>
  );
};

export default Faq; 
```

如您所见，我们利用 React 中的`useState`钩子来跟踪这个组件的打开/关闭状态。它并没有真正做太多，但很快我们将使用`react-spring`动画显示&隐藏答案！

### FaqsList.js

这个文件只是一个简单的 React 组件，它将作为一个容器来保存我们的 FAQ 列表:

#### /src/components/FAQ/faqslist . js

```
import React from "react";

import Faq from "./Faq";

// this data could come from anywhere
const faqsData = [
  { 
    question: "What does FAQ stand for?",
    answer: "Frequently Asked Question"
  },
  {
    question: "What is the best ice cream flavor?",
    answer: "Coffee with fudge ripple, or homemade strawberry."
  }
];

const FaqsList = () => {
  return (
    <div>
      {faqsData.map((faq, i) => (
        <Faq key={"faq_" + i} question={faq.question} answer={faq.answer} />
      ))}
    </div>
  );
};

export default FaqsList; 
```

注意上面 FAQ 对象的`faqsData`数组。这些数据可能来自任何地方(您的 CMS、API 等)，但是出于演示的目的，我只是硬编码了一些数据。

好了，现在我们已经设置好了基本组件...让我们添加有趣的东西:风格和动画！

## 用样式化组件进行样式化

让我们为我们的`FaqsList`和`Faq`组件创建一些基本样式。在组件所在的目录下创建一个新的`faq-styles.js`文件，并插入以下代码:

#### /src/components/FAQ/FAQ-styles . js

```
import styled from "styled-components";

export const StyledFaq = styled.div`
  cursor: pointer;
  margin: 0 0 10px;

  div.faq-question {
    font-size: 125%;
    font-weight: 800;
    margin: 0 0 5px;
  }

  div.faq-answer {
    background: #fff;
    overflow: hidden;

    span {
      display: block; 
      padding: 20px 10px;
    }
  }
`;

export const StyledFaqsList = styled.div`
  background: #efefef;
  margin: 20px 0;
  padding: 1rem;
`; 
```

注意我们是如何导出这些的吗？这将允许我们从上面创建的组件文件中导入它们。这种方法将把你所有的 FAQ 样式都保存在一个位置，便于以后定制。

注意:这是我在制作“基于文件夹”的组件时的典型模式，我计划在其他位置重用这些组件。*很多人似乎认为在使用`CSS-in-JS`解决方案时，样式必须在每个组件文件中...但这是不正确的！*

### 调整组件

让我们调整我们的`Faq.js`和`FaqsList.js`组件来利用这些新样式:

#### /src/components/FAQ/FAQ . js

```
import React, { useState } from "react";

import { StyledFaq } from "./faqStyles";

const Faq = props => {
  const { question, answer } = props;
  const [isOpen, toggleOpen] = useState(false);

  return (
    <StyledFaq onClick={() => toggleOpen(!isOpen)}> 
      <div className="faq-question">
        <span>Q: {question}</span>
      </div>

      <div
        className="faq-answer"
        style={isOpen ? { display: "block" } : { display: "none" }}
      >
        <span>A: {answer}</span>
      </div>
    </StyledFaq> );
};

export default Faq; 
```

上面我们所做的只是为`StyledFaq`添加了一个导入语句，然后用我们导入的样式化组件替换掉外部的`div`元素。有道理吗？

接下来，我们将对`FaqsList`组件做同样的事情:

#### /src/components/FAQ/faqslist . js

```
import React from "react";

import Faq from "./Faq";
import { StyledFaqsList } from "./faqStyles";

const faqsData = [
  {
    question: "What does FAQ stand for?",
    answer: "Frequently Asked Question!",
  },
  {
    question: "What's the best ice cream flavor?",
    answer: "Coffee with fudge ripple, or homemade strawberry.",
  },
];

const FaqsList = () => {
  return (
    <StyledFaqsList> {faqsData.map((faq, i) => (
        <Faq key={"faq_" + i} question={faq.question} answer={faq.answer} />
      ))}
    </StyledFaqsList> );
};

export default FaqsList; 
```

您现在应该有一个基本样式的 FAQ 列表显示，每个 FAQ 项目在单击时显示/隐藏答案。如果你的没有做到这一点，我会在 end⁠上发布完整源代码的链接——所以不要惊慌！😎

## 用 react-spring 添加动画

我们用`react-spring`给这个加点动画吧！为了保持这篇文章的简单，我们将会在点击时显示/隐藏每个 FAQ 的答案部分。

(是的，我的 CSS 战士朋友们...我们可以用纯 CSS ⁠—做类似的事情，但我想在这篇文章中展示 react-spring 的用法！)

但是首先，我们需要添加一个微小的 npm 包来帮助我们测量答案的高度。我们需要这些信息来告诉`react-spring`答案处于“开放”状态时的高度。有几个可用的选项，但我将使用 [react-resize-aware](https://www.npmjs.com/package/react-resize-aware) -因为它有一个易于使用的基于钩子的解决方案。

像往常一样，将它添加到您的项目中:

```
$ yarn add react-resize-aware 
```

现在我们只需要编辑`Faq.js`组件来添加动画。以下是更新后的代码:

#### /src/components/FAQ/FAQ . js

```
import React, { useState } from "react";
import { useSpring, animated } from "react-spring";
import useResizeAware from "react-resize-aware";

import { StyledFaq } from "./faqStyles";

const Faq = props => {
  const { question, answer } = props;
  const [isOpen, toggleOpen] = useState(false);
  const [resizeListener, { height }] = useResizeAware(); 
  const animProps = useSpring({ 
    height: isOpen ? height : 0, 
    opacity: isOpen ? 1 : 0, 
  });

  return (
    <StyledFaq onClick={() => toggleOpen(!isOpen)}>
      <div className="faq-question">
        <span>Q: {question}</span>
      </div>

      <animated.div className="faq-answer" style={{ ...animProps }}> 
        <span style={{ position: "relative" }}> 
          {resizeListener} A: {answer}
        </span>
      </animated.div> </StyledFaq>
  );
};

export default Faq; 
```

为了解释得更详细一点，我们在上面做了以下事情:

*   导入了我们已经安装的两个包，`react-spring`和`react-resize-aware`。我们解构了`react-spring`的`useSpring`和`animated`，这样它们更容易使用。
*   使用来自`react-spring`的`useSpring`钩子为我们的动画配置设置创建了一个新变量。注意，我们为不透明度和高度设置了初始值`0`，然后当显示答案时，我们测量的`height`值用于设置高度。(当然，不透明度设置为 1。)
*   将`faq-answer` div 转换成 react-spring `animated.div`元素，并将`animProps`的值分散到`styles`属性中。
*   给答案的内部`span`标签添加了一个`position: relative`样式。这是`react-resize-aware`所要求的，以正确测量负载元件。(见下一项。)
*   在我们答案的内跨度里加了一个`resizeListener`。这是`react-resize-aware`的一部分，它测量答案加载时的高度。(它本质上是一个不可见的 div，通过自定义的 React 挂钩返回其宽度和高度...所以它在我们的无状态组件中完美地工作！)

如果您还没有尝试过，请尝试一下。每个 FAQ 项目现在应该在点击时动画打开，如果再次点击应该动画返回关闭。很酷吧。现在，您可以在您的任何站点中重用该组件，并且您只需要编辑样式/动画来满足您的需求。

## 最后的想法

我们完了！我希望这对你们中的一些人有所帮助，甚至可能给你一些自己尝试的想法！

### 预览/下载来源:

这里演示:[https://stoutlabs-faqs-demo.netlify.com/](https://stoutlabs-faqs-demo.netlify.com/)

Github 上的演示源:[https://github.com/stoutlabs/demo-spring-hooks](https://github.com/stoutlabs/demo-spring-hooks)。

我打算开始写更多...所以我会在下一个帖子中找到你！💜