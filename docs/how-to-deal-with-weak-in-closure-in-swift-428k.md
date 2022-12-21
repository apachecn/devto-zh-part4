# 如何应对 Swift 中的封闭弱势

> 原文：<https://dev.to/onmyway133/how-to-deal-with-weak-in-closure-in-swift-428k>

传统上我们需要从`if let`到`strongSelf`

```
addButton.didTouch = { [weak self] in
    guard
        let strongSelf = self,
        let product = strongSelf.purchasedProduct()
    else {
        return

    strongSelf.delegate?.productViewController(strongSelf, didAdd: product)
} 
```

这很麻烦，我们可以发明一个高阶函数来压缩和展开选项

```
func with<A, B>(_ op1: A?, _ op2: B?, _ closure: (A, B) -> Void) {
    if let value1 = op1, let value2 = op2 {
        closure(value1, value2)
    }
}

addButton.didTouch = { [weak self] in
    with(self, self?.purchasedProduct()) {
        $0.delegate?.addProductController($0, didIncrease: $1)
    }
} 
```

* * *

原帖[https://github.com/onmyway133/blog/issues/326](https://github.com/onmyway133/blog/issues/326)