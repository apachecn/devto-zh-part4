# 切换自定义代码镜像模式的注释

> 原文：<https://dev.to/johnphamous/toggling-comments-for-custom-codemirror-modes-4c0g>

为自定义的 CodeMirror 模式添加切换行/块注释的功能还没有很好的记录。

对于我的用例，我定义了一个简单模式。我希望允许用户通过点击 GUI 按钮或使用键盘快捷键来切换行/块注释。我是这样做的。

* * *

以下是我所做的与文档不同的更改。

### 你的模式文件

描述注释的样子。

```
CodeMirror.defineSimpleMode('mode-name', {
  arguments: [],
  meta: {
+     lineComment: '#'
   },
  start: [
+    {
+      regex: /#.*/,
+      token: 'comment',
+    },
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 导入 CodeMirror 注释插件

该文件可在`node_modules/codemirror/addons/comment`中找到

### [T1】实例化 CodeMirror 实例](#instantiating-the-codemirror-instance)

定义一个快捷键来触发 CodeMirror options 对象中的注释切换。

```
{
  extraKeys: {
    'Ctrl-/': editor.execCommand('toggleComment')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode