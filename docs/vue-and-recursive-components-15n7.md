# Vue 和递归组件

> 原文：<https://dev.to/proticm/vue-and-recursive-components-15n7>

> 最初发布于[devinduct.com](https://devinduct.com/blogpost/32/vue-and-recursive-components)

## 快速介绍

有人说递归很难消化。在软件开发方面，我感觉不是。一个简单的定义是`recursive function`是一个自调用函数，这意味着它将在其执行的某一点调用自身。

一个更理论的定义是`recursion`是一个需要两个属性的行为:

*   一个**基本情况**——将停止递归的情况
*   一组**规则**负责将所有案例归入基本案例

我不能决定哪一个更重要。如果没有基本用例，递归将变成一个无限循环，然而如果没有减少用例，我们将无法实现想要的行为。无论如何，你需要两者来使它正常工作。

## 递归和 Vue 组件

在 Vue 中，递归是非常可能的，而且非常有用。我的意思是，不仅仅是在 Vue 中，我们可以遵循上面的规则在任何框架中实现递归行为。因此，根据给定的定义，我们可以说**递归组件是调用自身**的组件。

这个什么时候有用？好吧，每当你需要使用相同的模板结构，但是有层次的输入数据，你可以使用递归。例如，用于显示文件夹结构的树形视图、网站评论、嵌套菜单...所有父子结构相同的东西。

好了，让我们构建一个实例来展示所有这些在实践中的应用。

## 问题

想象一下，你像往常一样来上班，给自己冲了一杯好咖啡，开始阅读你最喜欢的博客。突然，你的老板来了，说你需要实现一个新的页面，在这个页面上你将显示所有的文件夹、子文件夹和文件，而不知道它们有多少。它可以显示 10，5 或 100 个文件夹。你开始挠头思考如何解决这个问题，当然，你会想到递归。

解决这个问题的最少组件数是 1，但在我们的示例中，我们将创建其中的两个:

*   *根*组件
*   *文件夹*组件

但是首先，我们需要创建样本数据。

## 数据

如前所述，当我们有分层组织的数据，其中子节点和父节点具有相同的结构时，递归就很方便了。下面是反映这一点的数据:

```
const root = {
  text: 'Root Folder',
  leaf: false,
  expanded: true,
  children: [{
    text: 'Sub Folder 1',
    leaf: false,
    expanded: false,
    children: [{
      text: 'Sub Sub Folder 1',
      leaf: false,
      expanded: false,
      children: [{
        text: 'SomeFile1.js',
        leaf: true
      }]
    }, {
      text: 'Sub Sub Folder 2',
      leaf: false,
      expanded: false,
      children: []
    }, {
      text: 'SomeFile.txt',
      leaf: true
    }]
  }]
} 
```

有了上面的数据，我们就可以创建组件了。

## 根构件

这个组件将是我们的文件夹树的起点。它将启动所有子元素的渲染，但是如果需要，它也可以显示一些独立的信息，因为它不是递归本身的一部分。

它将包含一个名为`folder`的属性，我们将把我们的`root`数据对象绑定到这个属性。该属性将被传递给子组件，子组件将基于它递归地创建文件夹树结构。

### 模板

```
<template>
  <ul class="folders">
    <li>Folders</li>
    <folder v-bind:folder="folder"></folder>
  </ul>
</template> 
```

### 代码

```
import Folder from './Folder.vue';

export default {
  name: 'root',
  props: {
    folder: Object
  },
  components: {
    Folder
  }
}; 
```

### 造型

```
ul.folders {
  padding: 1rem;
  margin: 0;
  box-sizing: border-box;
  width: 100%;
  list-style: none
}
ul.folders > li:first-child {
  padding: 1rem 1rem 1rem 0
} 
```

就这么简单。

## 文件夹组件

该组件负责呈现我们树中的每个文件夹。它将显示当前文件夹的信息，并呈现其子文件夹(如果有)。此外，文件夹是可点击的，通过点击一个，组件将显示其子文件夹和文件。

### 模板

```
<template>
  <li class="folder" v-bind:class="[folder.leaf ? 'is-leaf' : 'is-folder']">
    <span v-on:click="expand()">{{ folder.text }}</span>

    <ul class="sub-folders" v-if="folder.children && folder.children.length > 0" v-show="folder.expanded">
      <folder v-for="child in folder.children" v-bind:folder="child"></folder>
    </ul>
    <div class="folder-empty" v-else v-show="!folder.leaf && folder.expanded">No Data</div>
  </li>
</template> 
```

### 代码

```
export default {
  name: "folder",
  props: {
    folder: Object
  },
  methods: {
    expand() {
      if (this.folder.leaf) {
        return;
      }

      this.folder.expanded = !this.folder.expanded;
    }
  }
}; 
```

### 造型

```
li.is-folder {
  padding: 1rem;
  border-left: 1px solid #d3d3d3;
  margin-bottom: 0.5rem
}
li.is-folder > span {
  padding: 0.5rem;
  border: 1px solid #d3d3d3;
  cursor: pointer;
  display:inline-block
}
li.is-leaf {
  padding: 0 0 0 1rem;
  color: #000;
}
ul.sub-folders {
  padding: 1rem 1rem 0 0;
  margin: 0;
  box-sizing: border-box;
  width: 100%;
  list-style: none
}
div.folder-empty {
  padding: 1rem 1rem 0 1rem;
  color: #000;
  opacity: 0.5
} 
```

## 示例用法

为了使用您刚刚创建的组件，您需要做的就是在需要这个功能的地方导入`root`组件，并传递数据结构。例如，在你老板要求的页面上。`App.vue`组件看起来像这样:

### 模板

```
<template>
    <div class="vue-app">
        <root v-bind:folder="root"></root>
    </div>
</template> 
```

### 代码

```
import Root from './Root.vue';

export default {
  name: 'app',
  data: function () {
    return {
      root: {
        text: 'Root Folder',
        leaf: false,
        expanded: true,
        children: [{
          text: 'Sub Folder 1',
          leaf: false,
          expanded: false,
          children: [{
            text: 'Sub Sub Folder 1',
            leaf: false,
            expanded: false,
            children: [{
              text: 'SomeFile1.js',
              leaf: true
            }]
          }, {
            text: 'Sub Sub Folder 2',
            leaf: false,
            expanded: false,
            children: []
          }, {
            text: 'SomeFile.txt',
            leaf: true
          }]
         }]
        }
      }
    },
    components: {
      Root
    }
}; 
```

就是这样！您的页面已经准备好显示它接收到的所有文件夹。点击查看 codepen [上的真实例子。](https://codepen.io/protic_milos/pen/WVbMjX)

## 包装完毕

递归并没有看起来那么难。它只是用不同的输入参数一遍又一遍地执行相同的代码块，直到它到达基本情况。

我希望这篇文章能让你更好地理解递归，以及如何用 Vue 创建一个递归组件。

感谢您的阅读，下一篇文章再见。