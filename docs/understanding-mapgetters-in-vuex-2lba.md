# 谅解...Vuex 中的 mapGetters

> 原文：<https://dev.to/laurieontech/understanding-mapgetters-in-vuex-2lba>

在`vuex`中，有一个助手函数经常出现。

```
computed() {
    ...mapGetters(['currentUser'])
} 
```

Enter fullscreen mode Exit fullscreen mode

当我第一次使用它时，我有一些 babel 问题阻止我使用 ES6 语法，结果，我不能按预期使用该函数。使用变通方法帮助我确切地理解了这个小片段在做什么，所以我想我应该把它写下来。

事实证明，这里发生了一些事情。一个是`mapGetters`作为一个函数做什么，另一个是 spread 语法如何转换结果。让我们从函数本身开始。

### 什么是 mapGetters？

在我们的例子中，`mapGetters`的目的是将`store.getters.currentUser`的结果映射到计算属性`currentUser`。这允许我们像这样访问 getter 函数的结果。

```
<div>{{currentUser}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，`mapGetters`可以带一个对象或一个数组。上面显示的这个片段。

```
computed() {
    ...mapGetters(['currentUser'])
} 
```

Enter fullscreen mode Exit fullscreen mode

也可以是这个。

```
computed() {
    ...mapGetters({currentUser: 'currentUser'})
} 
```

Enter fullscreen mode Exit fullscreen mode

在第一种情况下，名称分配是隐式的，在第二种情况下，它是显式的。但这是唯一的区别。

现在我们知道了这个函数在做什么，让我们来分解它。

> 我总是赞成用多种不同的方式来理解概念。所以，我让我的同事读了这篇文章，他对`mapGetters`所做的解释是这样的。
> “我们的想法是在视图控制器中的 store 对象中获取一组变量，并将它们映射到一个顶级 getter 函数，您可以通过名称从 html 页面上的双向绑定中调用该函数？”

### 地图获取者

假设我们根本不能使用函数或助手语法。为了完成同样的事情，我们需要做什么？

我们希望调用 getter 函数，返回结果，并通过某种名称来访问该结果。所以我们会这样做。

```
computed() {
    currentUser() {
         return this.$store.getters.currentUser;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个函数，并在其中返回 getter 函数的结果。可以使用函数名来访问结果，如下例所示。

```
<div>{{currentUser}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我们使用的名称是计算函数的名称，而不是我们调用的 getter 方法的名称。举个例子，名字不一样。

```
computed() {
    otherName() {
         return this.$store.getters.currentUser;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了访问结果，我们将引用`otherName`。

```
<div>{{otherName}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

这种绑定很多来自 Vue。这里可以阅读更多[。](https://vuejs.org/v2/guide/computed.html)

### 深切

如果你想真正深入研究，这里有`mapGetters`的源代码。

```
/**
 * Reduce the code which written in Vue.js for getting the getters
 * @param {String} [namespace] - Module's namespace
 * @param {Object|Array} getters
 * @return {Object}
 */
export const mapGetters = normalizeNamespace((namespace, getters) => {
  const res = {}
  normalizeMap(getters).forEach(({ key, val }) => {
    // The namespace has been mutated by normalizeNamespace
    val = namespace + val
    res[key] = function mappedGetter() {
      if (
        namespace &&
        !getModuleByNamespace(this.$store, 'mapGetters', namespace)
      ) {
        return
      }
      if (
        process.env.NODE_ENV !== 'production' &&
        !(val in this.$store.getters)
      ) {
        console.error(`[vuex] unknown getter: ${val}`)
        return
      }
      return this.$store.getters[val]
    }
    // mark vuex getter for devtools
    res[key].vuex = true
  })
  return res
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里有很多，其中一些是为了处理对象和数组输入的差异，但是它的核心是在`mappedGetter`函数中。它接受返回对象，并使用给定的键，将相关 getter 函数的结果赋给该值。

### 倍数

现在我们已经理解了函数本身，让我们把注意力转向 spread 语法。理解`...mapGetters`的关键在于认识到这个函数是为同时处理多个 getter 调用而设计的。我们开始的单一用例仍然有效，但是让我们看看这个。

```
computed() {
    currentUser() {
         return this.$store.getters.currentUser;
    }
    otherThing() {
         return this.$store.getters.otherThing;
    }
    finalThing() {
         return this.$store.getters.finalThing;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们将获取 getter 函数的结果，并将它们赋给可以在组件内部引用的函数变量。这和我们上面做的是一样的，只是为了同时调用多个 getter。

这样做可以让我们呈现所有这些结果。

```
<div>{{currentUser}} {{otherThing}} {{finalThing}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在在 mapGetters 面前解释`...`更容易了。

### 传播语法

您可能熟悉 ES6 扩展语法。如果你想复习一下，我在这里写了一篇文章。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## Spread 运算符的 5 种用法

### 劳丽 1919 年 7 月 10 日 3 分钟阅读

#javascript #webdev #productivity #learning](/laurieontech/5-uses-for-the-spread-operator-b9i)

这是一个强大的语法，可以做许多不同的事情。但是让我们看看它在我们的例子中做了什么。

我们已经看到,`mapGetters`被设计用来替换多个函数调用。解析时，它返回一个由键值对组成的对象。每个键都是一个字符串名称，每个值都是相关 getter 函数调用的结果。所以实际上，我们只剩下这样的东西了。

```
computed() {
    ...{'currentUser': currentUser, 'otherThing': otherThing, 'finalThing': finalThing}
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意，这不是有效的 JavaScript！只是一个抽象。*

事实证明，这看起来非常像一个对象文字。spread 语法如何处理对象文字？它充当`Object.assign()`。

如果你不清楚那是什么意思，它实际上完成了以下任务。spread 语法选择对象内部的每个键值对，并将其作为独立对象取出。所以每个键都可以用来引用存储结果的值，就像我们之前看到的一样。

```
<div>{{currentUser}} {{otherThing}} {{finalThing}}</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 现在都在一起了

让我们从头开始看看我们的代码。

```
computed() {
    ...mapGetters(['currentUser', 'otherThing', 'finalThing'])
} 
```

Enter fullscreen mode Exit fullscreen mode

我们能看看现在发生了什么吗？数组中的每个字符串都由`mapGetters`处理。它采用同名的 getter 方法并调用它，然后将结果赋值为值，其中键是原始字符串。

`mapGetters`根据该公式返回一个包含一系列键值对的对象。然后，spread 语法将这些键值对中的每一个都提取出来，作为一个独立的对象，可以在模板代码中直接引用。这就是一切。

### 结论

太多了！您可能永远也不会使用 mapGetters。但理解它还是挺酷的，对吧？