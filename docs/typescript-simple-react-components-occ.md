# 打字稿。简单的反应成分。

> 原文：<https://dev.to/pretaporter/typescript-simple-react-components-occ>

你好。我非常喜欢使用 React 和 Typescript。几乎每个新项目都需要简单的组件，像原子，例如:按钮，输入，复选框等。我有一些最佳实践，使它对团队的所有成员友好，但也有严格的打字。我们来看看吧，也许对你也有帮助。

### 按钮

```
import React from 'react';

interface IProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  block?: boolean; // Your some custom prop
}

export const Button: React.FunctionComponent<IProps> = ({ block, children, ...shared }) => {
  return <button {...shared}>{children}</button>;
}

Button.defaultProps = {
  type: 'button',
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。您有自定义的`Button`组件，它支持所有具有严格类型的本机按钮属性。您可以为项目中的所有按钮放置通用的东西，如样式或一些业务逻辑。

对于用

`import React from 'react'`

代替

`import * as React from 'react'`

添加在您的`tsconfig.json`属性`esModuleInterop: true`

### 输入

```
import React from 'react';

interface IProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'onChange' | 'value'> {
  value: string;

  onChange(value: string): void;
}

export const Input: React.FunctionComponent<IProps> = ({ children, onChange, ...shared }) => {
  return <input onChange={e => onChange(e.target.value)} {...shared} />;
}

Input.defaultProps = {
  type: 'text',
}; 
```

Enter fullscreen mode Exit fullscreen mode

省略帮助器类型是在 Typescript 3.5 中添加的。如果您使用以前版本的 Typescript，只需添加以下字符串:

`type Omit<T, K> = Pick<T, Exclude<keyof T, K>>`

### 复选框

```
import React from 'react';

interface IProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'onChange'> {
  onChange(value: boolean): void;
}

export const Checkbox: React.FunctionComponent<IProps> = ({ children, 
onChange, ...shared }) => {
  return <input onChange={e => onChange(e.target.checked)} {...shared} />
}

Checkbox.defaultProps = {
  type: 'checkbox',  
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以像这样使用它:

```
<Checkbox checked={false} onChange={toggleCheckbox} /> 
```

Enter fullscreen mode Exit fullscreen mode

小教程到此结束。如果对某人会有帮助，我可以继续讲解 React 和 Typescript 的一些好东西。感谢关注。