# 学习 NextJs 9！-第十天

> 原文：<https://dev.to/eperedo/learning-nextjs-9-day-10-3i6e>

因此，在我学习的过程中，nextjs 的团队决定发布一个巨大的版本,我将介绍他们在上一个版本中包含的许多非常棒的功能中的一个:

### 动态路线

所以你可能记得对于 nextjs 中的动态路由，你通常需要[创建一个定制服务器](https://dev.to/eperedo/learning-nextjs-day-8-11)。这听起来很难做到，但实际上很简单。

nextjs 团队意识到在真实的 web 应用程序中这是一个常见的任务，所以他们决定将这个特性构建到 nextjs 的核心中。

让我们用 3 条路线创建一个新的 nextjs 应用程序:

1.  一个主页，没什么特别的。

```
/ 
```

1.  一个用户页面，它将显示一个用户列表

```
/users 
```

1.  显示一个用户信息的用户资料页面

```
/users/{user-id} 
```

这个版本的安装过程没有什么新的东西，和以前的
一样

```
npm install next@9.0.1 react react-dom 
```

现在让我们创建我们的 pages 文件夹，在它里面有下面的结构来实现我们的 3 条路线

```
pages
  index.js # this is going to generate the home url
  users index.js # this is going to generate the users url (/users)
    [id].js # Square brackets means dynamic parameters for nextjs, it is going to # generate the url: /users/{id} 
```

是的，那容易吗！如果你想创建一个动态 url，你需要在方括号中定义动态部分，你放入的名字将被添加到你的 **getInitialProps** 方法中的查询对象中。

好了，让我们为每个页面创建组件。如果你不熟悉 nextjs 页面，你可以在这里看到[的介绍](https://dev.to/eperedo/learning-next-day-1-1pdf)

```
// pages/index.js
import Link from 'next/link';

function Home() {
  return (
   <div>
     <h1>Welcome to NextJs 9!</h1>
      <Link href="/users">
        <a>My Users</a>
      </Link>
   </div>
  );
}

export default Home; 
```

这里没有什么新东西，只有一条欢迎信息和一个到用户页面的链接

```
import Link from 'next/link';

const users = [
  {
    id: 1,
    name: 'Eduardo',
  },
  {
    id: 2,
    name: 'Cinthya',
  },
];

function Users() {
  return (
    <ul>
      {users.map(user => {
        return (
          <li key={user.id}>
            <Link href={`/users/${user.id}`}>
              <a>
                {user.name}
              </a>
            </Link>
          </li>
        );
      })}
    </ul>
  );
}

export default Users; 
```

我使用一组用户来缩短代码，但是你完全可以从 API 或任何你想去的地方获取数据。

现在我们的动态页面

```
function UserProfile({ user }) {
  return (
    <h1>
      Hello! My ID is {user.id}
    </h1>
  );
}

async function getInitialProps({ query }) {
  // Since our file name is [id].js
  // nextjs is going to add the value of
  const { id } = query;
  return {
    user: {
      id,
    },
  };
}

UserProfile.getInitialProps = getInitialProps;

export default UserProfile; 
```

实现与拥有一个定制的服务器是一样的，所以 nextjs 团队在没有重大改变的情况下添加了很棒的特性，这是值得称赞的！

### 多个动态参数

好的，但是如果你想让动态轮廓更复杂一点，比如说

```
/users/{id} 
```

你要这个:

```
/users/{id}/{name}/profile 
```

这对 nextjs 来说不是一个挑战，因为新的文件系统 API 也支持文件夹中的多个参数。

您可以使用此文件夹结构
创建上述路线

```
pages
  users
    [id]
      [name]
        profile.js 
```

现在我们的**查询**对象将有两个属性: **id** 和**姓名**

```
// /users/[id]/[name]/profile.js

function UserProfile({ user }) {
  return (
    <h1>
      Hello! My ID is {user.id}, but you can call me {user.name}
    </h1>
  );
}

async function getInitialProps({ query }) {
  const { id, name } = query;
  return {
    user: {
      id,
      name,
    },
  };
}

UserProfile.getInitialProps = getInitialProps;

export default UserProfile; 
```

当然，为了得到一个完整的例子，我们需要改变我们的用户页面中的属性**href**

```
// users/index.js
import Link from 'next/link';

const users = [
  {
    id: 1,
    name: 'Eduardo',
  },
  {
    id: 2,
    name: 'Cinthya',
  },
];

function Users() {
  return (
    <ul>
      {users.map(user => {
        return (
          <li key={user.id}>
            <Link href={`/users/${user.id}/{user.name}/profile`}>
              <a>
                {user.name}
              </a>
            </Link>
          </li>
        );
      })}
    </ul>
  );
}

export default Users; 
```

### 我们学到的东西

1.  您可以使用文件系统 API 创建动态路由。

2.  您可以在文件夹和文件中使用方括号将多个参数应用于动态路线。

3.  NextJs 太牛了！