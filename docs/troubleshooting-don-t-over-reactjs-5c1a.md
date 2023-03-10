# 故障排除？不要反应过度！

> 原文：<https://dev.to/knheidorn/troubleshooting-don-t-over-reactjs-5c1a>

对于我在熨斗学校的顶点项目，我决定在 React 中构建一个缩小版的*价格合适*。简化游戏的功能是在三周内完成项目的关键。与任何游戏一样，逻辑主导了大部分页面呈现，我依靠`React Router`来动态浏览我的各个页面。这并没有引起任何问题，直到我在一个链接的`onClick`中有了一个`fetch PATCH`请求。我用一个链接包装了一个按钮元素，这样我就可以按照自己的要求设计它了。在按钮内部，我将`fetch`请求函数传递给了 props 中的组件。然而，该功能需要`event.preventDefault()`。更熟悉`React Router`的人会发现我如何设置代码的问题。作为一个新手，我不明白为什么链接不能重定向。`fetch PATCH`请求会像内部`console.log()`一样完成。问题显然出在链接上。

在阅读了`React Router`文档后，我了解到`event.preventDefault()`阻止了链接将用户重定向到正确的页面。为了正确地重定向并保留该功能，我需要访问会话`history` prop for `React Router`来将用户重定向到正确的路线。我不确定如何做到这一点，但谢天谢地，泰勒·麦金尼斯的这篇博客帮助指引了正确的步骤。由于重定向不是由`React Router`呈现的，我不得不将组件包装在`withRouter`中以访问`history.push`。

`export default withRouter(SpinningWheel )`

一旦`history.push`可以访问，我就在包装的组件中创建一个回调函数，强制重定向到我想要的页面。

```
goHome = () => {
   this.props.history.push('/')
} 
```

因此，我编写了回调函数，但是我需要将它与`fetch`请求挂钩。预先调用这个回调函数是没有用的，因为在游戏完成后,`fetch PATCH`会更新用户的分数。我只想在分数正确保存后重定向用户。`fetch`函数在我的主组件页面上，因此，我必须将`goHome`作为参数传递，并为`fetch`请求添加一个回调函数。为了简单起见，我在代码中将其称为`cb`。

在`withRouter(<Component>)` :

```
<Link to="/">
   <button className="Start-button"
      onClick={(ev) => saveMoney({ totalMoney }, ev, this.goHome)}>
      Go Home
   </button>
</Link> 
```

在`App.js` :

```
saveMoney = (money, event, cb) => {
   event.preventDefault()

   cb & cb()

   let { gameId } = this.state
   let url = "http://localhost:3000/games/" + gameId

   let config = {
      headers:{
         'Content-Type': 'application/json'
       },
      method: 'PATCH',
      body: JSON.stringify({
         score: money.totalMoney
      }
   }

   fetch(url, config)
      .then(response => response.json())
      .then(data => {() => {
         this.getScores()
         cb & cb()
      })
} 
```

最后，我现在可以重定向用户，而不需要对代码做太多改动，也不会给用户带来重新加载的效果，也就是正确地使用 React。

老实说，这是我在 React 游戏中遇到的众多挑战之一。不出所料，我的 3D 纺车成了我最棘手的问题。下周，我将写并分享我的代码(还有爱！)用于垂直旋转木马。敬请期待！

了解更多关于 [`withRouter`和其他高阶组件(HOC)](https://reactjs.org/docs/higher-order-components.html)