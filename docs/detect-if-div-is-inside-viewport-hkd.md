# 检测 div 是否在视口内

> 原文：<https://dev.to/nikomontana/detect-if-div-is-inside-viewport-hkd>

我正在使用 NextJS，希望在用户滚动到特定元素时显示一个按钮。不幸的是，我不能让它真正工作。

我有一个`Layout`文件，它在所有组件之间共享。

```
import Navbar from "./Navbar";
import Footer from "./Footer";

const Layout = props => {
  return (
    <div>
      <Navbar />
      {props.children}
      <Footer />
    </div>
  );
};

export default Layout; 
```

我试图在布局文件中实现第一个`<div/>`的`onScroll`，但是它没有被触发。