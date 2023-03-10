# 反应闪烁组件

> 原文：<https://dev.to/10secondsofcode/react-blink-component-4o2h>

# 做出反应眨眼

这个可重用的 React 组件将设法闪烁任何文本，并根据需要更改文本颜色和字体大小。

Blink 接受普通的 HTML 标签并输出普通的 HTML 标签。这非常简单，对初学者也很友好。

## 举例

```
import React, { Component } from "react";
import Blink from 'react-blink-text';

class Application extends Component {
  render() {
    return (
      <div className="Application">
        <Blink color='blue' text='TestReactApp' fontSize='20'>
          Testing the Blink
        </Blink> 
      </div>
    );
  }
}
export default Application; 
```

## 特性

1.  支持所有的 html 标签:H1，H2，h3，..等。，Span，Div，一个 Href 标签。
2.  支持所有不同类型的颜色
3.  支持服务器端呈现。

## 安装

### 纱线:

```
yarn add react-blink-text 
```

### Npm:

```
npm install --save react-blink-text 
```

## 回购链接:

链接:[https://github.com/10secondsofcode/react-blink-text](https://github.com/10secondsofcode/react-blink-text)
创建问题:[https://github . com/10 seconds ofcode/react-blink-text/issues/new](https://github.com/10secondsofcode/react-blink-text/issues/new)