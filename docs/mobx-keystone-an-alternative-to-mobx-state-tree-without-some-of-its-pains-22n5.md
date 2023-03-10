# mobx-keystone 是 mobx-state-tree 的替代方案，没有它的一些麻烦

> 原文：<https://dev.to/xaviergonz/mobx-keystone-an-alternative-to-mobx-state-tree-without-some-of-its-pains-22n5>

你用过 [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree) 吗？

对于不知道的人来说，这是一个*牛逼的*国家管理图书馆。它提供了易变树状结构的易用性，可以自动生成结构共享的不可变快照、补丁、运行时类型验证、动作序列化和重放等等。所有这些款待使我成为他们的维护者和大力支持者之一(我现在仍然是！).

然而，没有什么是完美的，在两个大项目中使用它之后，我看到开发人员(包括我自己)有时会与它斗争，这就是我想称之为的痛点。

由于这些棘手问题是 [mobx-keystone](https://mobx-keystone.js.org/) 被创建的主要原因，我想展示一下它们，然后也展示一下所采用的替代解决方案。

## 痛苦#1:类型脚本支持

虽然它的类型脚本支持比过去好得多，但仍有一些领域没有得到解决(可能对于当前的类型脚本功能或没有 API 重新设计根本不可能)。

以自递归模型为例，比如树或交叉引用模型(相互引用的模型)。虽然这个库本身支持这种结构，但是如果不使用奇怪的技巧或者仅仅使用`any`，很难让 Typescript 支持这些结构。更不用说打字是如此复杂，以至于新的 Typescript 版本有破坏它们的风险(尽管修复确实很快)。

mobx-keystone 是按照“类型脚本优先”的思想创建的，如果不需要运行时类型检查，甚至不需要使用任何其他东西来声明类型。例如，适当地键入由自递归节点组成的树只是:

```
// self recursive model
@model(“myApp/TreeNode”)
class TreeNode extends Model({
  children: prop<TreeNode[]>(() => [])
}) {} 
```

交叉引用模型也很简单:

```
// cross-referenced models
@model("myApp/A")
class A extends Model({
  b: prop<B | undefined>()
}) {}

@model("myApp/B")
class B extends Model({
  a: prop<A | undefined>()
}) {} 
```

换句话说，当不使用运行时类型检查时，mobx-keystone 使用标准的 Typescript 类型注释来声明模型的数据，因此降低了学习曲线。但是，如果您需要运行时类型检查，mobx-keystone 还包括一个完全可选的类型定义/运行时类型检查系统。

## 难题#2:实例、输入快照、输出快照、强制转换…

在 mobx-state-tree 中，可以将快照分配给属性以及实际的实例，但是属性的实际类型是实例，这导致了令人困惑的转换和构造，例如:

```
// mobx-state-tree code

const Todo = types.model({
  done: false,
  text: types.string
})
.actions(self => ({
  setText(text: string) {
    self.text = text
  },
  setDone(done: boolean) {
    self.done = done
  }
}))

const RootStore = types.model({
  selected: types.maybe(Todo))
})
.actions(self => ({
  // note the usage of an intersection of the snapshot type
  // and the instance type
  setSelected(todo: SnapshotIn<typeof Todo> | Instance<typeof Todo>) {
    // note the usage of cast to indicate that it is ok to use
    // a snapshot when the property actually expects an instance
    self.selected = cast(todo)
  }
})) 
```

请注意`setSelected`动作实际上是如何将输入快照(普通 Javascript 对象)或实例(实例化的 mobx-state-tree 对象)作为输入的，加上一个强制转换以使 Typescript 与普通 Javascript 对象很好地相处，普通 Javascript 对象在赋值时被自动转换为实例。然后想象一下，必须向另一个不熟悉这项技术的开发人员解释这一点。

在 mobx-keystone 中，快照通常只在处理`getSnapshot`和`fromSnapshot`时出现，也就是说，只在实际处理序列化场景时出现。这导致更少的混淆和更明确的用法:

```
// mobx-keystone code
@model("myApp/Todo")
class Todo extends Model({
  done: prop(false),
  text: prop<string>(),
}) {
  @modelAction
  setText(text: string) {
    this.text = text
  }
  @modelAction
  setDone(done: boolean) {
    this.done = done
  }
}

@model("myApp/RootStore")
class RootStore extends Model({
  selected: prop<Todo | undefined>(undefined),
}) {
  @modelAction
  setSelected(todo: Todo | undefined) {
    this.selected = todo
  }
} 
```

## 痛苦#3:这个，自我，动作组块，观点组块…

当使用 mobx-state-tree 和 Typescript 时，为了获得正确的类型，需要从以前的“块”(动作、视图等)中获取代码。)必须使用`self`来访问，而同一“块”中的代码必须使用`this`来访问。

```
// mobx-state-tree code

const Todo = types
  .model({
    done: false,
    text: types.string,
    title: types.string,
  })
  .views(self => ({
    get asStr() {
      // here we use self since the properties
      // come from a previous chunk
      return `${self.text} is done? ${self.done}`
    },
    get asStrWithTitle() {
      // here we use this for asStr since it
      // comes from the current chunk
      return `${self.title} - ${this.asStr}`
    },
  })) 
```

在 mobx-keystone 中，`this`是访问当前实例的唯一方式，不需要人为地将动作块与视图块分开，加上可以使用标准的 mobx `computed`装饰器，使得从普通 mobx“类”的转换更容易掌握。

```
// mobx-keystone code

@model("myApp/Todo")
class Todo extends Model({
  done: prop(false),
  text: prop<string>(),
  title: prop<string>(),
}) {
  @computed
  get asStr() {
    return `${this.text} is done? ${this.done}`
  }
  @computed
  get asStrWithTitle() {
    return `${this.title} - ${this.asStr}`
  }
} 
```

## 痛苦#4:模型生命周期

mobx-state-tree 有几个生命周期挂钩(`afterCreate`、`afterAttach`、`beforeDetach`、`beforeCreate`)，当您认为它们应该触发时，它们可能会触发，也可能不会触发，这是由于节点的惰性初始化。

例如，您可以创建一个带有`afterCreate`钩子的子模型，但是它可能永远不会被实际执行，除非节点内容被访问(由于惰性初始化)。也许您可能想要设置一个效果(`reaction`或类似的东西)，但是您只希望这个效果在它真正成为您的应用程序状态的一部分之后才起作用。同样，你可能想要访问`getRoot`来访问根模型，但是它们可能实际上不会给出你所期望的值，直到它们被附加到一个最终(或者没有)附加到正确根的父模型。

mobx-keystone 通过只提供两个生命周期钩子来解决这个问题，`onInit`，它总是在模型被创建后被调用(因为没有惰性初始化，它们总是被调用)，和`onAttachedToRootStore`(加上一个可选的处理器，当它被分离时被执行)，它在模型被附加到适当的根节点(一个根存储)时被调用，从而确保在那个点`getRoot`将返回预期的值，并使它成为设置效果的理想位置。

## 痛苦之五:再来点

mobx-state-tree 中的引用被设计成对用户透明。可能太透明了，以至于无法获取引用节点的父节点。在 mobx-keystone 中，引用是显式的对象，因此使得这个用例变得微不足道。

动作中间件没有考虑到异步(mobx 行话中的流),这使得它们在这种情况下很难使用。mobx-keystone 确保在他们的中间件中,(异步)流和同步动作一样容易使用。

## 总结

本文无意以任何方式抨击 mobx-state-tree(再次强调，它太棒了！)，而只是为了暴露它的一些痛点。也许你对它非常满意，它非常适合你的应用程序。那也行！但是，如果你也感受到了其中的一些痛点，我建议你前往[https://mobx-keystone.js.org](https://mobx-keystone.js.org/)试一试！