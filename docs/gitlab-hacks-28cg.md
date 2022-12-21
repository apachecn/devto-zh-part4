# Gitlab“黑客”

> 原文：<https://dev.to/shushugah/gitlab-hacks-28cg>

曾经想在 Gitlab 上看起来“高效”吗？将此粘贴到您的控制台。你最喜欢的 git 相关技术是什么？😜

```
colors = [`rgb(172, 213, 242)`,
`rgb(127, 168, 201)`,
`rgb(82, 123, 160)`,
`rgb(37, 78, 119)`]

$(".user-contrib-cell").each(function(){ $(this).attr("fill", colors[Math.floor(Math.random()*colors.length)])}) 
```

Enter fullscreen mode Exit fullscreen mode

查看下图查看结果！

[![Graph of pixels representing coding activity on Gitlab homepage, with varying shades of blue. The darker, the more productive.](img/453921a196fa5e6417c9081cc07d08b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z47vqsQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5fd4es13h7obh7om95qz.png)

两者可以兼得，为什么还要选择一个平台？💩

[![Github productivity chart, however with Gitlab and Github mishmash of blue and green tiles](img/c735ff2c986647c7fc878df8cd79b9b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kMShDa0b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6202gcvbx1ok769wa6vr.png)

感谢 [lukeshiru](https://dev.to/lukeshiru) 的香草 javascript 解决方案+ GitHub 脚本！

[![lukeshiru profile image](img/835ae3043b719cf9432b06c9ebbe7099.png) ](/lukeshiru) [ △ LUKE 知る ](/lukeshiru) • [<time datetime="2019-08-16T13:39:06Z"> Aug 16 '19 </time>](https://dev.to/lukeshiru/comment/e7dd) 

你那里打错了，应该是`colors.length`而不是`items.length`。同样，你可以用普通的 JS 这样写:

```
((colors, query) =>
    Array.from(document.querySelectorAll(query)).forEach(cell =>
        cell.setAttribute(
            "fill",
            `#${colors[Math.floor(Math.random() * colors.length)]}`
        )
    ))(["acd5f2", "7fa8c9", "527ba0", "254e77"], ".user-contrib-cell"); 
```

Enter fullscreen mode Exit fullscreen mode

对于 GitHub，你可以这样改变参数:

```
((colors, query) =>
    Array.from(document.querySelectorAll(query)).forEach(cell =>
        cell.setAttribute(
            "fill",
            `#${colors[Math.floor(Math.random() * colors.length)]}`
        )
    ))(["c6e48b", "7bc96f", "239a3b", "196127"], ".js-calendar-graph-svg rect"); 
```

Enter fullscreen mode Exit fullscreen mode