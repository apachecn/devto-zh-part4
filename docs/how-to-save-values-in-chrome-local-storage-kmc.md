# 如何在 Chrome 本地存储中保存值

> 原文：<https://dev.to/chintukarthi/how-to-save-values-in-chrome-local-storage-kmc>

如何在 Chrome 浏览器的本地存储内部本地保存一个变量？
这是我曾经在谷歌上搜索得到答案的组合之一。

你猜怎么着？作为 chrome 扩展开发领域的新手，我很难理解 chrome 扩展开发文档中解释的概念。最后，我登陆了 youtube 的一个频道，那个频道([https://www.youtube.com/watch?v=DHP2h_7mOfA](https://www.youtube.com/watch?v=DHP2h_7mOfA))解释了这个过程。这家伙是那种你向一个 5 岁的孩子解释这个概念的人，他很擅长。多亏了他。

所以在这里我试着用一种简单的方式来解释同样的事情，这样如果将来有人面临这个问题。以防万一。

首先，您需要设置希望保存的变量的值，以便检索它。

所以我们用下面的方法来保存。

```
 chrome.storage.sync.set({'variable_name': 'variable_value'}, function() {
  console.log("you saved me!!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里,“set”方法在本地存储中设置名为“variable_name”的变量的值。

为了检索它，您需要使用“get”方法而不是“set”方法。

```
 chrome.storage.sync.get(['variable_name'], function(result) {
      if(result.variable_name == undefined) {
        console.log("I am retrieved!!");
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面将检索作为对象的“变量名称”的值。因此，为了访问它，您需要使用对象名后跟变量名。
(即)

```
result.variable_name 
```

Enter fullscreen mode Exit fullscreen mode

我们检查未定义的原因是一种故障保险。这在文档中没有涉及。

这种自动防故障的场景是，假设您的扩展是使用名为
的变量的本地存储开发的

```
'local_variable' 
```

Enter fullscreen mode Exit fullscreen mode

但是当用户第一次试图在浏览器中打开扩展时，它会抛出一个

```
undefined - error 
```

Enter fullscreen mode Exit fullscreen mode

这是因为变量尚未在用户的本地存储中定义。

所以这个自动防故障装置到时候就派上用场了。你可以像这样增加一个额外的条件，这样就不会伤害你的代码和用户的心😜。