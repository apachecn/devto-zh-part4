# 与 react 挂钩——使用 React 路由器创建图书详情页面，第 4 部分

> 原文：<https://dev.to/paramharrison/hooked-with-react-creating-book-details-page-using-react-router-part-4-2pcp>

我们已经为图书搜索页面创建了图书列表页面。现在让我们使用 react router 为每本书创建另一个页面。

你可以在这里查看这个应用程序，

[https://codesandbox.io/embed/rl20opm2l4](https://codesandbox.io/embed/rl20opm2l4)

## 创建图书明细页面

首先，让我们使用 App.js 中的 react router 创建路由，并加载两个页面

*   索引页，这是我们当前的搜索页面，显示图书列表
*   将通过唯一 ID 标识的图书详细信息页面

将我们所有的逻辑移到索引页。创建一个名为`pages`的新文件夹，并创建一个名为`searchPage.js`
的文件

```
import React, { useState } from 'react';
import axios from 'axios';

import BookSearchForm from '../components/bookSearchForm';
import Loader from '../components/loader';
import BooksList from '../components/booksList';

const SearchPage = () => {
  const [searchTerm, setSearchTerm] = useState('');
  const [books, setBooks] = useState({ items: [] });
  const [error, setError] = useState(false);
  const [loading, setLoading] = useState(false);

  let API_URL = `https://www.googleapis.com/books/v1/volumes`;

  const fetchBooks = async () => {
    setLoading(true);
    setError(false);
    try {
      const result = await axios.get(`${API_URL}?q=${searchTerm}`);
      setBooks(result.data);
    } catch (error) {
      setError(true);
    }
    setLoading(false);
  };

  const onInputChange = e => {
    setSearchTerm(e.target.value);
  };

  const onSubmitHandler = e => {
    e.preventDefault();
    fetchBooks();
  };

  return (
    <>
      <BookSearchForm
        onSubmitHandler={onSubmitHandler}
        onInputChange={onInputChange}
        searchTerm={searchTerm}
        error={error}
      />
      <Loader searchTerm={searchTerm} loading={loading} />
      <BooksList books={books} />
    </>
  );
};

export default SearchPage; 
```

并重构`App.js`文件

```
import React from 'react';

import SearchPage from './pages/searchPage.js';
import './App.css';

const App = () => {
  return (
    <>
      <SearchPage />
    </>
  );
};

export default App; 
```

添加`react-router-dom`包并通过路由
呈现搜索页面

```
yarn add react-router-dom 
```

```
import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import SearchPage from './pages/searchPage.js';
import './App.css';

const NoMatchRoute = () => <div>404 Page</div>;const App = () => {
  return (
    <Router> <Switch> <Route path="/" exact component={SearchPage} /> <Route component={NoMatchRoute} /> </Switch> </Router> );
};

export default App; 
```

让我们创建详细页面，并通过 react 路由器进行路由。在`pages`文件夹
中新建一个文件`bookDetailPage.js`

```
import React from 'react';

const BookDetailPage = () => {
  return <div>Book details page</div>;
};

export default BookDetailPage; 
```

将路线添加到`App.js`。在这里，路径将具有路由参数`bookId`来通过 ID 识别图书。

```
<Route path="/book/:bookId" exact component={BookDetailPage} /> 
```

现在通过 react-router 发送的 props 获取`BookDetailPage`中的图书 ID。如果你想了解它如何工作的细节，可以参考我在 react 路由器中关于[动态页面的博文。](https://dev.to/blog/dynamic-pages-in-react-router/)

路线参数通过一个叫做`match`的道具发送。

```
import React from 'react';

const BookDetailPage = ({ match }) => {
  const {
    params: { bookId },
  } = match;

  return (
    <div>
      Book details page: <strong>{bookId}</strong>
    </div>
  );
};

export default BookDetailPage; 
```

## 链接来自图书列表组件的详细信息页面

添加一个链接，转到`BooksList`组件中的详细信息页面。

```
import { Link } from "react-router-dom";

...

<Link to={`/book/${book.id}`}>Show details</Link> 
```

> 使用`Link`是因为，它使用客户端路由。HTML `a`标签将刷新页面并点击服务器来加载新的 URL

好了，我们已经完成了页面之间的链接。现在，我们需要在详细信息页面中显示这本书的详细信息。为此，

*   我们需要用 book ID 调用 API，获取详细信息并呈现输出。让我们开始吧。
*   在调用 API 时，我们需要设置`loading`状态。
*   如果 API 抛出错误，我们需要设置`error`状态。
*   如果 API 返回内容，则设置`book`状态。

这是图书详细信息页面的代码，

*   它只在页面挂载时使用`useEffect` react 钩子来调用图书细节 API。要了解更多关于 useEffect hook 的信息，请点击查看官方文件。

```
import React, { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import axios from 'axios';

import BookDetail from '../components/bookDetail';

const BookDetailPage = ({ match }) => {
  const {
    params: { bookId },
  } = match;
  const [book, setBook] = useState(null);
  const [error, setError] = useState(false);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const API_BASE_URL = `https://www.googleapis.com/books/v1/volumes`;
    const fetchBook = async () => {
      setLoading(true);
      setError(false);
      try {
        const result = await axios.get(`${API_BASE_URL}/${bookId}`);
        setBook(result.data);
      } catch (error) {
        setError(true);
      }
      setLoading(false);
    };
    // Call the API
    fetchBook();
  }, [bookId]);

  return (
    <>
      <Link to={`/`}>Go back to search books</Link>
      {loading && (
        <div style={{ color: `green` }}>
          loading book detail for book ID: <strong>{bookId}</strong>
        </div>
      )}
      {error && (
        <div style={{ color: `red` }}>
          some error occurred, while fetching api
        </div>
      )}
      {book && <BookDetail book={book} />}
    </>
  );
};

export default BookDetailPage; 
```

它使用一个新的组件`BookDetail`来呈现书籍的细节。`bookDetail.js`包含

```
import React from 'react';

import { bookAuthors } from '../utils';

const BookDetail = ({ book }) => {
  const createDescMarkup = description => {
    return { __html: description };
  };

  return (
    <section>
      <div>
        <img
          alt={`${book.volumeInfo.title} book`}
          src={`http://books.google.com/books/content?id=${book.id}&printsec=frontcover&img=1&zoom=1&source=gbs_api`}
        />
        <div>
          <h3>
            <strong>Title:</strong> {book.volumeInfo.title}
          </h3>
          <p>
            <strong>Authors:</strong> {bookAuthors(book.volumeInfo.authors)}
          </p>
          <p>
            <strong>Published Date:</strong> {book.volumeInfo.publishedDate}
          </p>
          <p>
            <strong>Publisher:</strong> {book.volumeInfo.publisher}
          </p>
          <p>
            <strong>Page Count:</strong> {book.volumeInfo.pageCount}
          </p>
          <div
            dangerouslySetInnerHTML={createDescMarkup(
              book.volumeInfo.description
            )}
          />
        </div>
      </div>
    </section>
  );
};

export default BookDetail; 
```

现在我们已经成功地渲染了整个页面。你可以回到搜索页面，搜索任何书籍，并检查它们的详细信息。

在这里尝试一下，

[https://codesandbox.io/embed/books-detail-page-by2nj](https://codesandbox.io/embed/books-detail-page-by2nj)

伙计们，我们可以通过样式和测试来进一步扩展这些应用程序。我要么继续写，要么很快作为单独的博客文章😎

在这里查看第 4 部分[的代码库，在这里](https://github.com/learnwithparam/books-series-react-hooks/commit/de644a750b935268c085cc83d5771a9b88e17432)查看整个系列的代码库[。](https://github.com/learnwithparam/books-series-react-hooks)

### 保持联系！

如果你喜欢这篇文章，你可以在 [Twitter](https://twitter.com/learnwithparam) 上找到我，了解更新、公告和新闻。🐤