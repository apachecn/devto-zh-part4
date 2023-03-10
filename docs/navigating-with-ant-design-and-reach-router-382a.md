# 使用 Ant Design 和 Reach 路由器导航

> 原文：<https://dev.to/mikewheaton/navigating-with-ant-design-and-reach-router-382a>

[Ant Design](https://ant.design/docs/react/introduce) 是一个用 React 构建的开源设计系统。它提供了一组设计良好、功能强大且易于访问的组件来启动 React 项目。

对于站点范围的导航来说,`Menu`组件是优秀的，但是还不清楚如何用[到达路由器](https://reach.tech/router)来实现这一点。

经过一些试验，我能够让这些工作在一起:

```
<Location>
  {props => {
    return (
      <Menu selectedKeys={[props.location.pathname]}>
        <Menu.Item key="/courses">
          <Link to="/courses">Courses</Link>
        </Menu.Item>
        <Menu.Item key="/users">
          <Link to="/users">Users</Link>
        </Menu.Item>
        <Menu.Item key="/profile">
          <Link to="/profile">My Profile</Link>
        </Menu.Item>
      </Menu>
    );
  }}
</Location> 
```

使用 Reach Router，任何作为路由器直接子组件的组件都会收到一个描述用户在应用程序中当前位置的`location`属性。

在我的例子中，标题导航是页面布局组件的子组件。我没有将`location`作为道具传递，而是使用了`Location`组件。这提供了可以访问用户位置的子渲染属性。

Ant 的`Menu`组件接受任意数量的`Menu.Item`子组件。每一个都有一个`key`来唯一地标识它，而父菜单有`selectedKeys` prop，它接受一个应该被选择的项目的数组。

通过将每个菜单项的键设置为相应的路径名，可以很容易地通过将`selectedKeys` prop 传递给包含`props.location.pathname`的单元素数组来匹配它们。

简而言之，Reach Router 说“我们在/courses 上”，Ant 的菜单说“好的，我用/courses 的键来选择菜单项”。

这对于带有变量的路径(例如/courses/abc123/edit)来说会变得更加复杂，但是对于基本的导航来说，这已经足够了。