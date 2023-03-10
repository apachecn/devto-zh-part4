# code mirror——一个简单有效的 web 应用程序代码编辑器组件

> 原文：<https://dev.to/prahladyeri/codemirror-a-simple-and-efficient-code-editor-component-for-your-web-applications-15da>

我非常喜欢生活中简单的东西，那些从用户端用最少的努力或配置就能实现很多的东西。每当我遇到这样简单的事情，我喜欢与大家分享，这就是我现在正在做的。

在我最近的基于 flask 的 web 项目中，一个要求是在应用程序本身中提供一个代码编辑器。web 应用程序有一个主系统和一个子系统，用户希望子系统部分可以动态地编写脚本，这样她就可以在以后更改这部分代码，并对其进行自定义。基于 web 的编辑器如下所示(除了它包含实际的代码而不是 Hello World 占位符):

[![CodeMirror Demo](img/1a27030fb6bb1b5fca626790812ddac3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d6utwYi4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prahladyeri.com/uploads/CodeMirror_demo.png)

这是可能的，因为 [CodeMirror](https://codemirror.net/) 是一个开源的 javascript 库，他们也有 [github 库](https://github.com/codemirror/CodeMirror/)。你甚至不需要下载这个库，它可以在 CDNJS 上找到，所以你可以简单地链接样式表和你的 html 标签中的两个脚本，就像这样:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.47.0/codemirror.min.js"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.47.0/codemirror.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.47.0/mode/python/python.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

前两个资源是必需品(`codemirror.min.js`和`codemirror.min.css`)，而最后一个资源是模式资源(`python.min.js`)，这取决于您希望编辑器使用哪种语言或模式。在我的例子中，它是 python，但是有几十种模式可用于不同的语言，如 java、php、ruby、html、css 等。

使用这个组件最好的事情就是开始使用它！你所要做的就是创建一个 html `textarea`(我已经有了一个愚蠢的代码编辑器！)而且你已经准备好了:

```
$(document).ready(function(){
    console.log('adding codeMirror object');
    window.myCodeMirror = CodeMirror.fromTextArea(document.getElementById("txtScript"), {
       lineNumbers: true,
        mode: 'python',
    });
    window.myCodeMirror.on('change', editor => {
        //console.log(editor.getValue()); 
    });
    window.myCodeMirror.on('keydown', editor => {
        //do whatever you want
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

`CodeMirror.fromTextArea()`是将你的`textarea`直接转换成代码编辑器的重要方法，如此简单有效！但是请注意，在这之后，它会完全使您的`textarea`元素消失(`display: none`)，您将不得不使用 CodeMirror 对象变量(在本例中为`window.myCodeMirror`)来读取或写入文本到它:

```
var code = window.myCodeMirror.getValue(); //get value from editor
window.myCodeMirror.setValue(code); //set value to editor 
```

Enter fullscreen mode Exit fullscreen mode

这个组件也有大量的[配置选项](https://codemirror.net/doc/manual.html#config)，如标签大小、主题、方向(ltr/rtl)、行号等。如果您遇到一个需要它的 web 项目，我希望这个编辑器组件能够帮助您。

编码快乐，编码成功！