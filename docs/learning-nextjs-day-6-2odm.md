# 学习 NextJs -第 6 天

> 原文：<https://dev.to/eperedo/learning-nextjs-day-6-2odm>

URL 是每个 web 应用程序的关键部分，让我们看看如何在不同的页面之间传递数据。

让我们开始向我们的用户列表添加一个链接，这个链接将重定向到 about 页面，但是传递所选用户的 id。我们将使用该 ID 获取当前用户的信息，并显示在页面中。此外，我们将使用这些信息为每个用户生成不同的元标签。

为此，我们将使用 Link 组件。

```
import Link from 'next/link';

function Home({ users }) {
  return (
    <div>
      <Head>
        Home Page
      </Head>
      <h1>Users</h1>
      {users.map((user) => (
        <div key={user.id}>
          <p>{user.name}</p>
          {/* here we pass the user id in the query string */}
          <Link
            prefetch
            href={`/about?userId=${user.id}`}
          >
            <a>About</a>
          </Link>
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

如果你转到根 url，你可以看到每个用户的“关于”链接。如果你点击它，它将带你到关于页面以及用户 id。

现在，让我们实际获取该用户 id 并获得关于该特定用户的信息。我们需要使用 getInitialProps 方法。该函数中可用的值之一是**查询**对象。它将包含您作为查询字符串在 url 中传递的所有值。在这种情况下，**查询**是一个具有属性 **userId** 和所选用户的值的对象。

大概是这样:

```
query = {
  userId: 1, // The value is dynamic depending of the user selected
// in the home page.
} 
```

有了用户 id，我们就可以使用 jsonplaceholder API 来获取其余的用户信息。

```
 async function getInitialProps({ query }) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/users/${query.userId}`);
  const user = await response.json();
  // This method always needs to return an plain JS object.
  // The object is going to be the props of our page component.
  return { user };
}

About.getInitialProps = getInitialProps; 
```

现在在**关于**页面中有了一个用户道具。让我们使用这些信息为每个用户生成定制的元标签。

```
import Head from 'next/head';
import fetch from 'node-fetch';

function About({ user }) {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <p>
        The author of this page is {user.name}, check meta tags in the view source!
      </p>
      <Head>
        {user.name} - About Page

        {/* HTML meta tags */}

        <meta
          name="description"
          content="Hey google, bing, altavista, etc this is my about page, please rank me nice!"
        />
        <meta name="robots" content="index, follow" />
        {/* This is going to render a different author meta tag per each user */}
        <meta name="author" content={user.name} />

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

async function getInitialProps({ query }) {
  // 
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${query.userId}`,
  );
  const user = await response.json();
  return { user };
}

About.getInitialProps = getInitialProps;

export default About; 
```

现在转到根 url，单击任何用户的“关于”链接，您将看到每个用户的自定义“关于”页面。如果您查看 about 页面的视图源，您可以看到 author meta 标记的值是用户名！

好吧好吧，我知道你在想什么。在查询字符串中传递数据太 90 年代了！我们完全可以在 next 中使用干净的 URL，但我想这是第 7 天的信息。