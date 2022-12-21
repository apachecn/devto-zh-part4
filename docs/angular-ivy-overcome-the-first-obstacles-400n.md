# 棱角分明的常春藤:克服第一个障碍

> 原文：<https://dev.to/negue/angular-ivy-overcome-the-first-obstacles-400n>

这是我在升级到`9.0.0-next.2`后，在一个已经存在的 Angular (7)应用程序上试用 Ivy 时出现的一系列与 Ivy 相关的问题。

在最初需要对[升级说明](https://update.angular.io/#7.0:9.0)进行修改后，我偶然发现了这些问题:

## 1。`Unable to write a reference to {X} from {Y}`

我有一个从目录(低于`src/lib/{name}/`)中导出多个模块的库(使用路径别名),在第一次 ivy-try 时出现了这个问题。

起初，我试图允许通配符别名`@my/lib-name/subfolder`并导出每个子文件夹和它自己的桶。虽然这在一个模块上有效，但必须在每个子模块上完成。幸运的是，我发现了这个:

### 解🎉

将`"rootDir": ".",`添加到`{project-root}/tsconfig.json`中的`"compilerOptions"`中。

来源:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 评论为 #29361](https://github.com/angular/angular/issues/29361#issuecomment-511050660) 

[![rt-gavrilov avatar](img/0d3c8e24225657becfbc4748481d62c5.png)](https://github.com/rt-gavrilov) **[rt-gavrilov](https://github.com/rt-gavrilov)** commented on [<time datetime="2019-07-12T22:18:15Z">Jul 12, 2019</time>](https://github.com/angular/angular/issues/29361#issuecomment-511050660)

@p3x-robot 这在我的情况下有效:

1.  角度 8 . 2 . 0-下一个. 1
2.  将`"rootDir": "."`追加到根`tsconfig.json`中的`compilerOptions`
3.  像这样使用 index.ts:

```
"paths": {
    "@libs/api": ["libs/api/src/index.ts"], 
```

BTW，谢谢大家@pburkindine @james-criscuolo，你们是我的最爱

[View on GitHub](https://github.com/angular/angular/issues/29361#issuecomment-511050660)

## 2。`Cannot combine @Input decorators with query decorators`

这是一个奇怪的问题(对我来说)。即使在 GitHub/Stackoverflow 上找到了一些帖子，答案也不是真的有意义(或者说我就是没有找到“正确”的。😄)

为了解决这个问题，我在错误处添加了一个断点，以查看在参数调用中使用了什么属性名。一旦找到，我看到我在代码中做错了什么。

不知何故，我的身体里有这种东西，不知道是怎么来的😄:

```
 @Input()
  @ContentChild('template', {static: true})
  itemTemplate: TemplateRef<ElementRef>; 
```

### 解🎉

所以如果你有一个`@Input()` *和* `@ContentChild()`(大概也有`@ViewChild()`)..选一个！:)

谢天谢地，这是我唯一拥有的。如果我遇到其他人，我会继续更新这个帖子。

如果你还有任何其他奇怪的问题，请评论。:)