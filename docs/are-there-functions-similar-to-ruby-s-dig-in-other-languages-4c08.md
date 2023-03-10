# 其他语言中有类似 Ruby 的‘dig’的函数吗？

> 原文：<https://dev.to/obahareth/are-there-functions-similar-to-ruby-s-dig-in-other-languages-4c08>

我非常喜欢使用 Ruby 的`Array#dig`和`Hash#dig`操作符(在 Ruby 2.3 中引入)来快速安全地访问深度嵌套的结构

我希望看到其他语言的不同版本，这样我就可以更多地使用它们了！

dig 是这样工作的:

假设我们有一个类似这样的`orders`数组。

```
orders = [
  {
    id: 1,
    customer: {
      name: "Customer 1",
      phone: "1234"
    }
  },

  {
    id: 2
  },

# ...
] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以很容易地在这个结构中导航`dig`像这样

```
orders.dig(0, :customer, :phone) #=> "1234" 
```

Enter fullscreen mode Exit fullscreen mode

我们也不用担心任何“中间”对象不存在，因为一旦它找到不存在的对象，就会返回零。

```
orders.dig(1, :customer, :phone) #=> nil 
```

Enter fullscreen mode Exit fullscreen mode

当它发现`customer`不存在时就返回`nil`,这让我避免了每次访问嵌套对象时检查键是否存在。

在其他语言中，有哪些很酷的方法可以访问像这样的嵌套数据？我这样问是因为我想学习，也是因为目前我可能在以过于复杂的方式学习。

感谢阅读！