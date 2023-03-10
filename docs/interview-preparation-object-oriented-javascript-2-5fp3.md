# 面试准备—面向对象的 JavaScript-2

> 原文：<https://dev.to/nabendu82/interview-preparation-object-oriented-javascript-2-5fp3>

欢迎来到本系列的第 11 部分和面向对象 JavaScript 问题的第二部分。

**问题 67-** *解释 Object.assign()方法*？
T5】答案-object . assign()方法用于将一个或多个源对象复制到目标对象中。

在下面的代码中，我们将两个对象“o2”和“o3”复制到“o1”。请注意，对象“o3”也发生了变化。

```
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, target object is changed. 
```

如果你想合并对象，我们在源中传递一个空对象。因为对象包含唯一的键，所以只有一个“a”、“b”和“c”会在那里。

```
var o1 = { a: 1, b: 1, c: 1 };
var o2 = { b: 2, c: 2 };
var o3 = { c: 3 };

var obj = Object.assign({}, o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 } 
```

如果我们考虑上一个问题(问题 66)中的例子，并使用 **Object.assign** 而不是 **Object.setPrototypeOf** ，我们会注意到“蝙蝠”行走()被“哺乳动物”覆盖。

```
let mammal =  {
  walk() {
    return 'walking!';
  },
  sleep() {
    return 'sleeping';
  }
};

let bat = {
  fly() {
    return 'Flying!';    
  },
  walk() {
    return 'Bat is walking';
  }
}

Object.assign(bat, mammal);

console.log(bat.walk()); //walking! 
```

Object.assign 的用途之一是在 react-redux 项目的 reducers 中。在还原器中，我们不能改变状态，所以我们可以用下面的方法克隆它。

```
let newState = Object.assign({}, state}; 
```

**问题 68-** *解释 JavaScript 中的函数 mixins*？
**回答-** 用编程的术语来说，Mixins 就是这些你可以和其他对象混合在一起扩展其功能的代码片段。

通过使用 mixins，您可以组合具有所需功能的对象。这与使用继承有很大的不同，当你想创建动态的千变万化的模型时，继承是非常死板的，而且大多数时候是行不通的。
举个例子，假设有一个人先学习“jsSkill”，成为“jsDeveloper”。然后他学习“reactSkills”，成为“reactDeveloper”。然后他学习“backendSkill”，成为“fullStackDeveloper”。
这可以用 mixins 很容易地完成，它使用*对象。分配*来组合对象或使用最新的*对象展开*。

```
const jsSkill = {
   knowsJS() {
    return true;
  }
};

const reactSkills = {
  knowsReact() {
    return true;
  } 
};

const backendSkill = {
  knowsNodeJS() {
    return true;
  } 
};

// Compose Objects
const jsDeveloper = Object.assign({}, jsSkill);
const reactDeveloper = Object.assign({}, jsSkill, reactSkills);
const fullStackDeveloper = {...jsSkill, ...reactSkills, ...backendSkill};

console.log(jsDeveloper.knowsJS());  //true
console.log(reactDeveloper.knowsReact()); //true
console.log(fullStackDeveloper.knowsNodeJS()); //true 
```

要理解**函数混合**，我们必须先理解工厂函数。在 JavaScript 中，有两种创建对象的方法，一种是使用“函数构造器/类”或“工厂函数”。当您使用常规函数构建对象时，它们被称为工厂函数。

```
//Function constructor
let Car = function(color) {
     this.color = color;
}

// New Object
let redCar = new Car('red');

// Factory Function
let Car = function(color) {
  return Object.assign({}, {
    color: color
  });
};

// New Object
let redCar = Car('red'); 
```

为了创建**函数混合**，我们将使用带有闭包的工厂函数。在工厂函数中有一个内部变量将保持闭包中的状态。状态将存储在使用该变量的每个函数中。

```
// Factory Function with closures
let Car = function(color) {
  let isMoving = false;
  return Object.assign({}, {
       color: color, 
       drive(){
          isMoving = true;
          return this;
       }, isMoving(){
          return isMoving;
     }
  });
};

// New Object
let redCar = Car('red');

console.log(redCar.drive().isMoving()); //true 
```

使用带有闭包的工厂函数，你可以构建**函数混合**。每个 mixin 将一个对象作为参数，并返回一个新的对象，该对象将接受传递的对象并用 mixin 的功能扩展它。
下面是一个使用两个 mixin**human factory**和 **coderFactory** 构建一个名为 **nabs** 的对象的例子。nabs 有人类的特征，所以他有行走的能力，也能编码。

```
// coder factory function
const coderFactory = function(obj) {
  // inner variable
  let coding = false;

// returns a new object
  return Object.assign({}, obj, {
    code() {
      coding = true;
      return this;
    }, isCoding() {
      return `${this.name} is ${(coding ? '' : 'not')} coding`;
    }
  });
};

// human factory function
const humanFactory = function(obj) {
  let walking = false;

return Object.assign({}, obj, {
    walk() {
      walking = true;
      return this;
    }, isWalking() {
      return `${this.name} is ${(walking ? '' : 'not')} walking`;
    }
  });
};

const nabenduBiswas = {
  name: 'Nabendu Biswas'
};

// compose an object
const nabs = humanFactory(coderFactory(nabenduBiswas));

// set the state
nabs.code().walk();

console.log(nabs.isWalking()); // prints: Nabendu Biswas is  walking
console.log(nabs.isCoding()); // prints: Nabendu Biswas is  coding 
```

**问题 69-** *解释 JavaScript 中的静态方法*？
**答-** 静态方法是在一个属于该类的类内部声明的方法，而一个类内部的普通方法是属于该类实例的实例方法。

静态方法不使用“this”，它们基本上被用作效用函数。如下例所示，我们通过在类前添加 static 关键字在类内创建静态方法。在这个例子中，我们使用静态方法来比较类的两个实例。

```
class Car {
  constructor(color, price) {
    this.color = color;
    this.price = price;
  }

  static comparePrice(car1, car2) {
    return Math.abs(car1.price - car2.price);
  }

  getColor() {
    return this.color;
  }
}

const maruti800 = new Car('silver', 350000);
const santro = new Car('red', 500000);

console.log(maruti800.getColor()); //silver
console.log(santro.getColor());  //red
console.log(Car.comparePrice(santro, maruti800)); //150000 
```

最近使用的静态方法之一是 React 16 中引入的新生命周期方法**static getDerivedStateFromProps()**。

**问题 70-** *讲解 JavaScript 装饰者*？
**回答-** 在最简单的形式中，装饰器只是用另一段代码包装一段代码的一种方式——字面意思是“装饰”它。
由于在 Angular 2+中的使用(比如@NgModule)，Decorators 变得流行起来。在 Angular 中，由于 TypeScript，decorators 是可用的，但在 JavaScript 中，它们目前是一个 [stage 2 提案](https://github.com/tc39/proposals#stage-2)，这意味着它们应该是该语言未来更新的一部分。

我们先来看下面一个 decorator 的例子。它有两个装饰器——**轮子**和**门**。注意特殊的“@”用在*级车*上。 [@wheels](https://dev.to/wheels) 装饰器用于将硬编码的**noow wheels**显示为 4。@doors 装饰器正在传递一个变量。

```
let wheels = function(target) {
  target.noOfWheels = 4;
}

let doors = function(number) {
 return function(target) {
   target.noOfDoors = number;
  }
}

[@wheels](http://twitter.com/wheels)
[@doors](http://twitter.com/doors)(2)
class Car {

}

console.log(`Wheels - ${Car.noOfWheels}`); //Wheels - 4
console.log(`Doors - ${Car.noOfDoors}`); //Doors - 2 
```

现在让我们看看完整的例子。我们可以看到现实生活中对装饰者的使用。这里我们有一个**只读**装饰器，用于使函数 **getColor** 只读。在下面的 jsfiddle 中，我们正在重写，**getColor * *在类外。如果我们从 **getColor** 中删除@** readOnly**，控制台日志*‘我会覆盖’*将会打印出来。

**问题 71-** *解释 JavaScript 中的多态性*？
**答-** 多态性是一个 OOP 概念，字面意思是“许多”“形式”。在传统的 OOP 语言中，它是通过函数重载来实现的，但是在 JavaScript 中，它是通过在继承中使用相同的函数来实现的。

考虑**问题 64** 中的例子。我们有三个*汽车、玩具汽车和玩具变形金刚*的实例。在调用 print()时，我们获得各自的控制台日志。

```
// Car constructor
const Car = function() {};

// Set Car's prototype
Car.prototype = {
  print() {
    return 'This is a Car';
  }
};

// ToyCar constructor
const ToyCar = function() {};

// Set ToyCar's prototype to be Car's prototype
ToyCar.prototype = Object.create(Car.prototype);

// Adding ToyCar's own print method
ToyCar.prototype.print = function(){
 return 'This is a ToyCar';
}

//ToyTransformer constructor
const ToyTransformer = function() {};

// Set ToyTransformer's prototype to be ToyCar's prototype
ToyTransformer.prototype = Object.create(ToyCar.prototype);

// Adding ToyCar's own print method
ToyTransformer.prototype.print = function(){
 return 'This is a ToyTransformer';
};

const toyota = new Car();
const legoCar = new ToyCar();
const bumbleBee = new ToyTransformer();

console.log(toyota.print()); //This is a Car
console.log(legoCar.print()); //This is a ToyCar
console.log(bumbleBee.print()); //This is a ToyTransformer 
```

这是本系列的第 11 部分，也是面向对象 JavaScript 问题的最后一部分。