# JavaScript 原型的神奇世界

> 原文：<https://dev.to/ladybenko/the-magical-world-of-javascript-prototypes-1mhg>

我们听过多少次“JavaScript 不是面向对象的语言，它是面向原型的”？事实证明这并不准确。

这里有一些 JavaScript 中的对象，每一个都以不同的方式创建:

```
({} instanceof Object)
// => true

([] instanceof Object)
// => true

function Foo() {}
((new Foo) instanceof Object)
// => true 
```

所以我们在 JavaScript 中确实有对象。那么，原型呢？这是 JavaScript 实现其面向对象的机制。是的， **JavaScript it 是一种基于原型的面向对象语言**。

随着 ES6 类的到来，一些人可能认为不值得学习如何处理原型。这是不真实的，原因如下:

1.  ES6 类基本上是原型的语法糖。ES6“类”的实例仍然是基于原型的。

2.  世界上有一个巨大的 ES5(即没有类)代码库，你迟早会处理它。

有了这些，让我们学习一些关于 JavaScript 原型的知识，好吗？

* * *

原型只是嵌入在对象中的“特殊对象”。在 JavaScript 中，我们可以通过属性`__proto__` :
来访问它

```
const witch = { name: "Hermione" }
witch.__proto__
// => {} (empty prototype) 
```

它的特别之处在于，原型透明地充当了某种“代理”或“备份”。如果我们试图访问一个对象中不存在的属性，但是原型确实有，JavaScript 将返回原型的。继续前面的例子:

```
// add a property to the prototype
witch.__proto__.spells = { leviosa: "Wingardium leviosa" }

// the property is not defined by the object…
witch
// => { name: "Hermione" }

// …but we can access it thanks to the prototype!
witch.spells
// => { leviosa: "Wingardium leviosa" } 
```

这个有什么实际应用？**在对象之间共享代码**。在有类的面向对象语言中，类充当一个“模板”,在该类的所有实例中共享。在 JavaScript 中，没有“模板”:我们拥有的是一个共享的公共对象，即原型。

当我们使用**构造函数**实例化对象时，我们可以很容易地看到这一点。如果我们有一个`Wizard`函数，每次我们用`new Wizard()`创建一个新的对象，在属性`Wizard.prototype`中定义的东西被建立为新创建的实例的原型。

```
function Wizard(name) {
  this.name = name || "Anonymous"
}

Wizard.prototype.spells = {
  leviosa: "Wingardium leviosa",
  expelliarmus: "Expelliarmus",
  patronus: "Expecto patronum" 
}

const draco = new Wizard("Draco")
// => Wizard { name: "Draco" }
const hermione = new Wizard("Hermione")
// => Wizard { name: "Hermione" }

draco.spells === hermione.spells
// => true (both wizards share spells)
draco.__proto__ === hermione.__proto__
// => true (that's why they share prototypes)
hermione.__proto__ === Wizard.prototype
// => true (their prototype is defined in Wizard.prototype) 
```

共享这个公共对象(原型)的好处是:

*   以避免内存中的重复，因为原型由所有需要它的对象共享，而不是每个对象都有它的副本。
*   通过修改原型，能够一次性修改多个对象*。*

多亏了这个系统，我们还可以通过添加只有它们才具有的属性来修改特定的对象。如果此属性与原型中的属性同名，则直接包含在对象中的属性优先。例如，我们可以让霍格沃茨的一年级学生拿着一本空的魔法书:

```
const newbie = new Wizard("Lorem")
newbie.spells = {} // bypass what's in the prototype

newbie.spells === hermione.spells
// => false 
```

现在让我们想象一下，在巫师世界里已经有了一个巨大的发现，他们已经学会了按需变出真正的霍查塔。我们可以很容易地更新每个人的魔法书——只要它以前没有被覆盖过——只要简单地改变原型本身。

```
// add a new spell
Wizard.prototype.spells.horchata = "Send horchata"

// check Hermione's spellbook
hermione.spells
// => { leviosa: "Windgardium leviosa",
//   expelliarmus: "Expelliarmus",
//   patronus: "Expecto patronum",
//   horchata: "Send horchata" } 
```

这是一个非常强大的功能，但多亏了漫威，我们所有人现在都知道**有了强大的力量，就有了巨大的责任**。在 JavaScript 中更是如此，因为太容易对原型进行深度处理了。我们能走多远？嗯，我们甚至可以改变作为标准库一部分的对象的原型，比如`Object`、`Date`、`Array`……这里有一个简单的例子，我将其命名为“弗拉门卡戏法”:

```
Date.prototype.toString = () => "💃"
`${new Date()}`
// => 💃 
```

我希望你喜欢这个简短的 JavaScript 原型介绍。编码快乐！