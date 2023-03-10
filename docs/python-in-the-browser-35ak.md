# 浏览器中的 Python

> 原文：<https://dev.to/petercour/python-in-the-browser-35ak>

[![](img/4a892012dc1af520d8d87e5f21b8307a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_bBI0zno--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/be4pcdin17te9kocgetn.png)

你可以在网上使用 Python，而不是 Javascript。是的，真的。

有一个名为 Brython 的项目:“客户端 web 编程的 Python 3 实现”。

那么它是如何工作的:

第一步:包含 Brython

```
<script type="text/javascript" src="/src/brython.js"></script> 
```

第二步:写你的代码，比如说

```
<body onload="brython(1)">

<script type="text/python">
from browser import document, alert

def echo(ev):
    alert("Hello {} !".format(document["zone"].value))

document["test"].bind("click", echo)
</script>
<p>Your name is : <input id="zone" autocomplete="off">
<button id="test">clic !</button>
</body> 
```

第三步:演示[https://brython.info/gallery/hello.html](https://brython.info/gallery/hello.html)

更多资源，

## 学习 python 和 demos

*   [https://brython.info/demo.html?lang=en](https://brython.info/demo.html?lang=en)
*   [https://brython.info/gallery/gallery_en.html?lang=en](https://brython.info/gallery/gallery_en.html?lang=en)
*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)