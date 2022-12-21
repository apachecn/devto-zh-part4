# 你必须理解委托原型的两个原因

> 原文：<https://dev.to/jsmanifest/2-reasons-why-you-must-understand-delegate-prototypes-221n>

在[媒体](https://medium.com/@jsmanifest)上找到我

我正在读一本关于 JavaScript 的书，我遇到了一个问题(也是这个问题产生的概念的力量),我想写下来，特别是对于 JavaScript 的新手。即使您不是新手，也有可能不知道 JavaScript 中的这个问题。

本文将讨论一个已知的具有委托原型的反模式。对于 React 的用户来说，这种反模式的概念可能更加熟悉。但是我们还将讨论如何使用这个概念来扭转局面，并极大地提高应用程序的性能，正如你今天在大多数 JavaScript 库中看到的那样！

因此，如果你想用 JavaScript 创建一个库或者有任何计划，我强烈建议你了解如何优化你的应用程序，如果你还不了解它们，你可以通过了解如何利用委托原型来提高你的应用程序的性能。它有一个名字叫做 [Flyweight 模式](https://www.dofactory.com/javascript/flyweight-design-pattern)，将在本文中解释。

如果你不知道什么是原型，所有的原型基本上都是 JavaScript 用来建模其他对象的对象。你可以说它在构造对象的多个实例方面类似于类，但是它本身也是一个对象。

在 JavaScript 中，所有对象都有一些对委托原型的内部引用。当通过属性或方法查询对象时，JavaScript 首先检查当前对象，如果当前对象不存在，则继续检查对象的原型，其中*是委托原型*，然后继续处理该原型的原型，依此类推。当它到达原型链的末端时，最后一站在根`Object`原型处结束。创建对象会在根级别附加根`Object`原型。您可以用 Object.create()设置不同的直接原型来分支对象。

我们来看看下面的代码片段:

```
const makeSorceress = function(type) {
  return {
    type: type,
    hp: 100,
    setName(name) {
      this.name = name
    },
    name: '',
    castThunderstorm(target) {
      target.hp -= 90
    },
  }
}

const makeWarrior = function(type) {
  let battleCryInterval

  return {
    type: type,
    hp: 100,
    setName(name) {
      this.name = name
    },
    name: '',
    bash(target) {
      target.hp -= 10
      this.lastTargets.names.push(target.name)
    },
    battleCry() {
      this.hp += 60
      battleCryInterval = setInterval(() => {
        this.hp -= 1
      }, 1000)
      setTimeout(() => {
        if (battleCryInterval) {
          clearInterval(battleCryInterval)
        }
      }, 60000)
      return this
    },
    lastTargets: {
      names: [],
    },
  }
}

const knightWarrior = makeWarrior('knight')
const fireSorc = makeSorceress('fire')

const bob = Object.create(knightWarrior)
const joe = Object.create(knightWarrior)
const lucy = Object.create(fireSorc)

bob.setName('bob')
joe.setName('joe')
lucy.setName('lucy')

bob.bash(lucy) 
```

Enter fullscreen mode Exit fullscreen mode

我们有两个工厂函数，其中一个是`makeSorceress`，它接受一个女巫的`type`作为参数，并返回一个女巫能力的对象。另一个工厂函数是`makeWarrior`，它将一个战士的`type`作为参数，并返回一个战士能力的对象。

我们实例化了一个类型为`knight`的 warrior 类的新实例，以及一个类型为`fire`的女巫。

然后，我们使用`Object.create`为 bob、joe 和 lucy 创建新的对象，另外为每个对象委派原型对象。

Bob、joe 和 lucy 在实例中被设置了他们的名字，这样我们就可以声明并期待他们自己的属性。最后，鲍勃用`bash`攻击露西，降低她 10 点生命值。

乍一看，这个例子似乎没有什么问题。但是实际上有一个问题。我们希望 bob 和 joe 拥有他们自己的属性和方法副本，这就是我们使用`Object.create`的原因。当 bob 猛击 lucy 并将最后一个目标名称插入到`this.lastTargets.names`数组中时，该数组将包含新目标的名称。

我们可以把它注销，然后自己去看:

```
console.log(bob.lastTargets.names)
// result: ["lucy"] 
```

Enter fullscreen mode Exit fullscreen mode

这种行为是意料之中的，但是当我们*也为`joe`记录最后一个目标名字*时，我们会看到这个:

```
console.log(joe.lastTargets.names)
// result: ["lucy"] 
```

Enter fullscreen mode Exit fullscreen mode

这说不通吧？如上所述，攻击露西的人是鲍勃。但为什么乔明显参与了这一行为？那一行代码显式地写了`bob.bash(lucy)`，仅此而已。

所以问题是 bob 和 joe 实际上共享了*相同的状态*！

但是等等，这没有任何意义，因为当我们使用`Object.create`的时候，我们应该已经创建了他们自己单独的副本，或者我们是这样假设的。

甚至 MDN 上的[文档也明确说 Object.create()方法创建了一个*新对象*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 。它确实创建了一个新对象——它确实创建了一个新对象，但是这里的问题是，如果您在*原型*属性上变异对象或数组属性，变异将*泄漏*,并影响原型链上与该原型有某种链接的其他实例。如果您替换原型上的整个*属性，那么实例上只发生*的变化。**

例如:

```
const makeSorceress = function(type) {
  return {
    type: type,
    hp: 100,
    setName(name) {
      this.name = name
    },
    name: '',
    castThunderstorm(target) {
      target.hp -= 90
    },
  }
}

const makeWarrior = function(type) {
  let battleCryInterval

  return {
    type: type,
    hp: 100,
    setName(name) {
      this.name = name
    },
    name: '',
    bash(target) {
      target.hp -= 10
      this.lastTargets.names.push(target.name)
    },
    battleCry() {
      this.hp += 60
      battleCryInterval = setInterval(() => {
        this.hp -= 1
      }, 1000)
      setTimeout(() => {
        if (battleCryInterval) {
          clearInterval(battleCryInterval)
        }
      }, 60000)
      return this
    },
    lastTargets: {
      names: [],
    },
  }
}

const knightWarrior = makeWarrior('knight')
const fireSorc = makeSorceress('fire')

const bob = Object.create(knightWarrior)
const joe = Object.create(knightWarrior)
const lucy = Object.create(fireSorc)

bob.setName('bob')
joe.setName('joe')
lucy.setName('lucy')

bob.bash(lucy)
bob.lastTargets = {
  names: [],
}

console.log(bob.lastTargets.names) // result: []
console.log(joe.lastTargets.names) // result: ["lucy"] 
```

Enter fullscreen mode Exit fullscreen mode

如果您更改了`this.lastTargets.names`属性，它将反映到链接到原型的其他对象中。然而，当您更改原型的属性(`this.lastTargets`)时，它将只为那个实例覆盖那个属性*。对于一个新的开发人员来说，这可能有点难以理解。*

我们中一些经常使用 React 开发应用程序的人在管理整个应用程序的状态时经常会遇到这个问题。但是我们可能从来没有注意到这个概念是如何通过 JavaScript 语言本身产生的。所以更正式地来看，这是 JavaScript 语言本身的一个问题，它是一个反模式。

但这难道不是好事吗？

在某些方面，*可能是一件好事，因为你可以通过委托方法来优化你的应用程序，从而节省内存资源。毕竟，每个对象只需要*一个方法的副本*，方法可以在所有实例中共享，除非那个实例需要覆盖它以获得额外的功能。*

例如，让我们回头看看`makeWarrior`函数:

```
const makeWarrior = function(type) {
  let battleCryInterval

  return {
    type: type,
    hp: 100,
    setName(name) {
      this.name = name
    },
    name: '',
    bash(target) {
      target.hp -= 10
      this.lastTargets.names.push(target.name)
    },
    battleCry() {
      this.hp += 60
      battleCryInterval = setInterval(() => {
        this.hp -= 1
      }, 1000)
      setTimeout(() => {
        if (battleCryInterval) {
          clearInterval(battleCryInterval)
        }
      }, 60000)
      return this
    },
    lastTargets: {
      names: [],
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在所有原型中共享`battleCry`函数可能是安全的，因为它不依赖于任何条件来正确运行，除此之外，它需要一个已经在实例化时设置的`hp`属性。这个函数新创建的实例不一定需要自己的`battleCry`副本，而是可以委托给最初定义这个方法的原型对象。

在同一个原型的实例之间共享数据的反模式是，存储状态是最大的缺点，因为意外改变共享属性或不应该改变的数据会变得非常容易，这一直是 JavaScript 应用程序常见的错误来源。

如果我们看看流行的[请求](https://github.com/request/request)包是如何实例化[源代码](https://github.com/request/request/blob/master/lib/har.js) :
中的`Har`函数的，我们可以看到这种做法实际上有很好的理由

```
function Har(request) {
  this.request = request
}

Har.prototype.reducer = function(obj, pair) {
  // new property ?
  if (obj[pair.name] === undefined) {
    obj[pair.name] = pair.value
    return obj
  }

  // existing? convert to array
  var arr = [obj[pair.name], pair.value]

  obj[pair.name] = arr

  return obj
} 
```

Enter fullscreen mode Exit fullscreen mode

那么为什么`Har.prototype.reducer`不这样定义呢？

```
function Har(request) {
  this.request = request

  this.reducer = function(obj, pair) {
    // new property ?
    if (obj[pair.name] === undefined) {
      obj[pair.name] = pair.value
      return obj
    }

    // existing? convert to array
    var arr = [obj[pair.name], pair.value]

    obj[pair.name] = arr

    return obj
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，如果要实例化新的实例，实际上会降低应用程序的性能，因为这是[在每次实例化时重新创建新的方法]，这是`reducer`函数。

当我们有单独的`Har` :
实例时

```
const har1 = new Har(new Request())
const har2 = new Har(new Request())
const har3 = new Har(new Request())
const har4 = new Har(new Request())
const har5 = new Har(new Request()) 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上在内存中创建了`this.reducer`的 *5 个单独的副本，因为该方法是在实例级定义的。如果 reducer 是直接在原型上定义的，那么`Har`的多个实例将*将`reducer`函数委托给原型上定义的方法。这是一个如何利用委托原型并提高应用程序性能的例子。**

## 结论

这就是我想说的。我希望你从这篇文章中学到了一些东西，下次再见！

在[媒体](https://medium.com/@jsmanifest)上找到我