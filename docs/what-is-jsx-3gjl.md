# 什么是 JSX？！

> 原文：<https://dev.to/temmietope/what-is-jsx-3gjl>

JSX 是 JavaScript 的类似 XML 的语法扩展。它是由脸书的一个团队创造的。它很酷，因为它允许您将视图与功能结合起来，从而简化开发人员的体验。它是 JavaScript 的语法扩展。它被用在 React 中，虽然并不是必须的，但是它对于能够在 JavaScript 代码中使用 UI 非常有用。在 React 中，您可以使用 JSX 轻松地将标记和逻辑松散地耦合到一个简单的单元(组件)中。人们喜欢 React 的原因之一是因为 JSX，因为 JSX 比在普通 JavaScript 中创建和添加许多元素更容易编写和理解。

JSX 更接近 JavaScript，而不是 HTML，所以在编写它的时候有一些关键的区别需要注意。

*   ' class '是 JavaScript 保留关键字，所以在 JSX 中添加 CSS 类时，使用' className '而不是' class'。
*   JSX 的属性和方法是驼峰式的——onsubmit 将变成 on submit。
*   自结束标签必须以斜线结束，例如

这是 JSX 的一个例子。

```
const name = 'Temitope';
const element = <h1 className="hello">Hello, {name}</h1>; 
```

Enter fullscreen mode Exit fullscreen mode

您将 JavaScript 元素放在花括号内。JSX 允许 JavaScript 的所有语言特性，所以可以用在函数中，if 语句，for 循环，赋给变量，用作参数，可以是 JavaScript 函数的返回值。

```
const exactLocation = (user) => {
  return user.state + ' ' + user.country;
}
const greetUser = (user)=>{
  if(user){
    return <h1>Hello, {user.name}. How is {exactLocation(user)} today?</h1>;
  }
  return <h1>Hello, I don't know you</h1>
}

const user = {
  name: 'Temitope',
  state: 'Lagos',
  country: 'Nigeria'
};

const element = (
  <h1>
    {user.name} is from {exactLocation(user)}!
    {greetUser(user)}
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

在 JSX，JavaScript 表达式被放在花括号内。

在上面的代码片段中，JavaScript 函数使用花括号嵌入在 HTML 标记中。为了确保更好的可读性，JSX 被拆分成多行，为了防止分号自动插入，最好将它们放在括号中(如上所示)。

有一组 transpilers 都符合 JSX 语法，但是它们在输出上使用不同的语义。

1.  反应 JSX:使用 JSX 创建反应元素。
2.  jsx 转换
3.  Babel:包括对开箱即用的 JSX 的支持。