# React 文件结构片段帮助您入门

> 原文：<https://dev.to/jasterix/react-file-structure-snippets-to-get-you-started-16o8>

反应过来我最近不是最好的朋友。在组件之间传递数据以及从组件到子组件的工作，每个组件都有不同的状态，对我来说并不直观。

所以今天，我决定花点时间来寻找一些有助于构建 React 文件的模式。

#### 可能会保持状态的组件

*   形式
*   输入
*   条件显示

### 通用 Form.js

```
 state ={
        title: "",
        description: ""
    }

    handleChange = (event) => {
        this.setState({title: event.target.value})
    }
    handleSubmit = (event) => {
        this.props.newBook(this.state)
    }

...

return (
    <form onSubmit={this.handleSubmit}>
    <input onChange={this.handleChange} value= {this.state.title}> 
```

Enter fullscreen mode Exit fullscreen mode

#### 通用 App.js

```
 state = {
        books: [] 
    }

    ComponentDidMount() {
        fetch(url)
        .then(res => res.json())
        .then(data => {
            this.setState({books: data})
    }

    handleNewBook = (bookData) => {
        const newBook = { ...bookData }
        this.setState({books:[...this.state.books, newBook]})
}

render() {
    return(
        <NewBookForm onNewBook = {this.handleNewBook} />
        <Container books= {this.state.books} /> 
```

Enter fullscreen mode Exit fullscreen mode

#### 通用容器. js

```
render() {
    const books = this.props.books.map(bookData => <Book book={bookData} />) return (
    <div>
    { books }
    </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

#### 通用 Book.js

```
 <div>
        <h1> {this.props.book.title} </h1>
        <p> {this.props.book.content} </p>
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

*   查看本文以更深入地了解如何创建和使用 React 组件
*   我还推荐[这篇文章](https://www.robinwieruch.de/react-pass-props-to-component/)，它深入探讨了组件之间的传递属性(尽管它确实使用了 state，[，我认为](https://dev.to/jasterix/state-or-this-state-4a80)使得 React 更加困难)