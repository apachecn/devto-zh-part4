# 带有作用域插槽的 Vue.js 组件组合

> 原文：<https://dev.to/fdietz/vue-js-component-composition-with-scoped-slots-3207>

在[之前的帖子](https://dev.to/fdietz/vue-js-component-composition-with-slots-36mf)中，我们研究了插槽并命名插槽，以一种非常灵活的方式组合我们的组件和内容。
有一个我们没有讨论过的问题。我们传递给 slot 的内容在父组件的上下文中，而不是在子组件的上下文中。这听起来很抽象，让我们构建一个示例组件并进一步研究这个问题！

## 物品清单示例

这种场景最典型的例子可能是 todo 列表，它为每个 todo 呈现一个带有名称的复选框。

<figure>![Example 1](img/5a381e70fe955f67e0bbf889ab35a591.png)

<figcaption>Example 1</figcaption>

</figure>

```
<div id="demo">
  <div class="list">
    <div v-for="item in listItems" key="item.id" class="list-item">
      <input type="checkbox" v-model="item.completed" class="list-item__checkbox" />
      {{item.name}}
    </div>
  </div>
</div> 
```

```
new Vue({ 
  el: '#demo',
  data: {
    listItems: [
      {id: 1, name: "item 1", completed: false},
      {id: 2, name: "item 2", completed: false},
      {id: 3, name: "item 3", completed: false}
    ]
  }
}); 
```

在下一步中，我们将代码重构为一个可重用的列表组件，我们的目标是让组件的客户端来决定呈现列表项的内容和方式。

## 重构为可重用列表组件

先说列表组件的实现:

```
Vue.component("List", {
  template: "#template-list",
  props: {
    items: {
      type: Array, 
      default: []
    }
  }
}); 
```

```
<template id="template-list">  
  <div class="list">
    <div v-for="item in items" class="list-item">
      <slot></slot>
    </div>
  </div>
</template> 
```

按照我们之前的例子，我们使用默认槽来呈现列表项。

现在使用我们的新组件:

```
<div id="demo">
  <List :items="listItems">
    <div class="list-item">
      <input type="checkbox" v-model="item.completed" class="list-item__checkbox" />
      <div class="list-item__title">{{item.name}}</div>
    </div>
  </List>
</div> 
```

但是，在尝试这个例子时，我们遇到了一个 Javascript 错误消息:

```
ReferenceError: item is not defined 
```

我们似乎无法从我们的位置内容访问`item`。事实上，我们传递的内容运行在父组件的上下文中，而不是子组件`List`的上下文中。

让我们通过使用 Vue 实例中定义的`listItems`数据打印`List`组件中的项目总数来验证这一点。

```
<div id="demo">
  <List :items="listItems">
    <div class="list-item">
      {{listItems}}
    </div>
  </List>
</div> 
```

这是因为我们运行在父组件的上下文中，在本例中是 Vue 实例。但是，我们如何将子节点`<List>`的`item`数据传递给我们的插槽呢？这就是“作用域插槽”的用处！

我们的组件必须将`item`作为道具传递给插槽本身:

```
<template id="template-list">  
  <div class="list">
    <div v-for="item in items" class="list-item">
      <slot :item="item"></slot>
    </div>
  </div>
</template> 
```

请注意，使用绑定`:item`而不是仅使用`item`来传递它是很重要的！

好吧，让我们再试一次:

```
<div id="demo">
  <List :items="listItems">
    <div slot-scope="slotProps" class="list-item">
      <input type="checkbox" v-model="slotProps.item.completed" class="list-item__checkbox" />
      <div class="list-item__title">{{slotProps.item.name}}</div>
    </div>
  </List>
</div> 
```

这一次我们使用了`slot-scope`属性，并将名称`slotProps`赋予它。在这个作用域槽中，我们可以访问通过这个`slotProps`变量传递的所有属性。

在 Vue.js 2.5.0+中，作用域不再局限于`<template>`元素，而是可以用在插槽中的任何元素或组件上。

## 扩展列表项的渲染

既然我们知道了如何传递数据，我们就可以在不改变列表组件的情况下，用一些新功能来扩展列表项了。如果我们能删除待办事项，那就太棒了！

首先，我们用一个方法来定义 Vue 应用程序，以删除 todo 项:

```
new Vue({ 
  el: '#demo',
  data: {
    listItems: [
      {id: 1, name: "item 1", completed: false},
      {id: 2, name: "item 2", completed: false},
      {id: 3, name: "item 3", completed: false}
    ]
  },
  methods: {
    remove(item) {
      this.listItems.splice(this.listItems.indexOf(item), 1);
    }
  }
}); 
```

我们使用 Javascript [splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) 函数从`listItems`中移除索引项。

接下来，我们在渲染列表项时使用这个方法:

```
<template slot-scope="slotProps" class="list-item">
  <input type="checkbox" v-model="slotProps.item.completed" class="list-item__checkbox" />
  <div class="list-item__title">{{slotProps.item.name}}</div>
  <button @click="remove(slotProps.item)" class="list-item__remove">×</button>
</template> 
```

我们添加一个带有`click`事件的按钮，该事件调用我们之前定义的`remove`函数。就是这样！

## 使用析构为`slot-scope`

我们可以通过在`slot-scope`属性上使用现代 Javascript 技巧来进一步简化这个模板。

下面是一个使用 Javascript“析构”来访问对象属性的例子:

```
const item = slotProps.item;
// same as 
const { item } = slotProps; 
```

我们现在可以直接访问`item`而不是使用值`slotProps`。

让我们在模板中使用它:

```
<template slot-scope="{item}" class="list-item">
  <input type="checkbox" v-model="item.completed" class="list-item__checkbox" />
  <div class="list-item__title">{{item.name}}</div>
  <button @click="remove(item)" class="list-item__remove">×</button>
</template> 
```

这更容易阅读，因为我们可以直接使用`item`变量，而不是总是通过`slotProps.item`。

## 总结

在本章中，我们使用了作用域槽来允许父节点访问子节点的数据。这给了我们许多以前不可能的新的可能性。在您希望将槽内容的呈现留给组件用户的情况下，此功能尤其有用。在我们的例子中，通过解耦列表项的呈现，列表组件是非常可重用的。

你可以在 Github 上找到完整的例子。

如果你喜欢这篇文章，也可以看看我的新课程 [Vue.js 组件模式课程](https://fdietz.github.io/vue-component-patterns-course.html)。

请继续关注我即将发布的关于无头组件的帖子！