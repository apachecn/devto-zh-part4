# 用 javascript 写类方法的最好方法？

> 原文：<https://dev.to/numtostr/best-wayto-write-class-methods-in-javascript-1ml3>

谁能告诉我用 js 写类方法最好的方法是什么，有什么好处和坏处吗？

# 第一次接近

```
class Dog {

    constructor(name) {
         this._name = name;
    }

    bark() {
         console.log(`hello ${this._name}`);
    }

} 
```

# 第二种方法

```
class Dog {

    constructor(name) {
         this._name = name;
    }

}

Dog.prototype.bark = function bark() {
     console.log(`hello ${this._name}`);
} 
```