# 一个饭桶:别名

> 原文：<https://dev.to/luisejrobles/un-vistazo-a-git-aliases-2kop>

如果我们出于某种原因或决心在编程方面处于领先地位，你可能已经意识到，在这个行业中，很多工作都是与你错误地认为的相反的合作，当这是其中一个不可或缺的工具时，它就是**【git】**。这次轮到写关于 **git 别名**的文章了。

#### Qué son los git 别名？

**别名**或姓氏是别名或‘昵称’，它们被分配给我们已经知道的 git 命令。

#### 我们这样做吗？

要配置别名，必须使用命令

`git config`

为此，我们有机会全局声明*(供任何版本化文件夹使用)*或局部声明*(每个版本化文件夹的别名)*。

> 如果不想使用命令`git config`，我们可以通过`.git/config` *局部*或`~/.gitconfig` *全局*配置这些别名

##### 例

我们想把 **commit** 的缩写设定为**局部**，这样做的方法是:

```
git config alias.co commit 
```

如果我们想把它配置成**全域**的话，那就是:

```
git config --global alias.co commit 
```

*Recuerdan que les cometéde la bandera`--global`？*T3】😉

现在，我们可以使用我们的新缩写，如下所示:

```
git co -m '¡nuevo commit utilizando aliases!' 
```

**如果我们也讨厌写 el -m 的话会发生吗？**

好吧，我们也可以把旗子连接起来，如下所示:

```
git config alias.com 'commit -m' 
```

[![](img/d203ec64aa56725a2f654c722a7ea23e.png)](https://i.giphy.com/media/s2qXK8wAvkHTO/giphy.gif)

因此，使用此功能非常简单，因为您可以根据需要创建不同的别名，以加快进度并节省时间。🤙

∞他们已经喜欢了，我们将在下一篇将于星期四发行的帖子中阅读！

欢迎反馈