# 使用 React Hooks 和 Cloudflare Workers 构建电影应用程序

> 原文：<https://dev.to/jgb-solutions/building-a-movie-app-with-react-hooks-and-cloudflare-workers-1m2p>

你好。这是我在这里的第一个教程。我叫 Jean Gérard，是海地太子港的一名开发人员。

因此，我已经花了几个月的时间开发这个 Spotify/SoundCloud 的克隆应用`MP3 Pam`。对于 API，在前端使用 React，在后端使用 Laravel。我经常使用 React Hooks 和 Cloudflare Workers，我认为在互联网上分享一些我学到的东西会很好。；)

那么我们要建造什么呢？一款[电影应用](https://movie-app-workers.jgb.solutions/)([movie-app-workers . jgb . solutions](https://movie-app-workers.jgb.solutions/))，可以让你搜索任何电影、连续剧或电视节目。我们将利用 OMDb API 。每天最多 1000 次请求都是免费的。我们将使用`Cloudflare Workers`来保护我们的 API 密钥，进行一些重新路由，以及大量的缓存。这将允许我们绕过他们每天 1000 个请求的限制，免费获得漂亮的 API urls，因为 Cloudflare Workers 每天最多可以免费获得 *100 000 个*请求。

那么什么是反应呢？React 是一个 JavaScript 库(也可以称为框架)，可以让你为 web (React.js)和 mobile (React Native)创建更好的 UI(用户界面)。

这个反作用钩子怎么样？是的，所以根据官方文件`Hooks are a new addition in React 16.8\. They let you use state and other React features without writing a class. Hooks are backwards-compatible. This page provides an overview of Hooks for experienced React users. This is a fast-paced overview.`换句话说，钩子将允许我们只使用功能组件，并为它们添加状态管理和生命周期，而不需要使用`class`。这是一件好事，因为与传统的 T2 相比，T4 似乎提供了很多优势。

关于我们列表中的最后一个流行词，Cloudflare Workers？他们的文档声明`Cloudflare Workers provides a lightweight JavaScript execution environment that allows developers to augment existing applications or create entirely new ones without configuring or maintaining infrastructure.`换句话说，我们可以用它来做传统服务器所做的事情，只是我们不需要管理它们，甚至不需要为它们付费。耶！

好了，现在要完成这个教程，你需要一些 React 知识， [Node.js](https://nodejs.org/en/) ，一个[代码编辑器](https://code.visualstudio.com/)，和一个安装在你机器上的[浏览器](https://www.google.com/chrome/)。

接下来，您可以克隆 starter 文件([客户端](https://github.com/jgb-solutions/movie-app-workers-client)、 [api](https://github.com/jgb-solutions/movie-app-workers-api) 和`git checkout starter`来访问`starter`分支，或者使用 [create-react-app](https://github.com/facebook/create-react-app) 和[牧马人](https://github.com/cloudflare/wrangler)来分别创建一个新的 react 项目和一个 workers 项目。

`cd`到`client`文件夹，运行`yarn`。该命令将安装在本地开发时运行我们的应用程序所需的节点依赖项。当你这么做的时候，用`yarh add lodash-es`拉动`lodash-es`。当对象或任何属性的父属性未定义时，我们将利用它的`get`方法来访问对象属性，而不会得到错误。

我已经在`App.css`文件中导入了 Bootstrap 4 CSS，让我们从一些基本的样式开始，因为这不是本教程的主题。

一旦所有东西都安装好了，运行`yarn start`，你应该会看到一个空白页。没错。我们还没有做任何新奇的事情。

现在我们需要在`src`文件夹中创建两个文件:`MovieList.js`和`useMovies.js`。

将负责显示搜索输入和电影(连续剧，电视节目)列表，并从 API 加载更多项目。

继续粘贴这段代码，我会解释它是做什么的。

```
import React from 'react';
import { get } from 'lodash';

import useMovies from './useMovies';
import logo from './logo.svg';

let debounceSearch;

function MovieList() {
  const [
    movies,
    setSearchTerm,
    isLoading,
    canLoadMore,
    fetchMovies,
    lastSearchTerm,
    setMovies,
  ] = useMovies()

  const handleSearch = event => {
    const searchTerm = event.target.value.trim();

    if (searchTerm.length > 2) {
      clearTimeout(debounceSearch)
      // do search
      debounceSearch = setTimeout(() => {
        setSearchTerm(searchTerm);
      }, 500);
    } else {
      setMovies([]);
    }
  }

  return (
      <div className="col-sm-8 offset-sm-2">
        <header>
          <h1>
            <img src={logo} alt='Movie App Workers' className='logo' f/>
            Movie App
          </h1>
        </header>
        <form>
          <div className="input-group">
            <input type="text"
              className="form-control"
              placeholder="Search any movie, series or TV Shows"
              onChange={handleSearch}
            />
          </div>
        </form>
        <br />
        {isLoading && <h2>Search Loading...</h2>}
        <div className="row">
          {movies.length ? (
            movies.map(movie => {
              const title = get(movie, 'Title', `No Title`);
              const movieId = get(movie, 'imdbID')
              let poster = get(movie, 'Poster');
              if (!poster || poster === 'N/A') {
                poster = `https://dummyimage.com/300x448/2c96c7/ffffff.png&text=No+Image`;
              }
              const type = get(movie, 'Type', `undefined`);
              const year = get(movie, 'Year', `undefined`);

              return (
                <div key={movieId} className="col-sm-6 mb-3">
                  <div className="row">
                    <div className="col-7">
                      <img src={poster} alt={title} className='img-fluid' />
                    </div>
                    <div className="col-5">
                      <h3 className='movie-title'>{title}</h3>
                      <p>Type: {type}.<br /> Year: {year}</p>
                    </div>
                  </div>
                </div>
              )
            })
          ) : lastSearchTerm.length > 2 ? <div className="col-12"><h2>No Movies Found</h2></div> : null}
        </div>
        {!!movies.length && canLoadMore && (
          <button
            className='btn btn-primary btn-large btn-block'
            onClick={fetchMovies}>
            Load More
          </button>
        )}
        <br />
        <br />
        <br />
      </div>
    )
}

export default MovieList; 
```

Enter fullscreen mode Exit fullscreen mode

我承认这是一段很长的代码。这里发生的是，我们从创建常规功能组件开始。

```
import React from 'react';
import { get } from 'lodash';

import useMovies from './useMovies';
import logo from './logo.svg'; 
```

Enter fullscreen mode Exit fullscreen mode

我们导入了`react`、来自`lodash`的`get`方法、`useMovies`钩子(我们将在稍后填充)以及我们在应用程序标题旁边使用的默认 react 标志。

接下来我们有

```
let debounceSearch; 
```

Enter fullscreen mode Exit fullscreen mode

这个变量将保存一个定时器 id，我们用它来延迟对 API 的调用，不是每次击键都调用 API，而是等待半秒(`500 milliseconds`)来击键。

下一个有趣的是:

```
 const [
    movies,
    setSearchTerm,
    isLoading,
    canLoadMore,
    fetchMovies,
    lastSearchTerm,
    setMovies,
  ] = useMovies() 
```

Enter fullscreen mode Exit fullscreen mode

这里我们调用我们的`useMovies`钩子，它给我们一个列表`movies`，一个`setSearchTerm`方法来设置我们想要搜索的值，`canLoadMore`是一个布尔值，它告诉我们是否可以加载更多的电影，因此我们将显示或隐藏“加载更多”按钮，`fetchMovies`是我们想要新电影时将调用的方法， `lastSearchTerm`是一个字符串，存储我们成功获得结果的最后一个值，因此让我们将其与我们想要搜索的当前字符串值进行比较，以查看我们是否想要进行新的搜索并清除我们拥有的列表或附加到它，`setMovies`允许在字符长度小于 3 时清空电影列表。

接下来我们有:

```
const handleSearch = event => {
    const searchTerm = event.target.value.trim();

    if (searchTerm.length > 2) {
      clearTimeout(debounceSearch)
      // do search
      debounceSearch = setTimeout(() => {
        setSearchTerm(searchTerm);
      }, 500);
    } else {
      setMovies([]);
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用输入更改事件来访问文本的值，为空白进行修剪，使用`setTimeOut`函数将调用延迟半秒，否则我们将列表设置为空数组。

现在:

```
const title = get(movie, 'Title', `No Title`);
const movieId = get(movie, 'imdbID')
let poster = get(movie, 'Poster');
if (!poster || poster === 'N/A') {
 poster = `https://dummyimage.com/300x448/2c96c7/ffffff.png&text=No+Image`;
}
const type = get(movie, 'Type', `undefined`);
const year = get(movie, 'Year', `undefined`); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用来自`lodash`的`get`来避免未定义对象和属性的错误，为文本和海报提供默认值，并将这些值存储在函数返回的`JSX`中使用的新变量中。

```
{!!movies.length && canLoadMore && (
  <button
   className='btn btn-primary btn-large btn-block'
   onClick={fetchMovies}>
   Load More
  </button> )} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，首先我们将`movies.length`值转换为布尔值，如果这是真的，并且如果我们可以加载更多，我们将显示 load more 按钮，它本身调用`fetchMovies`方法。

这是代码的快速浏览。我希望你能理解剩下的部分。否则请点击此处的在 [Twitter 上联系我。](https://twitter.com/jgb_solutions)

现在将这段代码粘贴到您的`useMovies.js`文件中:

```
import { useState, useEffect } from 'react';

function useMovies() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [canLoadMore, setCanLoadMore] = useState(false);
  const [page, setPage] = useState(1)
  const [searchTerm, setSearchTerm] = useState(undefined)
  const [lastSearchTerm, setLastSearchTerm] = useState('')

  const fetchMovies = async () => {
    setIsLoading(true);
    if (searchTerm !== lastSearchTerm) {
      setPage(1);
      setMovies([]);
    }

    try {
      const response = await fetch(
        `https://movie-api-app.jgb.solutions/search/${searchTerm}?page=${page}`
      );
      const responseBody = await response.json();
      const movies = responseBody.Search;
      const totalResults = parseInt(responseBody.totalResults);
      setIsLoading(false);

      if (searchTerm === lastSearchTerm) {
        setMovies(prevMovies => [...prevMovies, ...movies]);
      } else {
        setMovies([...movies]);
        setLastSearchTerm(searchTerm);
      }

      if (totalResults - (page * 10) > 0) {
        setCanLoadMore(true);
        setPage(prevPage => prevPage + 1)
      } else {
        setCanLoadMore(false);
        setPage(1)
      }

      console.log('response', responseBody);
    } catch (error) {
      console.log(error);
      setIsLoading(false);
    }
  };

  useEffect(() => {
    if (searchTerm)
      fetchMovies();
  }, [searchTerm]);

  return [
    movies,
    setSearchTerm,
    isLoading,
    canLoadMore,
    fetchMovies,
    lastSearchTerm,
    setMovies,
  ];
}

export default useMovies; 
```

Enter fullscreen mode Exit fullscreen mode

让我们一段一段地检查代码。

```
import { useState, useEffect } from 'react'; 
```

Enter fullscreen mode Exit fullscreen mode

我们从从`react`进口`useState`和`useEffect`开始。`React`如果我们在钩子中不使用任何`JSX`，就不需要导入。是的，如果你愿意，你可以在钩子中返回`JSX`，因为它们是 React 组件。

```
const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [canLoadMore, setCanLoadMore] = useState(false);
  const [page, setPage] = useState(1)
  const [searchTerm, setSearchTerm] = useState(undefined)
  const [lastSearchTerm, setLastSearchTerm] = useState('') 
```

Enter fullscreen mode Exit fullscreen mode

接下来在函数内部我们初始化一些状态，我不会再重复了，因为我已经在上面讨论过它们的用法了。

```
const fetchMovies = async () => {
    setIsLoading(true);
    if (searchTerm !== lastSearchTerm) {
      setPage(1);
      setMovies([]);
    }

    try {
      const response = await fetch(
        `https://movie-api-app.jgb.solutions/search/${searchTerm}?page=${page}`
      );
      const responseBody = await response.json();
      const movies = responseBody.Search;
      const totalResults = parseInt(responseBody.totalResults);
      setIsLoading(false);

      if (searchTerm === lastSearchTerm) {
        setMovies(prevMovies => [...prevMovies, ...movies]);
      } else {
        setMovies([...movies]);
        setLastSearchTerm(searchTerm);
      }

      if (totalResults - (page * 10) > 0) {
        setCanLoadMore(true);
        setPage(prevPage => prevPage + 1)
      } else {
        setCanLoadMore(false);
        setPage(1)
      }

      console.log('response', responseBody);
    } catch (error) {
      console.log(error);
      setIsLoading(false);
    }
  }; 
```

Enter fullscreen mode Exit fullscreen mode

fetchMovies 是一个异步方法(因为我们想使用 async/await ),它设置加载状态，根据我们是否在搜索一部新电影(连续剧，电视节目)来设置分页，这样我们就可以在需要时获取新内容。接下来，我们使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)来访问我们的 API 端点，从响应中提取电影和总结果，设置加载状态，将电影添加到我们的电影数组中或将数组设置为电影，并更新`lastSearchTerm`。然后，我们检查是否有更多的条目要加载，方法是用 10 减去我们所在的页面数的乘积，因为 10 是每个响应的条目数。

现在我们需要更新`App.js`文件来导入`MovieList`组件，如下所示:

```
import React from 'react';

import MovieList from './MovieList';

import './App.css';

function App() {
  return (
    <div className="container">
      <div className="row">
          <MovieList />
      </div>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

有了它，我们的应用程序应该能够显示任何查询的结果，比如:
[![Alt Text](img/5d294667fbb43d2d878fe2fe17618f9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rzN7xOz0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqhhsxynqag05xshvjpb.png)

点击我们的`Load More`按钮可以加载更多的相同搜索项目:
[![Alt Text](img/30aba0a31bcc485b614e057ffdfcf03e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u_jOtOVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/87cgcll2sut43ed2yjcw.png)

请注意，我们正在使用我设置的 API，因此您需要为您的应用程序设置自己的 API。

Cloudflare Workers 构建在 [Service Worker](https://developers.google.com/web/fundamentals/primers/service-workers/) [API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) 之上，这是浏览器中的一种新标准，允许您做一些有趣的事情，如缓存资产、推送通知等。这是 Progressive Web App 利用的一个关键特性。Cloudflare Workers 使用 Node.js 和 Google Chrome 运行的相同 V8 引擎。

现在来看看 Cloudflare Workers API。
使用 [API](https://github.com/jgb-solutions/movie-app-workers-api) `starter`分支抢占先机。
在代码编辑器中打开项目。我们需要编辑两个文件:`wrangler.toml`和`index.js`。前往 Cloudflare，创建一个帐户(如果您还没有),并开始添加一个域(如果有)。但是不要求用户开始使用 Cloudflare Workers。如果要将员工发布到您自己的域，则需要帐户 id 和区域 id。你可以在这里创建自己的`wokers.dev`子域[。你还需要你的 API 密匙和你的电子邮件。一旦你有了最后两个，运行`wrangler config`用 CLI 工具配置你的账户。您还可以在每次发布 worker 时使用环境变量，如下所示:](https://workers.cloudflare.com/) 

```
CF_API_KEY=superlongapikey CF_EMAIL=testuser@example.com wrangler publish 
```

Enter fullscreen mode Exit fullscreen mode

现在打开您的`index.js`文件并粘贴这段代码:

```
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event))
})

const API_KEY = `yourApiKey`
const API_URL = `http://www.omdbapi.com`

// if you want to fetch a single movie.
// const getSingleMovieUrl = movieId =>
//   `http://www.omdbapi.com/?i=${movieId}&apiKey=${API_KEY}`

const getSearchUrl = (searchTerm, page = 1) =>
  `http://www.omdbapi.com/?s=${searchTerm}&page=${page}&apiKey=${API_KEY}`

async function fetchApi(event) {
  const url = new URL(event.request.url)
  const uri = url.pathname.split('/')
  const page = url.searchParams.get('page')
  let urlToFetch = `https://movie-app-workers.jgb.solutions/`

  // if you want to fetch a single movie.
  // if (uri[1] === `movie`) urlToFetch = getSingleMovieUrl(uri[2])
  if (uri[1] === `search`) urlToFetch = getSearchUrl(uri[2], page)

  const cache = caches.default
  let response = await cache.match(event.request)

  if (!response) {
    response = await fetch(urlToFetch, { cf: { cacheEverything: true } })
    // const headers = { 'cache-control': 'public, max-age=31536000' }
    // response = new Response(response.body, { ...response, headers })
    event.waitUntil(cache.put(event.request, response.clone()))
  }
  return response
}

async function handleRequest(event) {
  if (event.request.method === 'GET') {
    let response = await fetchApi(event)
    if (response.status > 399) {
      response = new Response(response.statusText, { status: response.status })
    }
    return response
  } else {
    return new Response('Method not allowed', { status: 405 })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先监听 fetch 事件，然后用一个处理请求的方法来响应。
我们设置从[http://www.omdbapi.com/apikey.aspx](http://www.omdbapi.com/apikey.aspx)得到的 API 密钥，以及 API url。

然后我们检查请求的方法是否是`GET`，否则我们将拒绝访问。如果他们使用`GET`请求，那么我们使用我们的助手函数`fetchApi`，它使用事件参数来提取路径、搜索项和页面查询字符串。一旦我们有了新的 url，我们就在缓存中检查是否有匹配。如果没有，我们从 OMDb API 获取 url，并将响应存储在一个`response`变量中。这里有趣的是我们通过`{ cf: { cacheEverything: true } }`获取的第二个参数，这是告诉 Cloudflare 在其大型数据中心网络中尽可能长时间地捕捉响应的一种方式(他们甚至在太子港有一个)。耶！).然后我们返回响应。

现在，为了进行实时测试，我们可以运行`wrangler preview`，它将在 Cloudflare 上构建并发布我们的 worker，并为我们打开一个新的浏览器选项卡来测试我们的 worker。至此，我们完成了工人函数。我建议使用 Postman 之类的工具来测试 API 响应。需要注意的一点是 API 的响应头。如果 Cloudflare 缓存了响应，它将发送一个名为`cf-cache-status`的标头，其值为`HIT`，否则它将等于`MISS`。如果你用相同的术语点击 API，它应该在第二次请求时返回`HIT`。如果不是，你一定做错了什么。

不要忘记在 React 应用程序中更新您的 API url，以使用您自己的 API 密钥。:)

有了这些，你就有了一个使用 React、Hooks 和 Cloudflare Workers 的速度非常快的应用。

我希望即使这篇教程有点长，你也能从中学到一些东西。

你有什么建议或者知道或者已经用这些技术制作了一些更酷的东西，请在评论中告诉我。谢谢！

### 更新

你好。如果你需要托管你的网站或应用，并且预算有限，那么 Vultr 是一个很好的开始。您可以免费试用，并获得 100 美元的积分。如果你这样做，我还会收到 25 美元的信用。[点击此处](https://www.vultr.com/?ref=8402469-6G)获取您的积分。Vultr 快速、可靠且便宜。[在这里获得您的 100 美元信用点数](https://www.vultr.com/?ref=8402469-6G)