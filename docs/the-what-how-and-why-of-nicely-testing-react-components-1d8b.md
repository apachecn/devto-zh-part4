# 完美测试 React 组件的内容、方式和原因

> 原文：<https://dev.to/auroratide/the-what-how-and-why-of-nicely-testing-react-components-1d8b>

所以我想钩子是 React 生态圈中的新时尚。我不得不承认，它们非常简洁和漂亮，事实上非常漂亮，以至于我决定尝试使用 hooks 和 React 的上下文 api 而不是 MobX 来重构我的整个网站。你知道，为了练习什么的。

现在，我可以谈论所有我必须改变的事情了...这是很多)，但首先我想谈谈我的事情*没有*改变。令我有些惊讶的是，当我在进行状态管理手术时，我几乎不需要改变测试。然而，当组件损坏时，测试会失败，而当组件工作时，测试会通过。

我差点哭了，因为这让重构变得简单多了。

调试测试没有浪费时间，我花了更多的精力做重要的事情，从 MobX 发展到 hooks。但是更好的是，这些测试给了我合理的信心，一旦它们都通过了，我就正确地完成了工作，甚至没有打开我的浏览器。

经过思考，我想用一个简单、简洁的语句(如果你愿意，可以用一个**钩子**)来分享我在重构实验中所学到的东西:

> 测试你的组件做什么，而不是它如何工作。

让我们来看看这意味着什么，如何让它工作，以及为什么我们应该尽可能地遵循它！哦，是的，如果你有足够的耐心，我有一个必不可少的 Github repo 样本来分享，它也展示了这个原则。耶！代码！

注意:这绝不是一个新的、革命性的原则。无论是 React 还是其他地方的开发人员，多年来一直在重复这句话。参见肯特·c·多兹的文章[测试实现细节](https://kentcdodds.com/blog/testing-implementation-details)中的例子。然而，很难知道我们是否已经偏离了测试实现的轨道，这就是为什么我想写一些关于测试 React 组件的内容、方法和原因。

## 什么原理？

> 测试你的组件做什么，而不是它如何工作。

好吧，这些都是些花哨的词，但是它们真正的含义是什么呢？

为了帮助直观地理解这个原则的含义，让我们离开编程领域，用一些常见的名词来代替“组件”这个词...

*   测试你的**键盘**做什么，而不是它如何工作。
*   测试你的**咖啡机**能做什么，而不是它如何工作。
*   测试你的**水龙头**做什么，而不是它如何工作。

<figure>

[![Faucet](img/e829cc76c556d84f6caa888c018471a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_JoozYGI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://auroratide.com/assets/posts/nicely-testing-react-components/faucet.png)

<figcaption>A faucet, photo by Sasikan Ulevik on Unsplash</figcaption>

</figure>

想一想，你将如何着手测试你的水龙头是否工作，是否能达到预期的效果。当你转动旋钮时，水应该会从管子里流出来。因此，如果是我，我会转动旋钮，然后看看是否有水出来(而不是其他一些恶心的液体)。

换句话说，你...

*   ...查看对象是否做了它应该做的事情...
*   ...只使用它提供的接口。

注意你*没有*做的事情。你不会把水龙头拆开来看看旋钮是否钩在正确的机械装置上，或者挖进管子里看看阀门在不在。你只需要把它拆开来*调试*水龙头，以防*不按预期工作。嗯，除非你是一个机械工程师，你把所有东西都拆开来证明我是错的。*

好了，类比够了，回到编程。

事实证明，用完全相同的方式来测试组件是非常有用的。

*   查看组件是否做了它应该做的事情...
*   ...仅使用提供的接口。

如果你的组件是一个增加数字的按钮，那么提供的界面就是这个按钮，它应该做的事情就是让你看到的数字增加一。所以你测试它，如果内部状态改变或者按钮拥有某种回调。

这就是*“测试你的组件做什么，而不是它如何工作”*真正的意思。

思考这个原则的最后一种方法是，想想你将如何*衡量*你是否达到了这个原则。对我来说，试金石是“我的测试应该通过，即使我替换了我管理状态的方式，没有编辑过测试”。如果我能做到，那么我知道我的测试是可靠的。

## 我该怎么做？

好了，代码时间。

~~假设你想要一个超级简单的组件，增加一个数字~~开玩笑，这个例子用得太多了。假设你想要一个完全不同的组件，当按下按钮时*会减少*一个数字。因为为什么不呢。

```
const Decrementor = () => {}; 
```

Enter fullscreen mode Exit fullscreen mode

哈哈，骗你的！我们从一个框架开始，而不是一个实现的组件。这是因为我们将使用*测试驱动开发*来构建这个东西。哦耶！

记住我们的咒语。"测试你的组件做什么，而不是它如何工作."为此，我们需要知道两件事:

1.  用户可以与之交互的界面是什么？
2.  当用户与它互动时，他们会看到什么？

注意这个神奇的词:**用户**。为了编写我们的测试，我们从*用户*的角度考虑问题。从某种意义上说，我们在想*的例子用法*。输入是用户交互，输出是用户看到的东西。

来回答我们的问题...

1.  用户可以按下按钮。
2.  当他们这样做时，屏幕上的数字会减少 2。

这是一个测试！用代码来写，让我们使用 [React 测试库](https://github.com/testing-library/react-testing-library)，尽管你也可以用酶来实现同样的测试:

```
import React from 'react';
import { render, fireEvent, cleanup } from '@testing-library/react';
import Decrementor from './Decrementor';

describe('Decrementor', () => {
  let wrapper;
  const clickDecrement = () => fireEvent.click(wrapper.getByText(/decrement/i));
  const getNumber = () => wrapper.getByText(/-?\d+/).textContent;

  it('should decrement the number by 2 when the button is pressed', () => {
    wrapper = render(<Decrementor initialValue={10} />);
    expect(getNumber()).toEqual('10');

    clickDecrement();
    expect(getNumber()).toEqual('8');
  });

  afterEach(cleanup);
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们停下来注意一些事情:

*   该测试看起来像人类可读的指令。首先呈现组件，然后单击 decrement，然后查看数字是否是预期值。
*   测试不知道*关于减量按钮是如何实现的或者它是如何存储状态的。组件会使用钩子吗？MobX？减速器？它不在乎！*
*   我们使用用户看到的文本值来选择元素。用户看到文字文本“减量”，所以这就是我们点击的东西。或者，我们可以使用 CSS 类选择器，但是这些东西对最终用户是不可见的。

实际上，这就是 React 测试库的好处。不像 Enzyme，React 测试库的 API *阻止*你深入组件的实现细节。它以用户为中心。

现在我们有了一个测试，我们可以像这样写一个漂亮的小组件:

```
import React, { useState } from 'react';

const Decrementor = ({ initialValue }) => {
  const [ value, setValue ] = useState(initialValue);
  const decrement = () => setValue(prev => prev - 2);

  return <>
    <p>{value}</p>
    <button onClick={decrement}>Decrement</button>
  </>; };

export default Decrementor; 
```

Enter fullscreen mode Exit fullscreen mode

我们决定用钩子，因为，你知道，它们是新事物，但我们没有必要。很好，下面使用 MobX 类的实现也通过了，尽管*完全不同* :

```
import React, { useState } from 'react';
import { decorate, observable, action } from 'mobx';
import { observer } from 'mobx-react-lite';

const Number = decorate(class {
  constructor(initialValue) {
    this.value = initialValue;
  }

  decrementBy(n) {
    this.value -= n;
  }
}, {
  value: observable,
  decrementBy: action
});

const Decrementor = observer(({ initialValue }) => {
  const [ number ] = useState(() => new Number(initialValue));

  return <>
    <p>{number.value}</p>
    <button onClick={() => number.decrementBy(2)}>Decrement</button>
  </>; });

export default Decrementor; 
```

Enter fullscreen mode Exit fullscreen mode

即使状态管理范例完全不同，测试还是通过了。这是因为测试只关注组件的表面。最终，组件做什么才是用户真正关心的，而不是它如何工作。

很漂亮，对吧？

我们可以这样总结要点:

*   采用**真实用户**使用您的组件来驱动测试如何读取的范例。
*   在测试的动作和断言中只暴露组件的**表面**。有意识地问问你自己，用户可以操作什么旋钮，用户应该清楚地看到什么。
*   **不要深究**组件的状态并直接操纵值。否则，从用户的角度来看，不能保证你所做的是可以实现的。

还有一个额外的提示:

*   如果测试很难写，**暂停**并再次检查你的方法！

不可否认，大规模应用中的实元件可能比我们简单的减量器更疯狂。最后，如何确定用户对组件的界面取决于您自己。

## 这有什么关系？

测试是非常好的，因为它们给我们提供了代码工作的信心。然而，只有当我们真正相信我们的测试时，这才是真的。如果他们在不该通过的时候通过或失败，那么我们可能会不再相信我们的测试，我们会失去我们最初想要的信心。

同样，我们自动化测试，因此我们不必手动启动应用程序，也不必用肉眼验证。这里的主要好处是时间。我们越少需要打开应用程序来验证一些小行为，就越好。单个单元测试可以在几毫秒内运行，但是手动检查最多需要一分钟，甚至几百倍。

换句话说，我们编写自动化测试是因为...

*   ...他们为我们的代码提供了信心
*   ...为我们节省大量时间。

不幸的是，我们中的许多人(包括我自己)可能都经历过我们的测试不能满足其中一个或两个价值主张的时候。如果测试在不应该失败的时候失败了，那么我们就不再有信心，如果我们不得不照看这些失败的测试，那么我们最终会浪费我们希望节省的时间。

UI 组件特别不稳定，所以如果可以的话，避免任何陷阱是加倍重要的。这意味着准确地测试我们关心的东西，因为任何无关的东西都意味着要维护更多的代码。当你真正思考这个问题时，作为产品开发人员，我们关心的首要事情是用户能做产品设计的事情。

测试组件做什么，而不是它如何工作。通过这样做，你的测试将更加可靠，不那么脆弱，并且更接近现实，从而提供信心和节省时间。

## 举个例子:对 Tac Toe 做出反应

作为一个实验，我决定尝试用三种不同的状态管理范例来实现经典的井字游戏:基于钩子的、基于对象的和基于 reducer 的。

有什么问题吗？每个版本的井字游戏组件都必须通过完全相同的一组测试。

**[点击这里进入 Github 回购](https://github.com/Auroratide/react-tac-toe)** ！自述文件中有更多详细信息。

顺便说一下，我也试着用 web 组件来做这件事，只是为了证明即使没有 React 测试也能通过。不幸的是，当我意识到 JSDOM 还不支持定制元素时，我走进了死胡同。如果你喜欢冒险，请随时提出一个拉动请求，证明这确实可以做到！

## 关闭思绪

最后，这不是一个新概念，它适用于 frontend React 之外的所有编码方面。不过，有时候，当某样东西击中你的脸时，你真的会欣赏它，我的站点范围的重构之旅就是这样一个例子。

*   测试组件*做什么*，而不是它*如何工作*
*   从*用户*的角度编写测试
*   测试中只暴露组件的*表面*

并且(希望)你的测试会非常可靠而不脆弱。请在下面的评论中告诉我你的想法！