# C#中的列表不再有 NullReferenceException

> 原文：<https://dev.to/alialp/no-more-nullreferenceexception-for-lists-in-c-4dea>

虽然编码有时很小很小的事情很令人沮丧，比如在添加新项目之前忘记实例化一个列表对象，一个简单的解决方案是编写一个扩展方法，就像这样

```
public static class ListExtension
{
    public static void Add<T>(this List<T> list, T value, out List<T> newInstance)
    {
        if (list == null) list = new List<T>();
        list?.Add(value);
        newInstance = list;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后这样用

```
[Test]
public void SafeAddTest()
{
    List<string> list = null;
    list.Add("test",out list);

    Assert.AreEqual(1,list.Count);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，每当你想确定你要添加的列表已经被实例化了，你可以简单地把列表实例作为第二个变量传递，就这样。

快乐编码:)