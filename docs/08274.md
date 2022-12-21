# YDKJS —对象和原型—第 1 部分

> 原文：<https://dev.to/nabendu82/ydkjs-objects-and-prototypes-part1-1jml>

欢迎来到你不知道的 JS 第二辑。本系列的第一部分是关于作用域和闭包的。

这个系列全部来自于 **Kyle Simpson** 的学习，也来自于 **Kaushik Kothagul** 来自 [Javabrains](https://www.youtube.com/user/koushks/playlists?view=50&shelf_id=1&sort=dd) 的令人惊叹的 youtube Javascript 系列，它也受到了**你不知道的 JS** 的影响。

让我们从使用 object 创建一个简单的函数来创建 employee 对象开始。

```
 function createEmpObj(fName, lName, gender, desig) {
      var newObj = {};
      newObj.firstName = fName;
      newObj.lastName = lName;
      newObj.gender = gender;
      newObj.designation = desig;
      return newObj;  
    }

    var emp1 = createEmpObj("Parag", "Khandar", "M", "Project Manager"); 
```

> 现在考虑行**var new obj = { }；**和**返回 newObj**它们在我们创建的每个功能中都是一样的。所以，JS 给了我们一个特殊类型的函数，称为**构造函数**来创建它们。

我们将重构上面的代码以使用**构造函数**。看看下面的代码。

```
 function createEmpObj(fName, lName, gender, desig) {
      //var this;
      this.firstName = fName;
      this.lastName = lName;
      this.gender = gender;
      this.designation = desig;
      //return this;  
    }

    var emp2 = new createEmpObj("Shikha", "Biswas", "F", "Developer"); 
```

我们添加关键字 **new** 来创建对象。它主要负责 newObj 的创建和返回。但是给它起了一个新名字**这个**。
注意，我们不需要把这两行放在我们的代码中，由 JS 引擎自动放置。

让我们来看看在 Javascript 中执行函数的四种不同方式。

```
 function foo() {
      console.log("Hello");
    }

    foo();  //Method #1

    var obj = {};

    obj.foo = function() {
      console.log("Hello");
    }

    obj.foo() //Method #2

    new foo() //Method #3 
```

方法#1 是执行函数的明显方式。在方法#2 中，我们有一个对象，该对象具有属性 foo。属性 foo 具有作为匿名函数的值。所以，obj.foo()可以用来调用函数。
我们刚刚看到的方法#3，被调用构造函数并添加了 this，当用 new 关键字调用时。

我们将在下一部分讨论方法 4。