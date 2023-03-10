# 学习 NextJs -第 5 天

> 原文：<https://dev.to/eperedo/learning-next-day-5-46bi>

在服务器端呈现页面的好处之一是能够完全控制浏览器将接收到的整个 html。

通常在 HTML 中有一个你希望所有页面都不同的部分:标题部分。

接下来有这个特殊的组件**头**。一旦你的页面被渲染，它将把你放入其中的所有内容附加到 head html 标签中。

```
import Head from 'next/head';

function Home({ users }) {
  return (
    <div>
      <Head>
        Home Page
      </Head>
      <h1>Users</h1>
      {users.map(user => (
        <div key={user.id}>
          <p>{user.name}</p>
          <CustomButtom id={user.id}>Click Me</CustomButtom >
        </div>
      ))}
      <style global jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
} 
```

现在，您的主页将在 html 中包含一个标题标签，其值为“主页”，您实际上可以使用浏览器的**查看源**选项来验证它。
你可以为你的每一页设置不同的值。

```
import Head from 'next/head';

function About() {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <Head>
        About Page
      </Head>
    </div>
  );
}

export default About; 
```

### SEO

因为你可以在 head 里面添加任何有效的 html，现在你可以提高你的页面的搜索引擎优化。让我们添加元标签，给谷歌、facebook 和 twitter 机器人更多关于我们网络的信息。

```
function About() {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <Head>
        About Page
        {/* HTML meta tags */}

        <meta
          name="description"
          content="Hey google, bing, altavista, etc this is my about page, please rank me nice!"
        />
        <meta name="robots" content="index, follow" />
        <meta name="author" content="Eduardo P. Rivero" />

        {/* Twitter meta tags */}
        <meta name="twitter:card" content="summary" />
        <meta name="twitter:site" content="@eperedo" />
        <meta name="twitter:title" content="Learning NextJs" />
        <meta
          name="twitter:description"
          content="Hey twitter users! This is my about page built with NextJs!"
        />

        {/* Facebook meta tags */}
        <meta property="og:type" content="article" />
        <meta
          property="og:title"
          content="Hello Facebook! This is my about page built with NextJs!"
        />
        <meta property="og:site_name" content="Learning NextJs" />
        <meta property="og:url" content="http://localhost:3000" />
        <meta
          property="og:image"
          content="https://picsum.photos/id/607/200/300"
        />
        <meta property="article:published_time" content="2019-06-22" />
        <meta
          property="article:author"
          content="https://facebook.com/my-profile"
        />

      </Head>
    </div>
  );
}

export default About; 
```

当然，你也可以对你网站的所有页面做同样的事情。