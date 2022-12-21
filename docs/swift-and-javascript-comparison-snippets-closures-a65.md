# Swift 和 JavaScript 比较片段——闭包

> 原文：<https://dev.to/unbug/swift-and-javascript-comparison-snippets-closures-a65>

[![](img/b4d08e490fa849cf860feb6c4d492d2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP2yhdUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/799578/41671640-51642072-74ea-11e8-8bf2-7588062eed70.png)

GitHub:[https://github.com/unbug/sj](https://github.com/unbug/sj)

## 关闭

斯威夫特

```
// Closure Expressions
// The Sorted Method
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
// or
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } ) 
```

JavaScript

```
// Closure Expressions
// The Sort Method
const names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
function backward(s1, s2) {
    return s1 < s2
}
let reversedNames = names.sort(backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
reversedNames = names.sort((s1, s2) => {
    return s1 < s2
})
// or
reversedNames = names.sort((s1, s2) => return s1 < s2) 
```