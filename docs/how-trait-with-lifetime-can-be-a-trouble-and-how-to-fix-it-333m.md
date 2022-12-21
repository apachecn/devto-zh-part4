# 具有生命周期的特征如何成为一个麻烦以及如何解决它

> 原文：<https://dev.to/dannypsnl/how-trait-with-lifetime-can-be-a-trouble-and-how-to-fix-it-333m>

在我的例子中，我有一个名为`Resource`的特性，用于从字节反序列化。现在我想为其他人`Resource`重用一个名为`List`的结构，所以我写了 done:

```
struct List<T> {
    // ignore others field
    items: Vec<T>,
}

impl<T: Resource> Resource for List<T> {
    fn from_str(s: &str) -> Result<List<T>> {
        let list: List<T> = serde_json::from_str(s)?;
        Ok(list);
    }
} 
```

因为`serde_json::from_str`需要`impl Deserialize`所以我们要修改代码:

```
impl<T: Resource + Deserialize> Resource for List<T> {
    fn from_str(s: &str) -> Result<List<T>> {
        let list: List<T> = serde_json::from_str(s)?;
        Ok(list);
    }
} 
```

它看起来工作，但不是。问题是`Deserialize`实际上是`Deserialize<'de>`，当我们在声明中使用 trait 时，我们必须满足所有的类型参数，当然包括生存期。好，那么我们写:

```
impl<'a, T: Resource + Deserialize<'a>> Resource for List<T> {
    fn from_str(s: &'a str) -> Result<List<T>> {
        let list: List<T> = serde_json::from_str(s)?;
        Ok(list);
    }
} 
```

它看起来很好，实际上在大多数情况下都有效，但是，在我的代码中，我将整个 get 数据和反序列化隐藏在一个函数中，不管它是什么，都会导致问题。

```
fn get_list_via_fetching_data<'a, T: Deserialize<'a>>() -> Result<List<T>> {
    let data = fetch(); // in my case is kubernetes api server but that's fine
    List::<T>::from_str(data)
} 
```

好吧，你会发现`data`没有`'a`活得长，为什么？因为无论如何`'a`会是一个外部生命，当然比函数中的任何东西都活得长。我们能为这个案子做些什么？我们必须颠倒他人`Resource`和`List`之间的关系，而不是将`List`视为某种`Resource`，我们为特性`Resource`添加一个函数`fn from_str_to_list(s: &str) -> Result<List<Self>>;`。因为在我的例子中`Resource` impl `Sized`和`List`在应用`Sized T`后也有一个检测到的大小，所以`List<Self>`是`Sized`。现在我们可以拥有功能`get_list_via_fetching_data`。

当然，对于最正常的情况我认为我们不需要这个，如果在未来`'_` lifetime 引入这个问题可能会得到解决(但我不确定这个问题实际上是不是这样，所以只是一个猜测)。

感谢您的阅读，下次再见。