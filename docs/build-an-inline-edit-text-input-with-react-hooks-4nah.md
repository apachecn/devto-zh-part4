# 用 React 钩子构建一个内联编辑文本输入

> 原文：<https://dev.to/joelmturner/build-an-inline-edit-text-input-with-react-hooks-4nah>

许多应用程序的一个很好的功能是编辑标题或其他文本，而不离开我们所处的环境。

这是我们将要建造的。

[https://codesandbox.io/embed/inline-text-edit-react-hooks-6ql5m](https://codesandbox.io/embed/inline-text-edit-react-hooks-6ql5m)

让我们来看看这个组件的要求。

*   静止时必须显示文本
*   单击文本以编辑文本
*   输入要保存的密钥
*   Esc 键退出而不保存
*   单击外部进行保存

酷，让我们从创造静息状态开始。我们要用 CSS 做一些基本的样式来帮助我们。

```
import React from "react";

function InlineEdit(props) {
  return (
    <span className="inline-text_copy inline-text_copy--active">
      {props.text}
      <input className="inline-text_input inline-text_input--rest" />
    </span>
  )
}

export default InlineEdit; 
```

```
/* these make sure it can work in any text element */
.inline-text_copy--active,
.inline-text_input--active {
  font: inherit;
  color: inherit;
  text-align: inherit;
  padding: 0;
  background: none;
  border: none;
  border-bottom: 1px dashed #666666;
}

.inline-text_copy--active {
  cursor: pointer;
}

.inline-text_copy--hidden,
.inline-text_input--hidden {
  display: none;
}

.inline-text_input--active {
  border-bottom: 1px solid #666666;
  text-align: left;
} 
```

*   [x]在静止时必须显示文本

这为我们设置了一个简单的文本组件来显示我们的文本。现在诡计开始了！我们想要点击文本并显示输入内容。让我们创建一些状态来跟踪我们是在休息还是在活动。

```
import React, {useState} from "react";
{...}
const [isInputActive, setIsInputActive] = useState(false); 
```

酷，现在我们有一些状态来帮助我们显示/隐藏我们的文本和输入。我们还需要一些状态来跟踪我们输入的内容。让我们添加另一个`useState`来保存文本。

```
const [inputValue, setInputValue] = useState(""); 
```

让我们把这个状态和我们的元素联系起来。

```
function InlineEdit(props) {
  const [isInputActive, setIsInputActive] = useState(false);
  const [inputValue, setInputValue] = useState("");

  return (
    <span className="inline-text">
      <span className={`inline-text_copy inline-text_copy--${!isInputActive ? "active" : "rest"}`}>
        {props.text}
      </span>
      <input
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
        className={`inline-text_input inline-text_input--${isInputActive ? "active" : "rest"}`} />
    </span>
  )
} 
```

*   [x]单击文本以编辑文本

好了，现在我们需要设置文本的保存和转义。我们可以用一个`useEffect`和[钩子](https://usehooks.com/useKeyPress/)来做这件事，它们观察一个按键点击并采取行动。

```
function InlineEdit(props) {
  const [isInputActive, setIsInputActive] = useState(false);
  const [inputValue, setInputValue] = useState(props.text);

  const enter = useKeypress('Enter');
  const esc = useKeypress('Escape');

  useEffect(() => {
    if (isInputActive) {
      // if Enter is pressed, save the text and case the editor
      if (enter) {
        props.onSetText(inputValue);
        setIsInputActive(false);
      }
      // if Escape is pressed, revert the text and close the editor
      if (esc) {
        setInputValue(props.text);
        setIsInputActive(false);
      }
    }
  }, [enter, esc]); // watch the Enter and Escape key presses

  return ({...} 
```

*   [x]回车保存
*   [x] Esc 键退出而不保存

接下来，我们将在包装跨度上添加一个`useRef`来帮助我们判断组件外部是否发生了点击。我们将使用来自 useHooks.com[的](https://usehooks.com/)[挂钩](https://usehooks.com/useOnClickOutside/)。

```
function InlineEdit(props) {
  const [isInputActive, setIsInputActive] = useState(false);
  const [inputValue, setInputValue] = useState(props.text);

  // get the the wrapping span node
  const wrapperRef = useRef(null);

  const enter = useKeypress('Enter');
  const esc = useKeypress('Escape');

  // this hook takes a ref to watch and a function to run
  // if the click happened outside
  useOnClickOutside(wrapperRef, () => {
    if (isInputActive) {
      // save the value and close the editor
      props.onSetText(inputValue);
      setIsInputActive(false);
    }
  });

  useEffect(() => {
    if (isInputActive) {
      // if Enter is pressed, save the text and case the editor
      if (enter) {
        props.onSetText(inputValue);
        setIsInputActive(false);
      }
      // if Escape is pressed, revert the text and close the editor
      if (esc) {
        setInputValue(props.text);
        setIsInputActive(false);
      }
    }
  }, [enter, esc]); // watch the Enter and Escape key presses

  return (
    <span className="inline-text" ref={wrapperRef}>
      {...} 
```

*   [x]单击外部进行保存

当用户点击文本时，我们可以通过聚焦输入来帮助用户。为此，我们可以在输入上添加一个`useRef`和一个`useEffect`来观察输入是否有效。

```
 const inputRef = useRef(null);

  // focus the cursor in the input field on edit start
  useEffect(() => {
    if (isInputActive) {
      inputRef.current.focus();
    }
  }, [isInputActive]);

  {...}

  <input
    ref={inputRef}
    value={inputValue}
    onChange={(e) => setInputValue(e.target.value)}
    className={`inline-text_input inline-text_input--${isInputActive ? "active" : "rest"}`} /> 
```

有很多小零件。让我们把它放在一起，看看我们有什么。

```
import React, { useState, useEffect, useRef } from "react";
import useKeypress from "../hooks/useKeypress";
import useOnClickOutside from "../hooks/useOnClickOutside";

function InlineEdit(props) {
  const [isInputActive, setIsInputActive] = useState(false);
  const [inputValue, setInputValue] = useState(props.text);

  const wrapperRef = useRef(null);
  const textRef = useRef(null);
  const inputRef = useRef(null);

  const enter = useKeypress("Enter");
  const esc = useKeypress("Escape");

  // check to see if the user clicked outside of this component
  useOnClickOutside(wrapperRef, () => {
    if (isInputActive) {
      props.onSetText(inputValue);
      setIsInputActive(false);
    }
  });

  // focus the cursor in the input field on edit start
  useEffect(() => {
    if (isInputActive) {
      inputRef.current.focus();
    }
  }, [isInputActive]);

  useEffect(() => {
    if (isInputActive) {
      // if Enter is pressed, save the text and case the editor
      if (enter) {
        props.onSetText(inputValue);
        setIsInputActive(false);
      }
      // if Escape is pressed, revert the text and close the editor
      if (esc) {
        setInputValue(props.text);
        setIsInputActive(false);
      }
    }
  }, [enter, esc]); // watch the Enter and Escape key presses

  return (
    <span className="inline-text" ref={wrapperRef}>
      <span
        ref={textRef}
        onClick={() => setIsInputActive(true)}
        className={`inline-text_copy inline-text_copy--${
          !isInputActive ? "active" : "hidden"
        }`}
      >
        {props.text}
      </span>
      <input
        ref={inputRef}
        // set the width to the input length multiplied by the x height
        // it's not quite right but gets it close
        style={{ width: Math.ceil(inputValue.length * 0.9) + "ex" }}
        value={inputValue}
        onChange={e => {
          setInputValue(e.target.value);
        }}
        className={`inline-text_input inline-text_input--${
          isInputActive ? "active" : "hidden"
        }`}
      />
    </span>
  );
}

export default InlineEdit; 
```

值得注意的是，输入文本在保存前可能需要清理。我在 DOMPurify 上运气不错。

就是这样！往前走，编辑！