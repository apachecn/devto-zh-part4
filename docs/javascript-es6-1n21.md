# JavaScript ES6+

> 原文：<https://dev.to/meabed/javascript-es6-1n21>

我写这篇文章的时候，正在为我在[的](https://me.io)演讲做准备。IO JS meetup ，我认为分享它很好，因为它可以帮助或指导人们搜索或阅读它。

## JavaScript 历史:

*   1995 年:JavaScript 作为 LiveScript 诞生
*   1997 年:ECMAScript 标准建立
*   1999 年:ES3 问世，IE5 风靡一时
*   2000 年至 2005 年:XMLHttpRequest，又名 AJAX，在 Outlook Web Access (2000 年)和 Oddpost (2002 年)、Gmail (2004 年)和 Google Maps (2005 年)等应用程序中广受欢迎。
*   2009 年:ES5 推出了 forEach、Object.keys、Object.create 和标准 JSON
*   2015 年:ES6/ECMAScript2015 出来；它主要是句法上的糖，因为人们无法就任何更具突破性的事情达成一致
*   2016 年:ES7，2017 年:ES8，2018 年:ES9… ES。然后

### 要点代码链接:

*   [ES6 10 特性](http://bit.ly/es6-features-10)
*   [ES6 JS 自毁](http://bit.ly/es6-js-destruct)

## 10 ES6 特性:

我不会在这篇文章中介绍 JS ES6+的所有特性，我会介绍 IMO 的 10 大特性:)-请随意评论、讨论和建议更多。

##### 1。ES6 中的默认参数

```
// ES5
var link = function (height, color, url) {
    var height = height || 50
    var color = color || 'red'
    var url = url || 'http://google.com'
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var link = function(height = 50, color = 'red', url = 'http://google.com') {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 2。ES6 中的模板文字

```
// ES5
var name = 'Your name is ' + first + '  ' + last + '.'
var url = 'http://localhost:3000/api/messages/' + id 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var name = `Your name is ${first}  ${last}.`
var url = `http://localhost:3000/api/messages/${id}` 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 3。ES6 中的多行字符串

```
// ES5
var roadPoem = 'Then took the other, as just as fair,\n\t'
    + 'And having perhaps the better claim\n\t'
    + 'Because it was grassy and wanted wear,\n\t'
    + 'Though as for that the passing there\n\t'
    + 'Had worn them really about the same,\n\t'

var fourAgreements = 'You have the right to be you.\n\ You can only be you when you do your best.' 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var roadPoem = `Then took the other, as just as fair,
    And having perhaps the better claim
    Because it was grassy and wanted wear`

var fourAgreements = `You have the right to be you.
    You can only be you when you do your best.` 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 4。ES6 中的析构赋值

```
// ES5
var data = $('body').data() // data has properties house and mouse
var house = data.house
var mouse = data.mouse 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var {house, mouse} = $('body').data() // we'll get house and mouse variables 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES5
var jsonMiddleware = require('body-parser').json
var body = req.body, // body has username and password
  username = body.username,
  password = body.password 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var {json: jsonMiddleware} = require('body-parser')
var {username, password} = req.body 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
var [col1, col2]  = $('.column'),
  [line1, line2, line3, , line5] = file.split('\n') 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES6
const myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};
const { one, two, three } = myObject;
// one = 'a', two = 'b', three = 'c' 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
const myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};
const { one: first, two: second, three: third } = myObject;
// first = 'a', second = 'b', third = 'c' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 5。ES6 中增强的对象文字

```
// ES5
function getLaptop(make, model, year) {
    return { make: make, model: model, year: year }
}
getLaptop("Apple", "MacBook", "2015");// {make: "Apple", model: "MacBook", year: "2015"} 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6 
function getLaptop(make, model, year) {
    return { make, model, year }
}
getLaptop("Apple", "MacBook", "2015"); // {make: "Apple", model: "MacBook", year: "2015"} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES5
function getLaptop(make, model, year) {
    return {
       sayModel : function() {
            return model;
        }
    }
}
getLaptop("Apple", "MacBook", "2015").sayModel(); //"MacBook" 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
function getLaptop(make, model, year) {
    return{
        sayModel() {
            return model;
        }
    }
}
getLaptop("Apple", "MacBook", "2015").sayModel(); //"MacBook" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES5 
var key1 = 'one',
  obj = {
    two: 2,
    three: 3
  };
obj[key1] = 1;
// obj.one = 1, obj.two = 2, obj.three = 3 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
const key1 = 'one',
  obj = {
    [key1]: 1,
    two: 2,
    three: 3
  };
// obj.one = 1, obj.two = 2, obj.three = 3 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES6
const i = 1,
  obj = {
    ['i' + i]: i
  };
console.log(obj.i1); // 1 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6
const i = 2,
  obj = {
    ['mult' + i]: x => x * i
  };

console.log( obj.mult2(5) ); // 10 
```

Enter fullscreen mode Exit fullscreen mode

* * *

###### ES2018 (ES9)休息/传播属性

```
const myObject = { a: 1, b: 2, c: 3 };
const { a, ...x } = myObject;
// a = 1, x = { b: 2, c: 3 } 
```

Enter fullscreen mode Exit fullscreen mode

```
restParam({ a: 1, b: 2, c: 3 });
function restParam({ a, ...x }) {
  // a = 1
  // x = { b: 2, c: 3 }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const obj1 = { a: 1, b: 2, c: 3 },
  obj2 = { ...obj1, z: 26 };
// obj2 is { a: 1, b: 2, c: 3, z: 26 } 
```

Enter fullscreen mode Exit fullscreen mode

ES2018 (ES9) rest/spread 属性支持不完整，但在 Chrome、Firefox 和 Node.js 8.6+中可用。

* * *

##### 6。ES6 中的箭头功能

###### 主要好处:没有‘这个’的束缚

```
// ES5 Callback functions with dynamic context
var _this = this
$('.btn').click(function(event){
  _this.sendData()
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6 Callback functions with dynamic context
$('.btn').click((event) =>{
  this.sendData()
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
var cat = {  
  lives: 9,  
  jumps: () => {  
    this.lives--;  
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// implicit return
() => 42
// In fact, you don’t even need the parentheses!
_ => 42
x => 42  || (x) => 42
// explict return
var addValues = (x, y) => {  
  return x + y  
}
// wrap in parentheses to return object literal 
x =>({ y: x }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9']
var messages = ids.map(function (value) {
  return "ID is " + value // explicit return
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
var ids = ['5632953c4e345e145fdf2df8','563295464e345e145fdf2df9']
var messages = ids.map(value => `ID is ${value}`) // implicit return 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 7。ES6 中的承诺

一个*承诺*代表一个异步操作的最终结果。
有许多 promise 实现的语法略有不同。q，蓝鸟，deferred.js，vow，avow，jquery deferred 等...，ES6 有一个标准`Promise`实现

```
setTimeout(function(){
  console.log('Yay!')
}, 1000) 
```

Enter fullscreen mode Exit fullscreen mode

```
var wait1000 =  new Promise((resolve, reject)=> {
  setTimeout(resolve, 1000)
}).then(()=> {
  console.log('Yay!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
setTimeout(function(){
  console.log('Yay!')
  setTimeout(function(){
    console.log('Wheeyee!')
  }, 1000)
}, 1000) 
```

Enter fullscreen mode Exit fullscreen mode

```
var wait1000 =  ()=> new Promise((resolve, reject)=> {setTimeout(resolve, 1000)})

wait1000()
  .then(function() {
    console.log('Yay!')
    return wait1000()
  })
  .then(function() {
    console.log('Wheeyee!')
  }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 8。块范围的构造 Let 和 Const

`let`将范围限制到区块。`Vars`是函数作用域，`const`是不可变的，也是块作用域，就像`let`

```
function calculateTotalAmount (vip) {
  var amount = 0
  if (vip) {
    var amount = 1
  }
  { // more crazy blocks!
    var amount = 100
    {
      var amount = 1000
      }
  }  
  return amount
}
console.log(calculateTotalAmount(true))
// The result will be 1000 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
function calculateTotalAmount (vip) {
  var amount = 0 // probably should also be let, but you can mix var and let
  if (vip) {
    let amount = 1 // first amount is still 0
  } 
  { // more crazy blocks!
    let amount = 100 // first amount is still 0
    {
      let amount = 1000 // first amount is still 0
      }
  }  
  return amount
}
console.log(calculateTotalAmount(true))
// The result will be 0 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
function calculateTotalAmount (vip) {
  const amount = 0  
  if (vip) {
    const amount = 1 
  } 
  { // more crazy blocks!
    const amount = 100 
    {
      const amount = 1000
      }
  }  
  return amount
}
console.log(calculateTotalAmount(true))
// The result will be 0 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 9。ES6 中的类别

```
class baseModel {
  // default params
  constructor(options = {}, data = []) { // class constructor
    this.name = 'Base'
    this.url = 'http://azat.co/api'
    this.data = data
    this.options = options
  }
  // no need function keyword and ":"
  getName() { // class method
    console.log(`Class name: ${this.name}`)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
class AccountModel extends baseModel {
  constructor(options, data) {
    super({private: true}, ['32113123123', '524214691']) //call the parent method with super
     this.name = 'Account Model'
     this.url +='/accounts/'
   }

  get accountsData() { //calculated attribute getter
    // ... make XHR
    return this.data
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let accounts = new AccountModel(5)
accounts.getName()
console.log('Data is %s', accounts.accountsData) 
```

Enter fullscreen mode Exit fullscreen mode

```
Class name: Account Model
Data is %s 32113123123,524214691 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 10。ES6 中的模块

在 ES6 之前的 JavaScript 中没有本地模块支持。人们想出了 AMD、RequireJS、CommonJS 等变通办法。现在有了带`import`和`export`操作数
的模块

```
// ES5 `module.js`
module.exports = {
  port: 3000,
  getAccounts: function() {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES5 `main.js`
var service = require('module.js')
console.log(service.port) // 3000 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
// ES6 `module.js`
export var port = 3000
export function getAccounts(url) {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// ES6 file `main.js`
import {port, getAccounts} from 'module'
console.log(port) // 3000
// OR
import * as service from 'module'
console.log(service.port) // 3000 
```

Enter fullscreen mode Exit fullscreen mode

如果您有任何其他有用的想法或建议来提高生产力或保持您的系统更新，我很乐意听到并分享它，请随时在评论中留下或 [@meabed](https://twitter.com/meabed)

快乐编码🤞