# 探索反应

> 原文：<https://dev.to/vicradon/exploring-react-2c4n>

似乎这些天我探索了很多。这意味着我必须静下心来建立真正的项目。所以，这次是最受欢迎的反应。我必须说，我想象为每一件需要完成的小事编写组件。最后，我的项目将有> = 80 个 JS 文件。😅开玩笑的。

但那只是想象。现在，我还在摸索。我知道组件是由以下两者之一创建的:

1.  使用类语法
2.  在创建组件后，使用接收道具作为参数的函数，它们使用

```
ReactDOM.render(<Component />, desired-node) 
```

Enter fullscreen mode Exit fullscreen mode

# 更真实一点的东西

假设我们有一个 id = "test node "的 div

If we want to fix stuff in it using react, it would probably go like this.

```
class MyComponent extends React.Component {
  constructor (){
    super()
  }
  render (){
    return (
      <div>
        <h1>Just another boring text in a component</h1>
      </div>
    )
  }
};

ReactDOM.render(<MyComponent />, document.querySelector('#test-node')) 
```

Enter fullscreen mode Exit fullscreen mode

希望我坚持做这个叫 React 的东西。-有上百万个网站想教我如何反应-。我大概会坚持下去。