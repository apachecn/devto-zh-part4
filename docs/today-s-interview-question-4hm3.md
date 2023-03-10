# 今天的 JavaScript React Dev 面试问题

> 原文：<https://dev.to/oathkeeper/today-s-interview-question-4hm3>

我在刚才的面试中有这些问题

```
const spaceShip = {
  name: 'Chandrayan',
  get1: () => {
    console.log(this.name);
  },
  get2: function (){
    console.log(this.name);
  }
}

spaceShip.get1()
spaceShip.get2() 
```

Enter fullscreen mode Exit fullscreen mode

现在我知道 spaceShip.get1()不会打印出任何东西，但是如果我想让它以与 get2()完全相同的方式工作，我该如何绑定它呢？

我试着像 get1.bind(spaceShip.this)或 get1.bind(spaceShip)一样先绑定`this`，然后执行，但我不确定会发生什么。

*   然后在第二个问题中

```
const person = this.state.person;

const person = {...this.state.person}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们改变 person.name 的值呢？我知道在第二种情况下，值会改变，因为那个人是一个全新的对象

但是在第一种情况下，它会改变 state.person 中的值吗？

*   有人问我关于写轮询函数的问题，我不知道，但我仍然试图说

```
function myPoll(fn, timeInterval, endTime){

var checkCondition = function(resolve, reject) {
        var result = fn();
        if(result) {
            resolve(result);
        }
        else if (// for the time checking) {
            setTimeout(checkCondition, interval, resolve, reject);
        }
        else {
            reject(error);
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

但后来他放弃了。

*   第四个问题是写 polyfill for Promise

```
Promise.all([pr1, pr2, pr3]).then().catch();
var resolvedPromises = [];
[pr1, pr2, pr3].map((item, resolve, reject) => {
    var result = item();
    if(result){
       resolvedPromises.push(resolve(result));
    }
    else {
        return reject(result);
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

所以我试着解释，我将把承诺存储在 result 中，然后把它推到数组中，在所有迭代结束时，所有承诺的解析值都将存储在该数组中。

但是他又问了一遍，如果承诺没有得到解决或被拒绝，你应该如何处理，所以我这样修改了代码

```
var resolvedPromises = [];
[pr1, pr2, pr3].map((item, resolve, reject) => {
   item().then((result) => {
        if(result){
            resolvedPromises.push(resolve(result));
        }
    });  //pr1;
    else {
        return reject(result);
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后他也不知道该问什么，但面试就这样结束了，甚至不允许我问他们任何问题。我想我不应该希望今天会赢。