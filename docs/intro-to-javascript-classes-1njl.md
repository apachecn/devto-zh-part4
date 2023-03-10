# JavaScript 类简介

> 原文：<https://dev.to/jasterix/intro-to-javascript-classes-1njl>

### 类用于创建对象:

*   类别

#### 词汇

*   实例属性= >它们有什么实例方法= >它们做什么
*   constructor = >创建对象时运行一次的方法——用于设置对象
*   `this` keyword = >指由类创建的对象(实例)

### 创建一个类:

```
class Rectangle {
  constructor (_width, _height) {
    console.log("Creating a rectangle")
    this.width = _width (instance property)
    this.height = _height (instance property)
 ,, }
  getArea () (instance method) {
     return this.width * this.height
    }

  printDescription() (instance method) {
    console.log(`I am rectangle a of ${this.width} x ${this.height} }
} 
```

Enter fullscreen mode Exit fullscreen mode

###### 试试看:

```
let myRectangle1 = new Rectangle(2, 6)
let myRectangle2 = new Rectangle(10, 6)

console.log(myRectangle1.getArea())
myRectangle2.printDescription() 
```

Enter fullscreen mode Exit fullscreen mode

### getter 和 Setters

#### 吸气器

*   用于在类上定义方法，然后像使用属性一样使用这些方法

#### 设定者

*   为实例属性指定新值

```
class Square {
  constructor (_width) {
    this.width =_width
    this.height = _width
    }

  get area() {
    return this.width * this.height
  }

  set area(area) {
    this.width = Math.sqrt(area)
    this.height = this.width
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

###### 试试看:

```
let square1 = new Square(4)
console.log(square1.area)
square1.area = 25 //modifies the value of square sides to 5
console.log(sqare1.width) 
```

Enter fullscreen mode Exit fullscreen mode

### 静态法

*   可以在类上定义的方法，但不需要创建该类的实例即可使用
*   在 square 类本身上调用静态方法。也称为帮助器方法
*   没有绑定到它们的对象
*   不使用`this`关键字

```
class Square {
  constructor (_width) {
    this.width = _width
    this.height = _height
  }

  static equals (a, b) {
    return a.width * a.height === b.width * b.height
  }

  static isValidDemensions (width, height) {
    return width === height
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

###### 试试看:

```
let square1 = new Square(8)
let square2 = new Square(9)

console.log(square1, square2)
console.log(Square.isValidDimensions(7,6)) 
```

Enter fullscreen mode Exit fullscreen mode

### 继承与扩展

*   使用`extend`关键字
*   父类用于创建新的子类
*   子类接受父类的属性和行为。它也有自己的自定义行为

```
class Person {
  constructor (_name, _age) {
    this.name = _name
    this.age = _age
  }

  describe() {
    console.log(`I am ${this.name} and I am $this.age} years old.`

  }
}

class Programmer extends Person { //child class Programmer of Person
  constructor (_name, _age, _yearsOfExperience) {
    super(_name, _age) //calls the properties from the parent class)

    //custom behavior
    this.yearsOfExperience = _yearsOfExperience
  }

  code () {
    console.log(`${this.name} is coding`)
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 试试看:

```
let p1 = new Person("Sheva", 45)
let programmer1 = new Programmer("Ayana", 56, 12)

const programmers = [
new Programmer("Frank", 62, 5),
new Programmer("Jane", 25, 4
];

function developSoftware (programmers) {
  for (let programmer of programmers) {
    programmer.code()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 多态性

*   在父类的派生类中重新定义方法的行为
*   当您用子类中的方法替代父类中的方法时

```
class Animal {
  constructor(name){
    this.name = name
  }
  makeSound () {
    console.log("Generic sounds")
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name)
  }
  makeSound() {
    super.makeSound() //calls the parent class first, then child class
    console.log("Woof!")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const a1 = new Animal("Dom")
a1.makeSound()

const a2 = new Dog("Jeff")
a2.makeSound() 
```

Enter fullscreen mode Exit fullscreen mode

### HTML 列表绑定

*   创建一个可以直接操作 DOM 的类

```
<ul id = "myList">
//<-----JavaScript List will go here----->
</ul>

<script src = "ListBinding.js"> </script>

<script type = "text/javascript">
const myList = document.getElementById("myList");
const listBinding = new ListBinding(myList);
</script> 
```

Enter fullscreen mode Exit fullscreen mode

###### 列表绑定. js

```
class ListBinding {
  constructor (element) {
    this.listElement = element;
    this.textList = ["abc", "is as easy","as 123" ]
  }

  //Makes an <li>text</li> element/tag

  static createListItem (text) {
  const li = document.createElement("li");
    li.textContent = text;
    return li;

  update() {
  // 1---- remove all existing <li> tags
    while (this.listElement.firstChild) {
      this.listElement.removeChild(this.listElement.firstChild)
    }

  // 2---- Fill <ul> tag with <li>
    for (const text of this.textList) {
      this.listElement.appendChild(ListBinding.createListItem(text))
    }
  }
}

  add (text) {
    this.textList.push(text)
    this.update()
  }

  remove (index) {
    this.textList.splice(index, 1);
    this.update();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Freecodecamp.org 视频教程我用过:
[https://www.youtube.com/embed/2ZphE5HcQPQ](https://www.youtube.com/embed/2ZphE5HcQPQ)
【https://youtu.be/2ZphE5HcQPQ】T4