# 使用 VueJS 的动态组件

> 原文：<https://dev.to/pikax/dynamic-components-using-vuejs-4b78>

首先，如果你刚刚开始使用 VueJS，这可能对你来说有点太高级了，我强烈建议阅读关于 VueJS 的文档，尤其是组件。

这是我的第一本指南，欢迎批评:)

# 前言

基于用户输入交换组件，或者甚至只是通过 json 文件设置一个表单，这确实是在一个大项目中保持你的理智水平较低的有用方法，因为通常有数百个组件/表单/页面等，所以在代码中进行更改可能会引起连锁反应，并破坏其他地方的一些东西。

由于 VueJS 将处理所有处理 DOM 的肮脏细节，我们可以专注于解决业务问题。

我将介绍按名称加载组件、动态创建组件和异步组件。

# 分量“神奇”

如果没有`<component v-bind:is="dynComponent"></component>`检查[动态&异步组件](https://vuejs.org/v2/guide/components-dynamic-async.html)以获得更多详细信息，做动态不会这么容易。
基本上，组件将呈现组件、异步功能或组件名称。

## 按名称加载组件

使用`<component/>`将允许你通过名字访问全局和本地组件。

[codepen](https://codepen.io/pikax/pen/aeNKop)

```
// add some different components globaly
Vue.component("test-comp0", {
  template: `<p>comp0</p>`
});

Vue.component("test-comp1", {
  template: `<p>comp1</p>`
});

Vue.component("test-comp2", {
  template: `<p>comp2</p>`
});

// sample app
new Vue({
  el: "#app",

  components: {
    // add a local component
    // check https://vuejs.org/v2/guide/components-registration.html#Component-Names
    TestComp3: {
      template: `<p>comp3 locally registered component</p>`
    }
    // you can also add some components from other files, using ES6 import or required.
  },

  data() {
    return {
      componentIndex: 0
    };
  },

  computed: {
    componentName() {
      return "test-comp" + this.componentIndex;
    }
  },

  template: `
      <div>
        Component: {{componentIndex}} <button @click="componentIndex=(++componentIndex)%4">change</button>
        <component :is="componentName"></component>
      </div>`
}); 
```

Enter fullscreen mode Exit fullscreen mode

组件之间的循环是有用的，但是在现实世界中你会传递一些道具给它。
要添加道具，让我们更改组件`test-comp0`和应用程序模板。

```
Vue.component("test-comp0", {
  props: ["name"], // please use the object props for production
  template: `<p>Hello {{name}}</p>`
});

...

// add name prop
<component :is="componentName" name="pikax"></component> 
```

Enter fullscreen mode Exit fullscreen mode

这将把正确的名称传递给每个组件。为了解决这个问题，我们可以有一个计算的属性并将它绑定到组件上。

```
// app becomes

new Vue({
  el: "#app",

  components: {
    // add a local component
    // check https://vuejs.org/v2/guide/components-registration.html#Component-Names
    TestComp3: {
      template: `<p>comp3 locally registered component</p>`
    }
    // you can also add some components from other files, using ES6 import or required.
  },

  data() {
    return {
      componentIndex: 0,
      name: "pikax"
    };
  },

  computed: {
    componentName() {
      return "test-comp" + this.componentIndex;
    },
    componentProps() {
      if (this.componentIndex == 0) {
        return {
          name: this.name
        };
      }
      return {}; // return empty object
    }
  },

  template: `
      <div>
        Component: {{componentIndex}} <button @click="componentIndex=(++componentIndex)%4">change</button>
        <component :is="componentName" v-bind="componentProps"></component>
      </div>`
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 即时组件

动态组件是我们使用 javascript 在需要时生成的组件，这显示了`<component></component>`的强大，一些用例场景将构建小部件。

我们可以根据用户输入生成和测试组件。

**注意**:要非常小心，这可能会让攻击者攻击您的应用程序，请确保来源可信！

[codepen](https://codepen.io/pikax/pen/NQNBPR)

```
new Vue({
  el: "#app",

  data() {
    return {
      componentDefinition: `{ template: "<div>Hello</div>" }`
    };
  },

  computed: {
    myComponent() {
      return eval(`(${this.componentDefinition})`);
    }
  },

  template: `<div>
    <p>Change me</p>
    <textarea v-model="componentDefinition" rows="4" cols="50"></textarea>
    <component v-if="myComponent" :is="myComponent"></component>
</div>
`
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，当您更改 textarea 时，组件应该立即呈现。
我不推荐使用这个，但我认为这是一个很好的例子来说明`<component></component>`是多么强大。

## 导入异步组件

对我来说，这是组件最有用的用例。强烈推荐阅读(官方指南)[[https://vue js . org/v2/guide/Components-dynamic-Async . html # Async-Components](https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components)]

```
Vue.component(
  "async-webpack-example",
  // The `import` function returns a Promise.
  () => import("./my-async-component")
); 
```

Enter fullscreen mode Exit fullscreen mode

### 现实世界的问题

在我的上一个项目中，我们遇到了一个问题，我们想收集用户信息，但字段会根据旅程的不同而变化，有些旅程需要电子邮件，有些需要电子邮件和电话。

解决方案是在一个 JSON 文件中获取旅程定义，每次用户开始一次旅程时，我们将加载该文件并加载字段。
策略是使用名称来动态加载组件，通过名称来加载组件，但是我们最终在`Vue.Component`中加载了所有可能的编辑器，这很有效...但是在启动时加载它们意味着启动时间和应用程序的大小比需要的要大得多。

### 解

混合使用[异步](https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components)、【动态】[【https://vuejs.org/v2/guide/components-dynamic-async.html】]组件和 Webpack。

```
// returning equivalent of webpack : import(name)
const getComponent = async path => {
  /* I recomend having an switch with the possible components you will load, this
   *   will allow you only load specific components.
   */
  if (path == 1) {
    return async () => {
      template: `<p>component 0</p>`;
    };
  } else {
    return async () => {
      template: `<p>${path}</p>`;
    };
  }
};

Vue.component("component-fallback", {
  template: `<div>This is not the component you're looking for</div>`
});

new Vue({
  el: "#app",
  data() {
    return {
      componentIndex: 0,
      component: "component-fallback"
    };
  },

  methods: {
    changeComponent() {
      const newIndex = ++this.componentIndex;
      this.loadComponent(newIndex);
    },

    // returns the component
    loadComponent(name) {
      const componentFunc = getComponent(name)
        .then(x => {
          this.component = x;
        })
        .catch(e => {
          this.component = "component-fallback";
        });
    }
  },

  template: `
        <div>
            Component: {{componentIndex}} <button @click="changeComponent">change</button>
            <component :is="component"></component>
        </div>
        `
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 结束

希望我的第一篇文章对你有用，我发现`<component></component>`是多么的强大和灵活。

如果你有更多关于`<component></component>`的用例，请在评论中告诉我。

`This story was first publish at medium.com`