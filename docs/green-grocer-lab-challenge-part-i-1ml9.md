# 蔬菜水果实验室

> 原文：<https://dev.to/harlessmark/green-grocer-lab-challenge-part-i-1ml9>

## 问题

我有一个购物车，里面装满了我需要用`consolidate_cart`方法合并的物品。这辆车由一个`array`组成，需要放入一个`hash`。每个索引项目有一个名称，价格，如果它是清仓，每一个项目有多少(计数)。

比如这个:

```
[
  {"AVOCADO" => {:price => 3.00, :clearance => true }},
  {"AVOCADO" => {:price => 3.00, :clearance => true }},
  {"KALE"    => {:price => 3.00, :clearance => false}}
] 
```

Enter fullscreen mode Exit fullscreen mode

需要变成这样:

```
{
  "AVOCADO" => {:price => 3.00, :clearance => true, :count => 2},
  "KALE"    => {:price => 3.00, :clearance => false, :count => 1}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我学到了什么

这对我来说是一个特别困难的挑战，我需要帮助，因为我甚至不知道从哪里开始。不过，我也没差太远。我对`.each`和`|pipes|`的含义有了更坚定的理解，显然，也将数组中的嵌套散列移动到单个散列中。

在寻求帮助时，我被告知安装 Pry，这是一个 Ruby 程序，可以让你在特定行停止代码，以便更好地调试它。我自己没有机会玩它，但是它看起来确实是一个对 Ruby 程序员非常有用的工具。

## 最终迭代

```
def consolidate_cart(cart)
  output = {}
  cart.each do |item|
    item_name = item.keys[0]
    if output[item_name]
      output[item_name][:count] += 1 
    else
      output[item_name] = item[item_name]
      output[item_name][:count] = 1 
    end
  end
  output
end 
```

Enter fullscreen mode Exit fullscreen mode

## 原问题

购物车从一组单独的商品开始。用`consolidate_cart`方法将它转换成一个散列，其中包括每一项的计数。

例如，如果该方法给定了下面的数组:

```
[
  {"AVOCADO" => {:price => 3.00, :clearance => true }},
  {"AVOCADO" => {:price => 3.00, :clearance => true }},
  {"KALE"    => {:price => 3.00, :clearance => false}}
] 
```

Enter fullscreen mode Exit fullscreen mode

那么该方法应该返回下面的散列:

```
{
  "AVOCADO" => {:price => 3.00, :clearance => true, :count => 2},
  "KALE"    => {:price => 3.00, :clearance => false, :count => 1}
} 
```

Enter fullscreen mode Exit fullscreen mode