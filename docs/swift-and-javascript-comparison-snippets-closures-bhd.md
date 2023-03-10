# Swift 和 JavaScript 比较片段-类别

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-closures-bhd>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 类

斯威夫特

```
// class definition
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}

// class instance
let counter = Counter()
// the initial count value is 0
counter.increment()
// the count's value is now 1
counter.increment(by: 5)
// the count's value is now 6
counter.reset()
// the count's value is now 0

print("The count property value is \(counter.count)") 
```

JavaScript

```
// class definition
class Counter {
    contructor() {
        this.count = 0
    }
    function increment() {
        this.count += 1
    }
    function increment(amount) {
        this.count += amount
    }
    function reset() {
        this.count = 0
    }
}

// class instance
let counter = Counter()
// the initial count value is 0
counter.increment()
// the count's value is now 1
counter.increment(5)
// the count's value is now 6
counter.reset()
// the count's value is now 0

console.log(`The count property value is ${counter.count}`) 
```