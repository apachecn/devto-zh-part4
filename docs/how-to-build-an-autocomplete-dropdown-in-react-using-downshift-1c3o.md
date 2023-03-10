# 使用降档+ TS 在 React 中创建下拉列表

> 原文：<https://dev.to/aromanarguello/how-to-build-an-autocomplete-dropdown-in-react-using-downshift-1c3o>

从我接触降档的那一刻起，我就知道这个库有着惊人的潜力。如今，我们中的许多人都不考虑可访问性的重要性。降档惊人地在许多方面解决了这个问题。我曾经看过这篇文章:[https://baymard.com/blog/custom-dropdowns-cause-issues](https://baymard.com/blog/custom-dropdowns-cause-issues)，它说 31%的自定义下拉菜单都有问题，我知道为什么了。

我不得不建立了一些下拉只有 JSX，他们很难有这么多的边缘情况要涵盖。诸如键盘导航和 ARIA 标准的可访问性。而且更不用说我介绍过的 bug lol！自己构建它们会变得很复杂。

这是你有两个选择的场景之一，从头开始构建或者利用像降档这样的库，这种权衡是不公平的。如果你需要建立一个下拉列表，这个库可以处理所有的事情。它配备了完整的开箱即用键盘导航，所有正确的咏叹调道具，并管理其内部状态。但是，你也可以覆盖它的许多部分。

我在一本故事书上添加了一个下拉菜单，它有一个`a11y`插件(如果你不知道它是什么，它会告诉你关于你的组件的 11y 统计数据)，它成功地通过了 15 个不同的 aria 标准，没有违规。

在构建许多组件时，使用降档无疑会节省您和您的队友的时间。我曾经花了一周的时间来实现自动完成下拉菜单。现在我 45 分钟就能做一个:)！这个图书馆太棒了，我希望你试一试并喜欢它！

任何反馈都是非常感谢:D！

降档 Github:[https://github.com/downshift-js/downshift](https://github.com/downshift-js/downshift)

我们首先需要创建下拉组件。使用 typescript，当我们导入 react 时，我们需要从 React 库(`import * as React from 'react'`)导入所有内容。我知道有办法解决这个问题，但这样做没有坏处。

降档是从库中导出的默认组件，它使用 Render props 方法为我们提供了许多功能，如内部状态管理、令人难以置信的 a11y 方法、自动完成方法、键盘导航，以及我们将探索的其他一些非常酷的东西:)！我不会真的把重点放在样式上，而是放在下拉菜单的功能上。

注意:如果你试图在这一步运行它，降档将抛出一个错误。这是因为降档需要渲染一个 DOM 元素。

首先，在 react 项目中:

`npm install --save downshift`

然后创建一个名为`Dropdown.tsx`的文件

```
import * as React from 'react'
import Downshift from 'downshift'

const Dropdown: React.FC = () => { 
   // we declare our dropdown as Functional
   // Component type. Still no props to declare
  return (
    <Downshift></Downshift>
  )
}

export default Dropdown 
```

Enter fullscreen mode Exit fullscreen mode

我总是喜欢在后面添加样式，我喜欢这个`styled-components`库，它无疑是我最喜欢的 css-in-js 库。随着 V5 野兽模式的发布，这将是一个疯狂的速度！

我已经开发了调用下拉列表的自动完成部分的约定。这是个人喜好。

```
import * as React from 'react'
import Downshift from 'downshift'
import styled from 'styled-components'

const Input = styled.input`
  width: 100px;
  border: 1px solid black;
`;

const Dropdown: React.FC = () => {
  return (
    <Downshift></Downshift>
  )
}

export default Dropdown 
```

Enter fullscreen mode Exit fullscreen mode

现在，关于渲染道具的方法。我们将首先从降档中析构一些方法，其中一些方法返回一个对象，该对象将所有这些漂亮的 aria 道具插入到我们的组件中以使其可访问。它们还可能包含事件处理程序，这些程序要么给你控制权，要么在幕后操作。降档更喜欢我们提供这些物品作为道具

*   `{...getInputProps()}`将为输入标签提供一个对象，该对象包含其完全可访问所需的所有道具，如`aria-autocomplete`、`aria-controls`、`aria-label`等。

*   注意，如果我已经包装了我的组件(在`<Downshift>`之间的所有内容)

*   因为我们将下拉菜单包装在一个`<Form>`组合样式的组件中，所以我们需要在`<Form>`
    中析构和传播`{...getRootProps()}`

```
const Form = styled.form`
  display: flex;
  flex-direction: column;
  width: 100px;
  margin: 0;
`;

const Input = styled.input`
  width: 100%;
  border: 1px solid black;
`;

const Dropdown: React.FC = () => {
  return (
    <Downshift>
      {({ getInputProps, getRootProps }) => (
        <Form {...getRootProps()}>
          <Input {...getInputProps()} />
        </Form>
      )}
    </Downshift>
  );
};

export default Dropdown; 
```

Enter fullscreen mode Exit fullscreen mode

在输入之后，我创建了下拉菜单的实际下拉选项部分。我称之为`<ul>` = `<Menu>`和`<li>` = `<Item>`

我们稍后将映射`<Item>`来生成一个充满了酷项目的漂亮菜单。

同样，我们将从`Downshift`中析构`getMenuProps`，然后将其作为道具散布在`Menu`内部。这个方法将处理添加所有正确的 aria 角色和道具。

默认情况下，菜单会添加一个引用`<label>`的`aria-labelledby`。但是，您可以提供 aria-label 来给出描述可用选项的更具体的标签。

```
const Form = styled.form`
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  width: 100px;
  margin: 0;
  padding: 0;
`;

const Input = styled.input`
  width: 80px;
  margin-left: 37px;
  border: 1px solid black;
`;

const Menu = styled.ul`
  width: 80px;
  padding: 0;
  margin: 0;
  border: 1px solid;
`;
const Item = styled.li`
  list-style: none;
`;

const Dropdown: React.FC = () => {
  return (
    <Downshift>
      {({ getInputProps, getMenuProps, getRootProps }) => (
        <Form {...getRootProps()}>
          <Input {...getInputProps()} />
          <Menu {...getMenuProps()}>
            <Item>First Item</Item>
          </Menu>
        </Form>
      )}
    </Downshift>
  );
};

export default Dropdown; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要赋予`<Item>`所有令人敬畏的特性。我们可以通过降档为我们提供的另一种方法来实现这个
:`getInputProps`，然而，这一次我们处理的是一个不纯的函数(只有在你对一个物品应用 props 时才调用它)。与前面的方法相同，我们将传播到`<Item>`。但是，这一次该方法接受一个对象作为参数。它有不同的属性，其中只有一个属性是必需的:`item`，它是当用户在一个项目上选择点击或选择(用键盘)时将被选择的值。另一个不需要的属性是 index。我们为`getItemProps()`提供了一个索引，因为这是降档跟踪哪个项目被选中的方式，也是我们稍后将探讨的内容`highlightedIndex`。

实现下拉选项最有效的方法是迭代数组。我通常选择一个对象数组，因为一个对象可以容纳更多的键/值对。

此时，我们可以开始为组件设置第一个道具。我通常创建我的接口，并将其分配给`React.FC`，这样我们就知道道具可以期望什么样的形状。对于下拉，我喜欢把数组叫做:`options`。

```
import * as React from "react";
import Downshift from "downshift";
import styled from "styled-components";

type OptionType = {
  value?: string;
};

// it is considered best practice to append the 'I' Prefix to interfaces
interface IDropdownProps { 
  options: OptionType[];
  onChange?: (selectedItem: string) => void; // this is for later
}

const Form = styled.div`
  display: flex;
  flex-direction: column;
  width: 100px;
`;

const Input = styled.input`
  width: 100%;
  border: 1px solid black;
`;

const Menu = styled.ul`
  width: 100%;
  padding: 0;
`;
const Item = styled.li`
  list-style: none;
  width: 100%;
`;

const Dropdown: React.FC<IDropdownProps> = ({ options }) => { // add IDropdownProps
  return (
    <Downshift>
      {({ getInputProps, getMenuProps, getRootProps, getItemProps }) => (
        <Form {...getRootProps()}>
          <Input {...getInputProps()} />
          <Menu {...getMenuProps()}>
            {options.map((item, index) => ( // we map over the options array
              <Item {...getItemProps({
                item,
                index,
                key: item.value
              })}>{item.value}</Item>
            ))}
          </Menu>
        </Form>
      )}
    </Downshift>
  );
};

export default Dropdown; 
```

Enter fullscreen mode Exit fullscreen mode

关于内部状态管理，我们之前已经提到过...降档管理自己的内部状态，用于在打开和关闭之间切换下拉菜单。我们将从降档中析构`isOpen`，默认为`false`，其状态完全在内部处理。现在，我们可以点击输入，输入一些东西，这也将切换`isOpen`为真，并打开菜单。默认情况下，当您在降档范围之外单击时，降档具有关闭菜单的功能。

为了有一个直观的队列，我们将从降档中析构`highlightedIndex`。我们将在`{...getItemProps()}`方法`style`中添加一个新的键作为参数。该样式将允许我们将 CSS 属性注入到当前项目中。所以，我们基本上告诉它，嘿，如果你所在的项目的当前索引等于`highlightedIndex`(通过降档处理)，使选择浅灰色。

就像这样，现在我们可以点击输入，键入一个字母，下拉菜单应该会打开，你可以有键盘导航。WOOT·WOOT！是的，降档的另一个好处是，开箱即用的键盘导航！多甜蜜啊。！

```
const Dropdown: React.FC<IDropdownProps> = ({ options }) => {
  return (
    <Downshift>
      {({
        getInputProps,
        getMenuProps,
        getRootProps,
        getItemProps,
        isOpen,
        highlightedIndex
      }) => (
        <Form {...getRootProps()}>
          <Input {...getInputProps()} />
          <Menu {...getMenuProps()}>
            {isOpen &&
              options.map((item, index) => (
                <Item
                  {...getItemProps({
                    style: {
                      backgroundColor:
                        index === highlightedIndex ? "lightgray" : null
                    },
                    key: item.value,
                    item,
                    index
                  })}
                >
                  {item.value}
                </Item>
              ))}
          </Menu>
        </Form>
      )}
    </Downshift>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们添加一个按钮怎么样，这样我们可以通过鼠标点击来打开和关闭它？为了简单起见，我将添加一个简单的按钮，并为它提供库给我们的事件监听器方法。我将只添加一个按钮，因为这里带走的是降档如何处理这些类型的事件，所有这些都可以用类似`stateReducer`的东西覆盖

我们将从降档中析构`getToggleButtonProps`方法，并将它`{...getToggleButtonProps()}`作为道具传播到`<Button>`中。这个方法将为我们做几件事。它将赋予按钮所有适当的角色和 aria 道具，但它的主要功能是切换我们之前构造的`isOpen`的内部状态，所以本质上，它将允许我们通过点击来打开和关闭菜单！

```
// Button
const Button = styled.button`
  width: 20px;
  text-align: center;
  padding: 0;
`;
// Added this to align input and button :)
const InputContainer = styled.div`
  display: flex;
`;

const Dropdown: React.FC<IDropdownProps> = ({ options }) => {
  return (
    <Downshift>
      {({
        getInputProps,
        getMenuProps,
        getRootProps,
        getItemProps,
        getToggleButtonProps, // this is what we destructure
        isOpen,
        highlightedIndex
      }) => (
        <Form {...getRootProps()}>
          <InputContainer>
            <Input {...getInputProps()} />
            <Button {...getToggleButtonProps()}>{isOpen ? "-" : "+"} . 
            </Button> 
             // here is where we spread it 
          </InputContainer>
          <Menu {...getMenuProps()}>
            {isOpen &&
              options
              .map((item, index) => (
                <Item
                  {...getItemProps({
                    style: {
                      backgroundColor:
                        index === highlightedIndex ? "lightgray" : null
                    },
                    key: item.value,
                    item,
                    index
                  })}
                >
                  {item.value}
                </Item>
              ))}
          </Menu>
        </Form>
      )}
    </Downshift>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。现在我们有了一个可以打开和关闭的下拉菜单，它有一些选项，我们可以用键盘上下浏览这些选项。现在我们需要添加过滤功能。这样我们就可以输入我们的`<Input>`并缩小我们的搜索范围！

我们需要通过降档来破坏`inputValue`。当组件初始化时，它保存初始值。

现在，我们添加了根据输入的字母缩小搜索范围的功能

另外，我们需要给`<Downshift>`添加一个叫做`itemToString`的道具。降档使用它来处理所选项目的值。如果没有这个，当我们通过点击或输入选择一个项目时，`<Input` >组件将被字符串`[object Object]`填充，当然我们不希望这样。本质上，它最终导致输入字段的字符串值。要么是选中的项，要么是空字符串，要么是选择的占位符

我个人倾向于让它尽可能接近`<select><option>` API。所以，接下来，我想为事件变化添加`event listener`。

```
const Dropdown: React.FC<IDropdownProps> = ({ options, onChange }) => {
  return (
    <Downshift 
      onChange={selectedItem => onChange(selectedItem ? selectedItem.value : "")} 
      itemToString={item => (item ? item.value : "")}
    >
      {({
        getInputProps,
        getMenuProps,
        getRootProps,
        getItemProps,
        getToggleButtonProps,
        isOpen,
        highlightedIndex,
        inputValue,          // we destructure this from Downshift
        clearSelection       // add this to clear the currently selected item
      }) => (
        <Form {...getRootProps()}>
          <InputContainer>
            <Input {...getInputProps()} />
            <Button {...getToggleButtonProps()}>
              {isOpen ? "-" : "+"}
            </Button>
             // add this to clear the currently selected item
            <Button onClick={clearSelection}>x</Button> 
          </InputContainer>
          <Menu {...getMenuProps()}>
            {isOpen &&
              options
                .filter(item => !inputValue || item.value.includes(inputValue))     // added to narrow down ooptions                .map((item, index) => (
                  <Item
                    {...getItemProps({
                      style: {
                        backgroundColor:
                          index === highlightedIndex ? "lightgray" : null
                      },
                      key: item.value,
                      item,
                      index
                    })}
                  >
                    {item.value}
                  </Item>
                ))}
          </Menu>
        </Form>
      )}
    </Downshift>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 用法

* * *

```
import * as React from "react";
import { render } from "react-dom";
import Dropdown from "../components/Dropdown";
import "./styles.css";

const data = [
  { value: "One" },
  { value: "Two" },
  { value: "Three" },
  { value: "Four" },
  { value: "Five" }
];

function selectedItem(val) {
  console.log(val);
}
function App() {
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <Dropdown onChange={selectedItem} options={data} />
    </div>
  );
}

const rootElement = document.getElementById("root");
render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！
[https://codesandbox.io/embed/wgkuv](https://codesandbox.io/embed/wgkuv)