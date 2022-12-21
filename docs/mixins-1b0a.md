# 反应中的混合

> 原文：<https://dev.to/fosteman/mixins-1b0a>

“我如何在几个组件之间共享代码？”是人们学习 React 时最先问的问题之一。

React 提倡使用组件组合进行代码重用:
“你可以定义一个组件，并在其他几个组件中使用它。”- [丹·阿布拉莫夫，2016 年 7 月 13 日](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)。然而，在 React 中，[组件组合](https://reactjs.org/docs/composition-vs-inheritance.html)并不总是解决特定模式的自然方式。

React 开发人员引入了“Mixin”系统作为采用新模式的中间阶段。

此后， [Vue.js](https://vuejs.org/) 、 [Angular](https://angular.io/) 等组件模型驱动框架完成了小生境。使用[组合而不是继承](https://i-can-not-react.netlify.com/component-composition/)的声明式 UI 已经不再新奇。

## Mixins 坏了！

阅读原文[博客文章](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)了解更多背景信息。
随着代码库的膨胀增长，多亏了受欢迎的 React 特性，*它*，代码库，倾向于回忆起空心菜。在某些严重的情况下，人们可能会嘲笑组件的可重用性，因为组件太容易损坏，紧密耦合出现，最初的父代(最初编写它们的开发人员)已经失去了对它们的接触。

## *混搭导致的常见问题*

Mixin 模式在面向对象、函数式范例中得到了成功的应用，但是在 React 中，它们造成了不必要的问题，原因如下

#### -隐含依赖关系

多亏了动态类型化的 JS，由 mixins 中定义的方法产生的依赖不会被强制记录。

因此，耦合是一个大问题。在一些其他组件引用这个假设定义的方法的情况下，人们不能确定它没有在其他地方定义。
与组件不同，mixins 被展平到相同的名称空间中。

有时，mixin 可能依赖于其他 mixin，因此依赖图变得不可读。

Mixins 不允许通过在组件文件中搜索状态键或方法来重构它。

##### 团队中的新手可能会发现需要对代码库做出详尽的贡献

#### -名称冲突

`handleChange()`是功能组件的典型方法名。就其本质而言，混合调用同一个名称空间中的方法，因此冲突并不罕见。

如果名称与来自第三方包 mixin 冲突，一个解决方案是重构通常不可读的名称以避免冲突。

新方法带来的功能也面临同样的问题。为了避免冲突，作者通常不知道整个名称空间以绝对确定正确命名

#### -滚雪球式的复杂

每当创建一个简单的 mixin 时，它通常会被大量配置以满足组件的需求。

强加在 mixin 上的每一个新的需求和特性都让它变得更加难以理解。

没有办法从 mixin 中只提取需要的代码。

##### 代码冗余，发生间接和更多的依赖。

### 脸书采用的替代模式

以下模式将用户从 Mixins 中迁移出来

#### 性能优化

为了防止[不必要的协调](https://reactjs.org/docs/advanced-performance.html#shouldcomponentupdate-in-action)，将使用`PureRenderMixin`

```
const PureRenderMixin = require(mixins)

const Display = React.createClass({
  mixins: [PureRenderMixin]
}) 
```

**解决方案**是在[生命周期方法](https://i-can-not-react.netlify.com/component-life-cycle/) `shouldComponentUpdate`
中直接使用`shallowCompare`函数

```
const shallowCompare = require('react-addons-shallow-compare')

const Display = React.createClass({
  shouldComponentUpdate: function(nextProps, nextState) {
    return shallowCompare(this, nextProps, nextState)
  }
}); 
```

**[另一个方案](https://github.com/facebook/react/pull/7195)** 是继承自`React.PureComponent`

#### 渲染逻辑

```
const HeaderMixin = {
  // Called by components from render()
  renderHeader: function() {
    return (
          <div>
            {this.getHeaderText() /* Implemented by derived components */}
          </div>
          )
  }
}
const HeaderAwake = React.createClass({
  mixins: [HeaderMixin],

  // Invoked in HeaderMixin.renderHeader()
  getHeaderText: function() {
    return this.props
  },

  render: function() {
    return (
      <div>
        {this.renderHeader() /* Implemented by HeaderMixin */}
      </div>
    )
  }
}) 
```

**解决方案**是提取带有定义 new 的组件，`<Header>`
组件，`getHeaderText()`作为属性传入。

```
const Header = props => (
    <div className='row-header'>
      {props.data}
    </div>
);

const UserRow = props => (
    <div>
      <Header text={props.data} />
    </div>
); 
```