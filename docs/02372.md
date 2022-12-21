# 关于 Javascript 原型的初级读本

> 原文：<https://dev.to/npras/a-primer-about-javascript-s-prototype-1g85>

听着，我知道你们都是很酷的人，使用最新的 ES6 和 es7 功能、课程、VR 和 AR 等等。你用 typescript，react，在 JS 文件里写 CSS 和 HTML。我，我老了(10 年的行业经验),我全职做 ruby。我试着跟上 JS 的趋势，但是因为我没有那么多时间玩这些，我总是落后。

但是我偶尔也要写 Javascript，有一次我不得不在很短的时间内学习 React，为一个客户运送两个项目。在那些时候，对我有帮助的是对基础知识的良好理解。我 3 年前写了这个关于原型的指南。但是如果我不得不做一些严肃的 JS，我仍然会参考它，它总是帮助我更快地开始新的主题。希望对你也有帮助。

它在这里...

如果你刚刚开始学习 javascript，清楚地理解 Javascript 原型的概念可能是至关重要的，但同时也是不可能的。所有流行的谷歌搜索结果都会让你看到来自专家口中的解释清楚的文章。如下面的链接所示。

*   [http://yehudakatz . com/2011/08/12/understanding-prototypes-in-JavaScript/](http://yehudakatz.com/2011/08/12/understanding-prototypes-in-javascript/)
*   [http://JavaScript sexy . com/OOP-in-JavaScript-what-you-need-to-know/](http://javascriptissexy.com/oop-in-javascript-what-you-need-to-know/)
*   [http://JavaScript sexy . com/JavaScript-prototype-in-plain-detailed-language/](http://javascriptissexy.com/javascript-prototype-in-plain-detailed-language/)
*   [http://javascriptissexy.com/javascript-objects-in-detail/](http://javascriptissexy.com/javascript-objects-in-detail/)
*   [https://developer . Mozilla . org/en/docs/Web/JavaScript/Inheritance _ and _ the _ prototype _ chain](https://developer.mozilla.org/en/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
*   [http://blog . plural sight . com/understanding-JavaScript-prototypes](http://blog.pluralsight.com/understanding-javascript-prototypes)

但我发现它们缺乏简单的解释。虽然你可以把下面的解释作为“*上帝关于原型*的最后一句话”，但我会把它当作“*向我解释，就像我是 5(用程序员的话来说)*一样”，然后继续检查其他信息来源，交叉检查并加深你已经形成的记忆槽。

那么，javascript 中的原型是什么？当一个函数被创建时，它会得到一个名为“原型”的特殊属性。它之所以特别，是因为当 javascript 必须执行某些操作时，它会在函数或对象中专门寻找一个名为“原型”的属性。目前，我建议你把“原型”仅仅看作一个词，而不是一个有相关含义的词。忘记原型的字典意义与原型这个词的联系。将其视为 js 对象的另一个属性。

### 一个函数的原型

所以，当一个函数被创建时，它会得到一个叫做“原型”的特殊属性，它只是一个对象。

```
function sayHi(){
return 'hi';
}
typeof sayHi.prototype; // 'object' 
```

你可以通过打开 Chrome 的 javascript 控制台或者如果你已经安装了 NodeJS 的话输入“node”来检查这篇文章中的所有例子。Javascript 对象有自己的属性。

所以如果一个函数的原型是一个对象，那么它也必须有一些属性，对吗？让我们找出答案。

使用方便的内置`Object.getOwnPropertyNames(object)`函数，我们可以获得一个对象所有自身属性的数组。让我们找出`sayHi`函数的原型对象的自身属性。
T3】

```
Object.getOwnPropertyNames(sayHi.prototype)
// ["constructor"] 
```

`sayHi`函数的原型对象有一个称为“构造函数”的属性。这是什么？让我们找出答案。

```
sayHi.prototype.constructor
// outputs:
// function sayHi(){
// return 'hi';
// } 
```

嗯。和我们的`sayHi`函数是一回事吗？让我们也找到它。

```
sayHi.prototype.constructor === sayHi
// true 
```

确实是的。一个函数的原型对象最初有一个称为“构造函数”的特殊属性，它简单地指向原始函数。生命的循环。

### 原型属性的用途(或为什么命名为原型)

当您从函数创建一个对象时，当您向对象询问任何属性的值时，javascript 首先在对象本身中寻找属性权限。如果找到，则返回属性值。如果没有，javascript 将会向函数的原型询问属性值，而不是简单地停止。考虑一下这个:

```
function Person(name, age){
  this.name = name;
  this.age = age;
  this.bio = function(){
    return "I'm " + this.name + ", and my age is " + this.age + ".";
  };
} 
```

```
var john = new Person('John', 23);
john.name // 'John'
john.age // 23
john.bio() // I'm John, and my age is 23. 
```

当我们向 john 对象请求“name”属性时，它返回“John ”,因为它自己有一个名为“name”的属性。同样地，我们可以问它的年龄，也可以问它的简历。

```
Object.getOwnPropertyNames(john);
// ["name", "age", "bio"] 
```

现在让我们添加一些附加属性，但不是添加到 john 对象或 Person 函数，而是添加到 Person 函数的 prototype 对象。

```
Person.prototype.species = "Sapiens"
Person.prototype.sayName = function(){
  return "Hi, I'm" + this.name;
}; 
```

我们甚至可以检查这两个属性现在只添加到了原型对象，而没有添加到通过 Person 创建的任何对象(例如:john object)。

```
Object.getOwnPropertyNames(Person.prototype);
// outputs:
// ['constructor', 'species', 'sayName']

Object.getOwnPropertyNames(john);
// still the same as before
// ["name", "age", "bio"] 
```

现在尝试向 john 对象请求一个“物种”属性，并让它说出自己的名字，这两者都不是 john 自己的道具。

```
john.species
// 'Sapiens'

john.sayName()
// "Hi, I'm John" 
```

如您所见，john 对象现在变得非常棒。当我们向它询问一个不属于它自己的属性时，它仍然会查找并询问其构造函数的原型对象，看它是否有这些属性，如果有，就返回它们的值。

你是否也注意到，约翰对象能够做到这一点，即使当物种和 sayName 道具被添加到原型中，*在*约翰对象创建之后？这是一种难以承受的热情。现在我们再做一件事。

让我们从 Person 函数创建另一个名为“Joe”的对象。

```
var joe = new Person('Joe', 35);
joe.name // 'Joe'
joe.age // 35 
```

让我们也问问乔他的种族，并请他说出他的名字。

```
joe.species // 'Sapiens'
joe.sayName() // "Hi, I'm Joe" 
```

由此我们可以推断，分配给原型对象的属性对于从该函数创建的所有对象都是可访问的。但是为什么呢？在函数本身中定义这些属性会有什么不同呢？

函数和对象是占据计算机内存中真实空间的真实事物。因此，当我们在函数中定义一个属性，并且创建一个对象时，内存是为对象中的这些属性分配的。即使约翰和乔都有相同的属性-姓名、年龄、生物-因为他们是不同的对象，这些属性也倾向于占据不同的空间。另一方面，原型上定义的属性——物种和 say name——它们不会被复制。在应用程序运行时的任何给定实例中，这些道具只占用一个内存空间。因此，当我们向 john 和 joe 请求这些属性时，javascript 只是从这个共享位置为他们两人获取值。

### 一个物体的原型

到目前为止，我们已经了解了函数的原型属性及其用途。一个对象也有原型属性吗？有点像。

一个对象的原型仅仅指向该对象的构造函数的原型。(刷新一下，对象的构造函数是用来创建对象的函数。约翰和乔的构造函数都是人函数。)但是它是不可访问的，因为 ecmascript 实现禁止它。但是浏览器和 nodejs 实现仍然通过 **proto** 属性公开对象的原型。

```
john.__proto__ === john.constructor.prototype
// true 
```

我们还可以用另外两种方法来检验这一点:

```
Object.getPrototypeOf(john) === Person.prototype
// true

Person.prototype.isPrototypeOf(john)
// true 
```

### 结论

原型既不是神秘的独角兽，也不是任何东西的“0 版”(字典内涵)。

它只是 javascript 函数的一个属性，最初有一个“构造函数”属性。原型对象的构造函数属性只是一个指向函数本身的指针。原型对象中定义的属性可由通过函数创建的所有对象访问。一个对象没有自己的原型。但是它的内部原型属性只是一个指向对象构造器原型的指针。