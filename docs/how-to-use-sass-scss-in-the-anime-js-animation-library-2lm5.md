# 如何在 Anime.js 动画库中使用 SASS/SCSS

> 原文：<https://dev.to/ameliaruzek/how-to-use-sass-scss-in-the-anime-js-animation-library-2lm5>

Anime.js 是一个优秀的 JavaScript 动画库，易于使用和阅读。文档列出了属性的可接受值，如无单位、特定单位、相对、颜色、从-到和基于功能。Anime.js 可能不直接接受 SASS，但是接受 JavaScript 变量。

哦！当然啦！

如果 anime.js 中 CSS 属性的值可以是一个简单的变量，那么问题就变成了:如何将 SCSS 传递到 JavaScript 变量中。

好消息！有一个包可以解决这个问题！css 加载程序

* * *

npm 安装-保存-开发 CSS-加载程序

* * *

好了，现在怎么办？

直接从。scss 文件，然后导入您的。scss 到您的 JavaScript 中！

* * *

//variables . scss
$ white-color:# ffffff；

:export {

white color:$ white-color；

}

* * *

//您的 js 文件
从 variables.scss 导入变量

动漫({
...
color:variables . white color；
...
})

* * *

最后
你有了一个 JavaScript 变量，它可以作为一个值添加到 anime.js 动画中的 css 属性中！