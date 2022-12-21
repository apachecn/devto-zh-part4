# 学习 NextJs -第 4 天

> 原文：<https://dev.to/eperedo/learning-nextjs-day-4-l4c>

您可以使用 [styled-jsx](https://github.com/zeit/styled-jsx) 将 css 添加到您的页面组件中，因为这个库的创建者与 next 是相同的，所以您无需编写任何配置就可以使用它。

您将需要页面中特殊的**样式 jsx** 组件。

```
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
      <style jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
}

export default Home; 
```

这个页面将显示一个用户列表，每个用户的名字将是蓝色的，因为我们将这个颜色赋予了元素“T2”p“T3”。

style jsx 的一个优点是我们的 css 与外界隔离，在这种情况下,**世界**意味着其他页面/组件。所以其他的 **p** 元素将保持默认颜色。

### 全局样式

但是如果我们希望所有的 **p** 元素都有相同的颜色呢？简单，风格 jsx 给你一个特殊的**全局**道具。

```
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
      <style global jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
}

export default Home; 
```

### 动态样式

让我们假设你的经理发疯了，告诉你你的用户页面需要为每个用户设置一个按钮，如果用户的 ID 大于 5，按钮背景必须是**红色**否则**绿色**。

首先创建一个自定义按钮组件，接受 ID 属性，并据此编写一个简单的三元组，就可以发出疯狂的请求。

```
 function CustomButton({ children, id }) {
  return (
   <button>
     { children }
     <style jsx>{`
        button {
          background: ${id > 5 ? 'red' : 'green'};
        }
     `}</style>
   </button>
  )
} 
```

现在在你的主页中导入组件

```
 import CustomButtom from './../components/CustomButtom';

function Home({ users }) {
  return (
    <div>
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

export default Home; 
```

现在你有了使用道具值的动态样式。