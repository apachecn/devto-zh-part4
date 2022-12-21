# 5 个反应片段让你开始

> 原文：<https://dev.to/jasterix/5-react-snippets-for-the-programming-student-341k>

作为一个狂热的代码读者，我总是把吸引我眼球的漂亮代码藏起来。这是开发我的代码词汇的有效习惯。

最近，我开始使用[代码笔记](https://lauthieb.github.io/code-notes/)，这是一个免费的开源应用程序，用于跟踪代码片段。

由于 Code Notes 将我的代码片段保存在我的计算机上，我不确定分享这些内容的最佳方式。所以，现在，我把这些片段贴在这篇文章上，不过如果时间允许，我可能会把它们移到其他地方。

#### 初始化状态无道具

```
class App extends React.Component {
  state = {
    empty_array_state: [],
    empty_string_state: ""
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 做一个(获取)获取请求

*   这应该发生在 componentDidMount()中

```
 componentDidMount() {
    fetch(your_url)
    .then(res => res.json())
    .then(data => {
      this.setState({your_state:data})
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 处理事件

*   在这里，您可以基于用户的输入或任何事件被触发时设置状态

```
 handleEvent = (event) => {
   ....add code here....
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 迭代

*   您仍然可以在 render()中使用循环

```
 render(){

    let listItems = this.props.myList.map(item => {
      return(
        <Item
          id={item.id}
          name={item.name} />
    )
  })
    return (
      <div>
        {listItems}
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用绝对进口

*   这可以防止您的代码在移动文件时被破坏

```
import { Header } from 'components/Header'
import { HeaderContainer } from 'containers/HeaderContainer'
import headerStore from 'store/headerStore' 
```

Enter fullscreen mode Exit fullscreen mode

在阅读我最喜欢的 React 资源之一 Alligator.io 时，我遇到了 absolute imports。如果你想了解更多关于绝对进口的信息，看看这篇文章