# 与 react 上下文共享状态和功能

> 原文：<https://dev.to/hlebon/share-states-and-functionalities-with-react-context-4oh>

在这篇文章中，我们将学习如何使用 **React Context** 为一组组件创建并提供状态和功能。

## 一个基本单选按钮组件

首先，让我们创建一个我们称之为 **RadioButton** 的组件，它将接收 **checked** 、 **value** 、 **onChange** 和 **children** 作为道具。我们希望将**‘input’**html 标签封装到 react 组件中，使其可重用。

```
import React from "react";

function RadioButton({ checked, value, onChange, children }){
  return (
    <label>
      <input
        type="radio"
        value={value}
        checked={checked}
        onChange={({ target }) => onChange(target.value)}
      />
      { children }
    </label>
  )
} 
```

这个组件作为一个**表示组件**工作，这不是一个正式的东西，但是很多人喜欢给没有本地状态和返回 jsx 元素的组件起这个名字。

现在我们可以使用这个组件来显示一组 **type="radio"** 的输入，例如动物。

```
function Animals(){
  return (
    <div>
      <RadioButton>🐱</RadioButton>
      <RadioButton>🐶</RadioButton>
      <RadioButton>🐰</RadioButton>
      <RadioButton>🐵</RadioButton>
    </div>
  )
} 
```

[![alt text](img/821113926eaabff72f9cd1757bdb408d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ADHZO0yU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hansgarcia.dev/static/a110489b04fdb1a60427e2f1cda8d49a/4c4ed/radiobtn_animals.png)

为了选择一组选项中的一个，我们需要一个状态来保存当前选择的值。

例如，如果选择值为**【猫】**，状态为**【猫】**，如果改为**【猴子】**，状态将变为**【猴子】**。

## 处理我们组件的状态

让我们创建一个有状态的组件，在这个组件中，我们想知道用户是喜欢一只猫还是一只狗作为宠物。

我知道，我知道，这是个艰难的决定。🤔

```
function Form() {
  const [pet, setPet] = React.useState("cat");

  function handleOnChange(value) {
    setPet(value);
  }

  return (
    <div>
      <RadioButton value="cat" checked={"cat" === pet} onChange={onChange}>
        <span role="img" aria-label="cat">
          🐱
        </span>
      </RadioButton>
      <RadioButton value="dog" checked={"dog" === pet} onChange={onChange}>
        <span role="img" aria-label="dog">
          🐶
        </span>
      </RadioButton>
    </div>
  );
} 
```

让我们回顾一下我们在这里做了什么。

首先，我们从中声明了一个名为**的有状态组件。**

一个**有状态组件**是一个可以有一个或多个本地状态的组件。

*   我们使用初始值为`"cat"`的`React.useState`。
*   然后声明了一个函数`handleOnChange`，它将更新组件的状态。
*   最后，我们将带有相应标签的表情符号`cat`和`dog`传递给 RadioButton 组件。

```
<RadioButton
  value="dog"
  checked={"dog" === pet}
  onChange={handleOnChange}>
  <span role="img" aria-label="dog">
    🐶
  </span>
</RadioButton> 
```

[![radio button](img/63645d74ff1e22c95e6181f5dcdb1911.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZM08J90E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hansgarcia.dev/choose_a_pet-33d4e76c7d35a41c14507da8fc840707.gif)

## 使用上下文通过组件共享状态

一个**单选按钮**背后的逻辑很简单，它只允许用户选择一组选项中的一个，在这种情况下，用户只需在中做出选择🐱或🐶。

我们将使用 React 上下文通过单选按钮组件共享状态。

让我们用`React.createContext()`创建一个上下文，返回值将被赋给一个名为`RadioContext`的常量。

```
const RadioContext = React.createContext(); 
```

我们将把有状态组件的名称从 **Form** 改为 **RadioGroup** ，现在它将获得三个新的道具: **defaultValue** 、 **onChange** 和 **children** 。

```
- function Form()
+ function RadioGroup({ children, defaultValue, onChange }){
  //...
} 
```

我们将把旧的`pet`和`setPet`变量名重命名为更通用的名字，如`state`、`setState`，这个状态将保持为一个空字符串。

```
- const [pet, setPet] = React.useState("cat");
+ const [state, setState] = React.useState(""); 
```

现在我们正在接收一个新的 prop **defaultValue** 我们需要在它每次改变时将其添加到状态中，所以我们将使用 **React.useEffect** 。

```
React.useEffect(()=>{
    setState(defaultValue)
  }, [defaultValue]) 
```

在 return 语句中，我们将使用`RadioContext.Provider`来允许其他组件订阅上下文变化，我们将在`value={[state, onChange]}`
中提供这些值

```
<RadioContext.Provider value={[state, onChange]}>
  <div role="radiogroup">
    {children}
  </div>
</RadioContext.Provider> 
```

现在让我们把所有这些移到另一个文件`radioButton.js`

```
// radioButton.js
import React from "react";
const RadioContext = React.createContext();

function RadioGroup({ children, defaultValue, onChange }) {
  const [state, setState] = React.useState("");

  function handleOnChange(value) {
    setState(value);
    onChange(value); // we can call the onChange prop and pass the new value
  }

  React.useEffect(() => {
    setState(defaultValue);
  }, [defaultValue]);

  return (
    <RadioContext.Provider value={[state, handleOnChange]}>
      <div role="radiogroup">{children}</div>
    </RadioContext.Provider>
  );
} 
```

## 从上下文中消耗状态的变化。

我们的组件需要一种方法来获取上下文提供的值。
我们将使用 **React.useContext** ，我们将传递之前创建的**无线电上下文**作为输入`React.useContext(RadioContext)`，这将从提供者`<RadioContext.Provider value={[state, onChange]}>`
返回值

```
function useRadioContext(){
  // we could use array destructuring if we want
  // const [state, onChange] = React.useContext(RadioContext);
  const context = React.useContext(RadioContext);
  if (!context) {
    throw new Error(
      `Radio compound components cannot be rendered outside the Radio component`
    );
  }
  return context;
} 
```

在这里，我们只验证 **RadioButton** 组件是否在 **RadioGroup** 上下文组件中使用，如果不是，它将抛出一个错误。

## 订阅修改

单选按钮组件需要订阅**单选按钮组**组件中的更改。

```
function RadioButton({ value, children }) {
  const [state, onChange] = useRadioContext();
  const checked = value === state;
  return (
    <label>
      <input
        value={value}
        checked={checked}
        type="radio"
        onChange={({ target }) => onChange(target.value)}
      />
      {children}
    </label>
  );
} 
```

然后我们只需要通过比较来自上下文的状态(值)和组件的**值**来知道组件是否被**检查**。

让我们看看完整的代码。

```
// radioButton.js
import React from "react";

const RadioContext = React.createContext();

function useRadioContext() {
  const context = React.useContext(RadioContext);
  if (!context) {
    throw new Error(
      `Radio compound components cannot be rendered outside the Radio component`
    );
  }
  return context;
}

function RadioGroup({ children, defaultValue, onChange }) {
  const [state, setState] = React.useState("");

  function handleOnChange(value) {
    setState(value);
    onChange(value);
  }

  React.useEffect(() => {
    setState(defaultValue);
  }, [defaultValue]);

  return (
    <RadioContext.Provider value={[state, handleOnChange]}>
      <div role="radiogroup">{children}</div>
    </RadioContext.Provider>
  );
}

function RadioButton({ value, children }) {
  const [state, onChange] = useRadioContext();
  const checked = value === state;
  return (
    <label>
      <input
        value={value}
        checked={checked}
        type="radio"
        onChange={({ target }) => onChange(target.value)}
      />
      {children}
    </label>
  );
}

RadioGroup.RadioButton = RadioButton;

export default RadioGroup; 
```

在文件的底部，我们导出 Radio 组件作为一个**导出缺省值**，但是在我们添加 **RadioGroup** 组件作为组件的属性之前。

## 使用我们定制的组件

```
import React from "react";
import ReactDOM from "react-dom";
import RadioGroup from "./radioButton";

function App() {
  return (
    <RadioGroup
      defaultValue="cat"
      onChange={value => console.log("value: ", value)}
    >
      <RadioGroup.RadioButton value="cat">
        <span role="img" aria-label="cat">
          🐱
        </span>
      </RadioGroup.RadioButton>
      <RadioGroup.RadioButton value="dog">
        <span role="img" aria-label="dog">
          🐶
        </span>
      </RadioGroup.RadioButton>
    </RadioGroup>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

现在我们的新组件工作了，可能有点冗长，但我喜欢它。

这不是一个详细的实现，而是使用 **React 上下文**的一个起点。

如果你想玩一下，试试 **codesandbox**

[https://codesandbox.io/embed/radio-button-with-react-context-ktkf4](https://codesandbox.io/embed/radio-button-with-react-context-ktkf4)