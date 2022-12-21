# 反应钩，是什么？怎么用？第 2 部分-使用“使用状态”和“使用效果”

> 原文：<https://dev.to/lukyhenson/react-hooks-what-is-it-how-to-use-it-pt-2-using-usestate-and-useeffect-4mee>

在第二部分，在我们的项目示例之后，我将向您展示如何使用`useState`和`useEffect`。

### 第三步:继续使用“使用状态”

*   因此，现在我们可以在列表中键入一本新书并保存它:

```
// We need to import useState from react
import React, { useState } from 'react';

export default function App() {
  const [books, setBooks] = useState(['Javascript', 'React']);
  /* Here we're going to create a new state to save the value typed for newBook 
  */ 
  const [newBook, setNewBook] = useState('');

  /* Here we're going to create a function to bind this value inputted
  */
  const handleBookInput = e => setNewBook(e.target.value);

  /* Here we're going to pass as argument 'newBook' xD */
  const addBook = () => setBooks(newBook);

  return (
    <>
      <input 
        value={newBook}
        onChange={handleBookInput}
        placeholder="add here you new book"
        onKeyPress={e => e.key === 'Enter' && addBook()}
      />
      <button onClick={addBook}>Add Book!</button>
      <ul>
        {books.map((book, index) => <li key={index}>{book}</li>)}
      </ul>
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   我将展示第一个和第二个代码之间的区别，这些代码做同样的事情😄

```
-------- import React from 'react'; +++ import React, { useState } from 'react'; 
-------- class App extends React.Component {
+++ export default function App() { --------   state = {
--------     books: ['Javascript', 'React'],
--------     newBook: '',
--------   } +++  const [books, setBooks] = useState(['Javascript', 'React']);
+++  const [newBook, setNewBook] = useState(''); 
--------  handleBookInput = e => this.setState({ newBook: e.target.value });
+++  const handleBookInput = e => setNewBook(e.target.value); 
--------  addBook = () => {
--------    this.setState({
--------      books: [...this.state.books, this.state.newBook],
--------      newBook: '',
--------    });
--------  } +++  const addBook = () => setBooks(newBook); 
--------  render() {
    return (
      <>
        <input
--------       value={this.state.newBook} +++       value={newBook} --------       onChange={this.handleBookInput} +++       onChange={handleBookInput}
          placeholder="add here you new book"
--------       onKeyPress={e => e.key === 'Enter' && this.addBook()} +++       onKeyPress={e => e.key === 'Enter' && addBook()}          
        />
--------     <button onClick={this.addBook}>Add Book!</button> +++     <button onClick={addBook}>Add Book!</button>
        <ul>
--------       {this.state.books.map(
--------         (book, index) => <li key={index}>{book}</li>
--------       )} +++       {books.map((book, index) => <li key={index}>{book}</li>)}
        </ul>
      </>
    );
--------} +++}; 
--------export default App;
+++ 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:使用 useEffect

*   之前我向你展示了如何在一个功能组件中使用状态，现在我将展示如何使用生命周期方法；
*   首先，这些是从`React.Component`开始最常用的生命周期方法:
    *   `componentDidMount()`
    *   `componentDidUpdate()`
    *   `componentWillUnmount()`
*   座椅放下，在挂钩上使用它的方法是如此困难和昂贵😄：

```
 useEffect(() => {}, []); // xD easier, right? hehehe 
```

Enter fullscreen mode Exit fullscreen mode

*   让我们将这些方法应用到我们的例子中，我们将使用 componentDidUpdate 来更改`document.title`以显示添加了多少本书，首先是没有挂钩的类组件:

```
import React from 'react';

class App extends React.Component {
  state = {
    books: ['Javascript', 'React'],
    newBook: '',
  }

  componentDidUpdate() { 
    document.title = `Current books added: ${this.state.books.length}`
  }

  handleBookInput = e => this.setState({ newBook: e.target.value });

  addBook = () => {
    this.setState({
      books: [...this.state.books, this.state.newBook],
      newBook: '',
    });
  }

  render() {
    return (
      <>
        <input
          value={this.state.newBook}
          onChange={this.handleBookInput}
          placeholder="add here you new book"
          onKeyPress={e => e.key === 'Enter' && this.addBook()}
        />
        <button onClick={this.addBook}>Add Book!</button>
        <ul>
          {this.state.books.map(
            (book, index) => <li key={index}>{book}</li>
          )}
        </ul>
      </>
    )
  }
};

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

*   同样使用钩子:

```
// We need to import useEffect from react
import React, { useState, useEffect } from 'react';

export default function App() {
  const [books, setBooks] = useState(['Javascript', 'React']);
  const [newBook, setNewBook] = useState('');

  const handleBookInput = e => setNewBook(e.target.value);

  const addBook = () => setBooks(newBook);

  /* using useEffect you can use all lifecycle methods 
  to use componentDidMount() {} in hooks you should code something like that:
  useEffect(() => {
    ....
  }, []);
  here useEffect get 2 arguments a function and a array, inside 
  the function will contain your code, on componentDidMount the array value
  need to be empty.

  to use componentDidUpdate() {} in hooks the code will look like changing only one thing:
  useEffect(() => {
    ....
  }, [state_to_observe]);
  here use effect get 2 arguments like the code before, but the only change is on the
  second argument receives the state observer (Note: if do you want to observer many states 
  you can put that states inside the array), in our sample we're going to use useEffect with state observer.
  */

  /* When that state has any changes, the function inside useEffect is called xD  easy peasy, right?
  */

  useEffect(() => {
    document.title = `Current books added: ${books.length}`
  }, [books]);

  return (
    <>
      <input 
        value={newBook}
        onChange={handleBookInput}
        placeholder="add here you new book"
        onKeyPress={e => e.key === 'Enter' && addBook()}
      />
      <button onClick={addBook}>Add Book!</button>
      <ul>
        {books.map((book, index) => <li key={index}>{book}</li>)}
      </ul>
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我们将使用`useCallback()`，再见😄

[![to be continued](img/5c86c766672014929ba5ffdc0772230e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M7amY-63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydfudxm1k67q7npg62b8.jpg)

React hooks 角 1 - [链接此处](https://dev.to/lukyhenson/react-hooks-what-is-it-how-to-use-it-pt-1-using-usestate-1k8)
React hooks Pt。2 - [链接此处](https://dev.to/lukyhenson/react-hooks-what-is-it-how-to-use-it-pt-2-using-usestate-and-useeffect-4mee)