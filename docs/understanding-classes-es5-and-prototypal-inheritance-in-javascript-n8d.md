# 理解 JavaScript 中的类(ES5)和原型继承

> 原文：<https://dev.to/_hridaysharma/understanding-classes-es5-and-prototypal-inheritance-in-javascript-n8d>

```
function Person(name, age) {
    this.name = name;
    this.age = age;
}

const me = new Person('Joe', 20);

console.log(me); // {name: 'Joe', age: 20} 
```

简单地说，上面的代码片段创建了一个可以有多个实例的 Person 类。按照惯例，javascript 中的函数类以大写字母开头。

### 我们来深入挖掘一下这到底是怎么回事？？一个普通的函数如何用于类？？🤔🤔

`Person`函数就像任何其他函数一样，当被直接调用时只返回`undefined`,因为我们没有显式地从它返回任何东西。但是真正的魔力在于第`var me = new Person('Joe', 20)`行的`new`关键词。
**让我们理解一下这种魔力——当我们使用`new`关键字初始化函数时，会发生几个步骤:**

1.  创建了一个空对象`{}`。
2.  `Person`通过将对象的引用传递给它来调用:`Person.call({}, 'Joe', 20)`
3.  `Person`内`this`现在指的是上一步传递的对象。
4.  使用`{}.__proto__ = Person.prototype.`将对象的原型设置为函数的原型
5.  最后返回对象，这就是我们进入`me`

*抛开概念*:JavaScript 中的每个函数都有一个可用的原型对象。`Array.prototype.map`就是这么用的。而且每个对象上面都有一个`__proto__`对象。进一步观看这个伟大的视频[发现 JavaScript](https://www.youtube.com/watch?v=ZUHyZHwZtUY) 。

因为`prototype`和`__proto__`指的是同一个对象，每当你在`prototype`上添加一个新函数时，它在所有实例上都可用。

```
Person.prototype.greet = function() {
  console.log('Hi', this.name);
}

me.greet(); // Hi Joe

const you = new Person('Alice', 22);
you.greet(); // Hi Alice 
```

到目前为止，我们还没有理解在 javascript 中如何创建类。让我们来了解一下如何在 javascript 中继承类。

**让我们创建一个名为 Employee 的新类，它继承了 Person 类**

```
function Employee(name, age, title) {
  Person.call(this, name, age);
  this.title = title;
}

// create Employee prototype from Person prototype
Employee.prototype = Object.create(Person.prototype);

const joe = new Employee('Joe', 22, 'Developer');
console.log(joe.name); // Joe
joe.greet(); // Hi Joe 
```

哇，我们终于继承了我们的 Person 类来创建一个 Employee 类，而且我们不必重写`greet`函数。

让我们看看刚刚发生了什么？？

1.  我们创建了我们的`Employee`类，就像我们创建`Person`类一样。
    *   在我们的 employee 类中，我们通过传递引用来调用 Person 类。这就像在 ES6 类中使用`super`关键字一样。
2.  这是*最重要的部分*。我们正在从*人的原型*中重新创建*雇员原型*，以访问`Person`类中所有可用的方法。

**现在你可能会问自己*为什么要使用`Object.create`而不仅仅是将人员原型分配给员工*。**
这是因为我们不希望 Person 和 Employee 共享同一个原型，因为 javascript 中的对象是被引用的。这就是继承 Person 的全部意义。

这就是我们如何在 javascript 中使用原型继承。新的 ES6 类基本上是它上面的一个语法糖。基本上这就是引擎盖下实际发生的事情。

PS:你可以在这里找到完整的代码 [GitHub Repo](https://github.com/vyasriday/js-snippets/blob/master/src/js-concepts/prototypalInheritance.js)