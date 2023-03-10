# 数据的 React 挂钩第 1 部分——获取数据

> 原文：<https://dev.to/bdbch/react-hooks-for-data-part-1-fetching-data-28ge>

因此，对 React Hooks 的大肆宣传已经结束，社区也不再谈论它们了。但是说真的钩子是野兽。在这篇文章中，我将解释如何使用 React 钩子来获取和提交数据给任何 API(在本指南中我将使用 REST)。

## 编写自己的钩子

我们将从编写第一个从 API 获取书籍的钩子开始。下面是示例代码:

```
import { useEffect, useState } from 'react'

// The hook is just a simple function which we can export
export const useFetchBooks = () => {

  // First we define the necessary states for our hook
  // this includes book, the loading state and potential errors
  const [books, setBooks] = useState([])
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  // useEffect can be compared to componentDidMount,
  // componentDidUpdate and componentDidUnmount
  // read more about useEffect here:
  // https://reactjs.org/docs/hooks-effect.html
  useEffect(() => {

    // First we set the loading and error states
    setLoading(true)
    setError(null)

    fetch('https://library.com/api/books')
      .then(res => res.json())
      .then(json => {
        setLoading(false)
        if (json.books) {
          setBooks(json.books)
        } else {
          setBooks([])
        }
      })
      .catch(err => {
        setError(err)
        setLoading(false)
      })
  }, [])
  return { books, loading, error }
} 
```

这看起来很复杂，但实际上并不复杂。去掉注释，这将是一个非常短的函数，它获取数据并更新状态。

现在我们有了这个钩子，我们可以在这样的组件中使用它:

```
import React from 'react'
import { useFetchBooks } from './utils/hooks'

const BookList = () => {
  // use your own hook to load the data you need
  const { books, loading, error } = useFetchBooks()

  if (loading) return <div>Loading...</div>
  if (error) return <div>{error}</div>

  return (
    <div>
      { 
        books &&
        books.length > 0 &&
        books.map(book => <div key={book.id}>{book.title}</div>)
      } 
    </div>
  )
}

export default BookList 
```

## 在钩子中使用参数

现在我们的挂钩工作正常，但仍然有点笨。假设您希望您的用户能够搜索列表中的书籍。你可以这样做:

```
import { useEffect, useState } from 'react'

// Note here the new parameter we pass into the hook called "search"
// this will be used to search the api for specific books
export const useFetchBooks = (search) => {
  const [books, setBooks] = useState([])
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  useEffect(() => {
    setLoading(true)
    setError(null)

    // Change the apiUrl according to the search string
    const apiUrl = search && search.length > 0 ?
      `https://library.com/api/books?search=${search}` :
      'https://library.com/api/books'

    fetch(apiUrl)
      .then(res => res.json())
      .then(json => {
        setLoading(false)
        if (json.books) {
          setBooks(json.books)
        } else {
          setBooks([])
        }
      })
      .catch(err => {
        setError(err)
        setLoading(false)
      })

  // This is important. We pass the new search parameter into
  // the empty array we had before. This means, the effect
  // will run again if this parameter changes
  }, [search])

  return { books, loading, error }
} 
```

现在你可以像这样在你的组件中使用钩子:

```
const { books, loading, error } = useFetchBooks(props.search) 
```

这对于第 1 部分来说应该足够了，并且应该阐明如何使用钩子从任何 API 获取数据。

当我完成这篇文章后，我会马上更新这篇文章的第二部分的链接。

玩得开心！