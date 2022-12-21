# 有趣的 Javascript 特性。

> 原文：<https://dev.to/spankie/interesting-javascript-features-74j>

多年来，javascript 不断发展，语言语法中增加了各种特性。有些语法看起来很奇怪，不为人知，所以这里是我在从事 javascript 工作的几年中发现的一些语法。

# 属性访问器

javascript 中访问对象属性的常规方式类似于其他类似 C 的语言，例如获取一个人对象的名字属性是`Person.firstname`。
访问这些属性的另一种方式是将属性视为地图的关键字，例如`Person['firstname']`。这种访问对象属性的方式不仅限于字段/属性，方法也可以这样访问。下面是一个使用访问方法的例子，比如 map 的 key。

```
 // create an array arr
    var arr = ["hello"];

    // access the push method of array prototype.
    arr["push"]("Spankie");

    console["log"](arr); // prints out: ["hello", "Spankie"] 
```

# Javascript 对象传播

在 javascript 中连接对象属性通常是通过`Object`原型方法`assign`来完成的，但是也有其他方法可以实现，这可以通过使用扩展语法`...`来完成。这里有一个例子:

```
 let obj1 = { name: "Spankie" };
let obj2 = { greeting: "Hello", greet: () => console.log(this.greeting), sayhello: function() {
        console.log(this.greeting);
}};

// concatenating obj2 properties into obj1...
let obj3 = {...obj1, ...obj2}
obj3.greet(); // prints out: undefined
obj3.sayhello(); // prints out: "Hello" 
```

# Javascript 对象解构

如前所述，获取对象属性有几种方式，另一种方式是通过对象解构。这是一种从对象中获取特定属性并将其赋给与该属性同名的变量的方法。例如，从一个`person`对象中检索一个`first_name`属性，并把它赋给一个变量名`first_name`，可以像这样轻松地完成；

```
const person = {first_name: "Spankie", last_name: "Dee"};
const { first_name } = person;
console.log(first_name); // prints out "Spankie"; 
```

### 重命名被解构的变量

```
const person = {first_name: "Spankie", last_name: "Dee", address: {
    street: "1 Main st.",
    city: "Lagos",
    country: "Nigeria"
}};
const { address: myaddress } = person;
console.log(myaddress); // prints out "{street: "1 Main st.", city: "Lagos", country: "Nigeria"}" 
```

### 解构嵌套的对象属性

```
const person = {first_name: "Spankie", last_name: "Dee", address: {
    street: "1 Main st.",
    city: "Lagos",
    country: "Nigeria"
}};
const { address: { city, country } } = person;
console.log(city, country); // prints out "Lagos Nigeria";
console.log(address); // Error: address is not defined. 
```

# 解析对象和字符串到 json

对模板文字使用`json.parse()`可能相当棘手...

```
 const a = "A girl has no face";
const b = {stark: "Winter is coming."};
console.log(JSON.parse(a));         // this would throw an unexpected token error
console.log(JSON.parse(b));         // this would throw an unexpected token error
console.log(JSON.parse(`${a}`));    // this would throw an unexpected token error
console.log(JSON.parse(`"${a}"`));  // this would log "A girl has no face"
console.log(JSON.parse(`"${b}"`));  // this would log "[object Object]"
console.log(JSON.parse(`${b}`));    // this would throw an unexpected token error 
```

在任何一种情况下都有效的是:

```
console.log(JSON.parse(JSON.stringify(a)));

// or

console.log(JSON.parse(JSON.stringify(b))); 
```

两者都会运行良好。

# JavaScript 中的 Setters 和 getters 对象访问器。

使用 getters 和 setters 方法访问对象 OOP 风格是非常流行的，javascript 也不例外，事实上在 javascript 中是如何做到的非常有趣。

可以使用对象中的关键字`get`和`set`为属性定义 Setters 和 getters。这里有一个例子说明如何做；

```
const vehicle = {
    name: "Volvo",
    year: "1999",
    // using getters and setter to set/get the year.
    get description() {
        return `This ${this.name} was made in ${this.year}`;
    },
    set description(desc) {
        var descs = desc.toString().split('  ');
        this.name = descs[0];
        this.year = descs[1];
    }
}

vehicle.description = "Mercedes 2018";
console.log(vehicle.description); // prints out "Mercedes was made in 2018" 
```

# 赋予可变对象属性

有时，您可能希望在不知道属性的确切名称的情况下为对象属性赋值，但该名称是另一个变量的值。这里有一个例子来解释这一点；

```
const carname = "volkswagen";
const caryear = 1937;

let CarsFounded = {
    "bmw": 1916,
    "mercedes benz": 1926,
    [carname]: caryear,
}

console.log(CarsFounded); // prints out: { bmw: 1916, 'mercedes benz': 1926, volkswagen: 1937 }
console.log(CarsFounded[carname]); // prints out: 1937 
```

感谢把这个看完:)。你可以在这里查看我的一些文章