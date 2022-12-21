# 如何将 VueX 数据映射到 Vue 模型

> 原文：<https://dev.to/aminnairi/how-to-map-your-vuex-data-to-vue-models-2o6l>

*封面图片取自[此处](https://vuex.vuejs.org/)T3。*

我真的很爱 Vue.js，也很崇拜 VueX。对我来说，这是 Flux 架构最简单也是最性感的实现，对于像我这样一个彻头彻尾的傻瓜来说。实际上，我是在使用 Elm 时才了解到 Flux 架构的，Elm 是另一个用完全不同的语言编写的框架，但是可以编译成 JavaScript。但是我以前使用 VueX 没有问题，这就是它对我如此强大的原因。

因此，VueX 非常擅长通过组件共享数据，而不必通过 props 实现父子数据传输的分层级联，这可能很难维护。

但是 VueX 并不完美，在某些情况下使用它会像以前一样乏味，尤其是在处理可以检索和更新的数据时。让我们构建一个简单的应用程序来说明这个想法。

我们的应用程序将是一个非常简单的网页，有一个向你问好的一级标题和一个输入你名字的地方。这是我们需要的。

## HTML

理想情况下，这是我们最终想要的。

```
<!doctype html>
<html>
  <body>
    <div id="app">
      <h1>Hi, {{ name }}</h1>
      <input type="text" v-model="name">
    </div>
    <script type="module" src="./index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我知道，这不是一个完全兼容的 HTML 页面，但它只是为了演示。如您所见，我们想要一个存储我们名字的`name`数据。一个输入对事件作出反应，通过替换存储在数据中的旧名称来设置我们输入的名称。非常简单。但是具有挑战性的是，在使用 VueX 存储&更新我们的名字时，使用完全相同的语法，不做任何改变。

想想在 Vue.js 中你会怎么做。如果你不知道怎么做，那么你来对地方了，这篇文章就是为你准备的！

## 商店

这是我们商店理想的样子。

```
import Vue from "./vue.js";
import VueX, { Store } from "./vuex.js";

Vue.use(VueX);

export default new Store({
  state: {
    name: "Jhon"
  },
  getters: {
    getName(state) {
      return state.name;
    }
  },
  mutations: {
    setName(state, newName) {
      state.name = newName;
    }
  },
  actions: {
    SET_NAME(context, newName) {
      context.commit("setName", newName);
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果我想检索我的名字，我想使用 getter `getName`，它将在商店中查找我们商店的`state`对象中的`name`属性商店。如果我想更新我的名字，我会用我刚刚在输入中输入的新名字触发一个名为`SET_NAME`的动作，最终会使用`setName`变异来更新状态中的`name`属性。

既然我们的商店已经设置好了，我们需要使用它。

```
import Vue from "./vue.js";
import { mapGetters, mapActions } from "./vuex.js";
import store from "./store.js";

new Vue({
  el: "#app",
  store,
  methods: {
    ...mapActions([ "SET_NAME" ]),
    ...mapGetters([ "getName" ])
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我定义了一个新的 Vue 应用程序，就像您定义自己的应用程序一样。我导入了我在应用程序中使用的商店，还从 VueX 导入了`mapGetters` & `mapActions`助手，以帮助我在应用程序中更轻松地重用我的 getter &动作。这相当于使用这些指令。

```
this.$store.state.name; // to access our name
this.$store.commit("setName", "JOhn DOE"); to update our name 
```

Enter fullscreen mode Exit fullscreen mode

但是不直接访问我们的状态并使用动作来提交我们的更改被认为是一个好的实践。我想在大多数情况下，这只是个人喜好的问题。

现在一切都准备好了，我们需要找到一个让我们的标记工作的解决方案。因为在实际设置中，我们的代码不会工作，Vue.js 实际上会抱怨，因为我们这里没有名为`name`的状态属性。

这就是事情变得有趣的地方。您实际上可以构造一个既是 getter 又是 setter 的对象。

让我们后退一步，用一个简单的例子来理解 getter/setter 属性是如何工作的。

```
"use strict";

const motorcycle = {
  horsepower: "45hp"
};

console.log(motorcycle.horsepower);
// 45hp

motorcycle.horsepower = 90;

console.log(motorcycle.horsepower);
// 90 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我想显示摩托车的马力，设置它并在它升级后再次显示。但是输出是不一致的，坦率地说，不是人类友好的。我们可以用来解决这个问题的一个方法是 getter/setter 技巧。

```
"use strict";

const motorcycle = {
  _horsepower: 45,
  get horsepower() {
    return `${this._horsepower}hp.`;
  },
  set horsepower(newHorsepower) {
    this._horsepower = newHorsepower;
  }
};

console.log(motorcycle.horsepower);
// 45hp.

motorcycle.horsepower = 90;

console.log(motorcycle.horsepower);
// 90hp. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一致的输出！这就是它们在普通 JavaScript 中的工作方式:每当我们想要显示属性时，相应的`get PROPERTY()`函数就会被触发，用相应的`set PROPERTY(newValue)`更新属性也是如此。在 Vue.js 中，它们是以一种特殊的方式实现的，但是当你看到如何使用它们时，你会很快感觉很舒服。让我们回到我们的代码，实现这个所谓的计算 getter/setter 属性。

```
import Vue from "./vue.js";
import { mapGetters, mapActions } from "./vuex.js";
import store from "./store.js";

new Vue({
  el: "#app",
  store,
  methods: {
    ...mapActions([ "SET_NAME" ]),
    ...mapGetters([ "getName" ])
  },
  computed: {
    name: {
      get() {
        return this.getName();
      },
      set(newName) {
        return this.SET_NAME(newName);
      }
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它有一点改变，但它确实类似于我们在 JavaScript 中所做的。我们有一个`name`属性，它对显示的&变化做出动态反应。我们使用了我们在商店中定义的东西，而不是自定义处理。就这么简单！为了记录，这里是我们的标记(未更改)。

```
<!doctype html>
<html>
  <body>
    <div id="app">
      <h1>Hi, {{ name }}</h1>
      <input type="text" v-model="name">
    </div>
    <script type="module" src="./index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的代码是 100%的工作，没有黑客需要。您过去在与您的商店打交道时可能做过类似的事情。

```
<!doctype html>
<html>
  <body>
    <div id="app">
      <h1>Hi, {{ name }}</h1>
      <input
        type="text"
        @input="$event = $store.commit('setName', $event.target.value)"
        :value="this.$store.state.name">
    </div>
    <script type="module" src="./index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

但是我认为我们都同意使用计算的 getter/setter 属性更酷更性感。这个双向计算属性的文档可以在[这里](https://vuex.vuejs.org/guide/forms.html#two-way-computed-property)找到。但我不得不写这篇文章，以确保每个人都了解这个功能背后的想法。另外，他们没有用`mapGetters` & `mapActions`做例子，所以这里是这样的。如果您没有使用它们，您应该使用它们！在官方文档中也有更多的信息。

这是所有的人。我希望你喜欢这个小把戏。自从我发现这个巧妙的技巧后，我现在在工作中和我的兼职项目中广泛使用它。