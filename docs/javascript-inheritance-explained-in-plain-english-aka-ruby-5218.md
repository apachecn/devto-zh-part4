# 用简单的英语(又名 Ruby)解释 Javascript 继承

> 原文：<https://dev.to/npras/javascript-inheritance-explained-in-plain-english-aka-ruby-5218>

好了，别被标题气得太早走了。我这么说有两个原因:我的编程母语是 ruby。我通过与 ruby 进行比较来解释其他语言特性。Ruby 确实感觉像英国人🤷‍♂️.即使你不知道/不关心 ruby，你仍然可以从继承知识中受益。

很久以前，当我想了解 Javascript 基础知识时，我写了这篇文章。还有一个关于 [Javascript 原型](https://dev.to/npras/a-primer-about-javascript-s-prototype-1g85)的配套帖子。你可能也想检查一下，以便更好地理解基础知识。

所以，我们开始吧。

在 Ruby 中，我们有一流的语法支持来完成面向对象编程所需的几乎任何事情，甚至是编程技术的任何其他方面。我们有 procs、lambdas、继承、包含或扩展模块的能力、类和对象概念等。这就是它吸引人的原因，因为我们拥有几乎所有我们想要的东西的简洁语法。

但是在 Javascript 中，这种情况非常少。没有定义类的特殊语法支持，也没有直接的继承支持。它所拥有的就是这些:一个定义良好的对象和功能实体，以及像原型、对象绑定、作用域和上下文这样可靠的概念。

(2019 更新:我们现在在 JS 中确实对类和继承有很大的支持。但只是[语法糖](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)。底层实现基于函数和原型。所以明白这一点会让你很酷。)

然而，有了这些最基本的功能，并且彻底掌握了该语言的优缺点，您几乎可以用 Javascript 做任何事情。面对新兴的前端框架和节点，是时候了解和掌握 Javascript 了。在这篇文章中，我们将看到如何通过并列继承 Ruby 来实现 Javascript 的继承。

### 继承

什么是面向对象编程中的继承？我可以用 3 个最小的测试来决定是否实现了继承。

*   子类型对象应该是子类型和子类型所继承的超类型的实例。
*   子类型对象应该继承父类型定义的属性。
*   子类型应该能够覆盖父类型中定义的属性。

我们将使用 Ruby 来看看这些想法的例子。

### 红宝石的传承

考虑特定品牌的汽车对象——现代 I20Asta。它可以具有特定于制造商的属性，如转向类型、发动机类型、专有节油技术等。但就其核心而言，它只是一辆拥有汽车所有一般属性的汽车，如车轮数量、传动技术、发动机类型等。所以我们可以从一个通用的 Car 对象继承一个 I20Asta 对象。

```
class Car
  def available_fuel_options
    %w(petrol diesel lpg)
  end

  def steering_type
    'manual'
  end
end

class I20Asta < Car
  attr_accessor :owner
  def initialize(owner)
    @owner = owner
  end

  def steering_type
    'power'
  end
end 
```

有了汽车和 i20asta 汽车的独立对象，我们可以测试上面描述的 3 个继承思想。

```
a_car = Car.new
john_car = I20Asta.new('John')

# 1\. subtype should be instance of supertype
john_car.is_a? I20Asta # true
john_car.is_a? Car # true

# 2\. subtype should inherit properties from supertype
john_car.available_fuel_options
# ['pertrol', 'diesel', 'lpg']
# Note that the I20Asta class doesn't define or override the available_fuel_options method.

# 3\. subtype should be able to override properties defined in supertype
a_car.steering_type # manual
john_car.steering_type # power 
```

现在让我们看看如何在 Javascript 中做同样的事情。

### Javascript 的继承

让我们首先为 Car 和 I20Asta 创建构造函数。只能从这些构造函数中创建对象。

```
function Car(){
  this.wheels = ['front', 'back'];
}

Car.prototype.available_fuel_options = function(){
return ['petrol', 'diesel', 'lpg']
};

Car.prototype.steering_type = function(){
return 'manual';
};

function I20Asta(owner){
  this.owner = owner;
}

I20Asta.prototype.steering_type = function(){
  return 'power';
}; 
```

(我们没有直接向构造函数添加属性，而是将它们添加到函数的原型对象中。这样，由这些函数创建的所有对象共享这些属性，而不是占用单独的内存空间。)

注意，我们还没有实现继承。从这些函数创建的对象之间没有任何类型的关联。

```
var a_car = new Car();
var john_car = new I20Asta('John');

console.log(john_car instanceof I20Asta); // true
console.log(john_car instanceof Car); // false. Inheritance not yet implemented. 
```

#### 关于原型物体的一段旁白

当我们向 Javascript 对象请求属性值时，它首先在对象中寻找属性的存在。如果它存在，那么它的值将被返回。如果它不存在，那么 Javascript 将持久化并向对象的构造函数的 prototype 对象请求该属性的值。只有当它不存在时，javascript 才会承认失败。

事实上那不是真的。如果该对象还引用了另一个原型对象，那么 javascript fill 会沿着这条线索向上，直到它获得值或者到达一个死胡同。

有了这个想法，我们现在可以通过操作原型对象引用，让`john_car`对象从汽车构造器继承属性。默认情况下，`john_car`对象将通过其`__proto__`属性引用其构造函数的原型。也正因为如此，上面的‘instance of’检查通过了。

```
john_car.__proto__ === I20Asta.prototype // true 
```

到目前为止，我们的 I20Asta 函数的原型除了一个构造函数属性和我们添加到其中的‘steering _ type’属性之外，什么都没有。考虑到我们需要遗产，这对我们来说没有用。为了能够继承，如果我们擦洗 I20Asta 当前的原型对象，使其指向另一个对象，会怎么样？特别是我们要继承的对象——车？让我们马上做那件事。

```
// the key line that enables inheritance
I20Asta.prototype = new Car(); 
```

魔法结束了。但是等等，因为我们删除了旧的原型对象，我们已经丢失了添加到它的 steering_type 方法。我们需要再次添加它。

```
I20Asta.prototype.steering_type = function(){
  return 'power';
}; 
```

*   现在我们的 john_car 对象可以访问所有这些:它自己的属性
*   在其构造函数的原型对象中添加的属性
*   在其超类型的原型对象中定义的属性

我们现在可以成功地测试 3 个继承思想了。

```
// Redefine the objects
var a_car = new Car();
var john_car = new I20Asta('John');

// 1\. subtype should be instance of supertype
john_car instanceof I20Asta;  //  true
john_car instanceof Car;  //  true

// 2\. subtype should inherit properties from supertype
john_car.available_fuel_options();  //  ['petrol', 'diesel', 'lpg']

// 3\. subtype should be able to override properties defined in supertype
I20Asta.prototype.available_fuel_options = function(){
  return ['petrol', 'diesel', 'lpg', 'electric']
};
a_car.available_fuel_options();  //  ['petrol', 'diesel', 'lpg']
john_car.available_fuel_options();  ['petrol', 'diesel', 'lpg', 'electric'] 
```

这种实现继承的方法叫做**“原型链接”**。

### 原型链接的缺点

通过原型链接继承，您不能从超类型继承单个引用类型属性。它将在所有对象之间共享。(Javascript 引用类型是对象、数组和用户定义的自定义对象，与原始值相反。引用这些项的变量并不拥有单独的内存，相反，它们只是充当指向引用类型的实际位置的指针。)

注意，在 Car 函数中，我们有一个 wheels 属性，它是一个数组。javascript 中的数组是一种引用类型。使用这个继承设置，尝试向`john_car`和`joe_car`(I 20 asta 的另一个实例)请求这个属性。

```
john_car = new I20Asta('John');
joe_car = new I20Asta('Joe');

john_car.wheels // ['front', 'back']
joe_car.wheels // ['front', 'back'] 
```

一切似乎都很好。至少看起来是这样。假设约翰在他的汽车侧面加了一个轮子。为了反映这一点，我们向他的 wheels 属性添加了另一个项目。

```
john_car.wheels.push('side');
john_car.wheels // ["front", "back", "side"] 
```

现在问 joe_car 要轮子。

```
joe_car.wheels // ["front", "back", "side"]

// (2019 update: I cringe at the examples. Please forgive me.) 
```

无意中，我们也更新了乔的车轮！这是不对的。乔没有要求增强。如前所述，这只会影响引用类型属性。但这足以阻止人们开始寻找其他继承方法。

### 组合继承模式=原型链接+构造函数窃取

那是一口。但是这是 javascript 中最流行的继承模式。它的核心是使用原型链接，但是在子类型构造函数中窃取超类型的构造函数来纠正上面讨论的问题。为了在上面的例子中实现这一点，您应该这样做:

```
function Car(){
  this.wheels = ['front', 'back'];
}

Car.prototype.available_fuel_options = function(){
  return ['petrol', 'diesel', 'lpg']
};

Car.prototype.steering_type = function(){
  return 'manual';
};

function I20Asta(owner){
  // NOTE: THIS IS THE CRUCIAL STEP. Calling the supertype's constructor enables access to its properties individually for the objects.
  Car.call(this);
  this.owner = owner;
}

I20Asta.prototype.steering_type = function(){
  return 'power';
};

// the key line that enables inheritance
I20Asta.prototype = new Car();

var john_car = new I20Asta('John');
var joe_car = new I20Asta('Joe'); 
```

上面讨论的所有 3 个继承测试在这里也适用。你可以测试一下。现在要轮子，试着操纵它们。

```
john_car.wheels // ["front", "back"]
joe_car.wheels // ["front", "back"]

// add a wheeel to john's car in the side
john_car.wheels.push('side')
john_car.wheels // ["front", "back", "side"]

// Joe's car's wheels remain unaffected by the above change! It works ma!
joe_car.wheels // ["front", "back"]

joe_car.wheels.push('top')  //  for whatever reason!
joe_car.wheels // ["front", "back", "top"]
john_car.wheels // ["front", "back", "side"] 
```

胜利！我们现在可以看到，使用这种模式，我们能够在 javascript 中实现完美的继承。现在去玩吧。世界是你的 Javascripty 牡蛎！