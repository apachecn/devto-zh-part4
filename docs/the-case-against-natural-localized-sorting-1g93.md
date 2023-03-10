# 反对“自然/本地化”排序的理由

> 原文：<https://dev.to/strredwolf/the-case-against-natural-localized-sorting-1g93>

因此，我在 iPad 上最喜欢的文本编辑器更新了——text astic 升级到了第 8 版，其中一个功能是可选择排序，按名称、扩展名、日期和大小排序。我想，一切都很好。

然后我去找一个文件...它不在我期待的地方。

我在去巴尔的摩的通勤列车上，拿起电话，费力地搜索一个我知道是我创建并命名的文件。不在 Textastic。不是在 Dropbox，是在同步的地方。不是在普通的地方...

然后，我重新启动 Textastic...找到它...在错误的地点。同一个目录-他们改变了分类方式。

# 只是 ASCII！就是这么简单！

让我把这个放在上下文中。我正在写一部小说，将一种奇怪的毛茸茸的文化与一站又一站的游牧列车混合在一起，名为【T2:一站又一站。**插播预警**你可以在我的 [Patreon](https://patreon.com/strredwolf) **插播预警**看到一个多毛毛乐队坐火车巡演的故事。

这部小说是分章节写的，每一节的大小在 5-10K 之间。章节最多有 8 节长，所以为了让它们保持一定的顺序，我用节号前缀来命名它们...那是十六进制的。

这里有一个小例子:

```
01.Boston-1.md
02.Boston-2.md
03.Boston-3.md
04.Boston-4.md
05.Boston-5.md
06.NewHaven.md
08.NewYork.md
09.Secaucus.md
0A.Trenton.md
0B.Trenton-2.md
0C.Trenton-3.md
10.Philly.md
11.Philly-2.md
18.Baltimore.md
19.Baltimore-2.md
20.WashDC.md
24.WashDC.md
28.Pittsburgh.md
30.Cleveland.md
37.Gary.md
38.Chicago.md
39.Chicago.md
3A.Chicago.md
3B.Chicago.md 
```

随着故事沿着现有的现实生活中的铁路线穿过各个城市，我以它们的名字来命名各个部分。

那么 GTK 的文件选择器、MacOS Finder 和 Textastic 是做什么的呢？它将第一个数字视为数字，并忽略任何前面的零。这样你就可以拥有这个:

```
file 8
file 9
file 10
file 11
file 12 
```

好吧，那很好...但是我的命名惯例呢？谢天谢地，Linux 的“ls”要求你给它一个“-v”开关。

```
$ ls -v1 *.md
0A.Trenton.md
0B.Trenton-2.md
0C.Trenton-3.md
01.Boston-1.md
02.Boston-2.md
3A.Chicago.md
3B.Chicago.md
03.Boston-3.md
4A.Dallas.md
4B.Ft_Worth.md
4C.Ft_Worth.md
04.Boston-4.md
5A.Tuscon.md
05.Boston-5.md
06.NewHaven.md
7F.SanFrancisco.md
08.NewYork.md
09.Secaucus.md
9F.Metroburg-Home.md
10.Philly.md
11.Philly-2.md
18.Baltimore.md
19.Baltimore-2.md
20.WashDC.md
24.WashDC.md
28.Pittsburgh.md
30.Cleveland.md
37.Gary.md
38.Chicago.md
39.Chicago.md 
```

这就是我在 MacOS、GTK 的文件选择器以及现在的 Textastic 中看到的。

# 为什么讨厌上 strcmp？

这是 UTF 8，又名 Unicode。我正在运行“en_US.utf8”本地化，主要是因为*是默认的。* MacOS 和 iOS 使用它。GTK 使用它。现在 Textastic 开始使用它，因为它使用了 iOS 的' T0 '例程...它按上下文而不是按字节排序。它是由 Unicode 的规范强制规定的。

就是这么回事。这可能是*规定的*，但并不是*在所有情况下都是正确的*。我有要案。

# 能做什么？

作为一个程序员，给用户选择关闭“自然”排序为“文字”排序。选择越多越好。

作为用户，文件 bug。我这样做是为了纺织和 GTK/XFCE。

抵制变通的冲动。Linux 在命令行中就有——让它成为可选的。