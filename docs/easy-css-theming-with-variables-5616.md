# 使用变量轻松实现 CSS 主题化

> 原文：<https://dev.to/grumpytechdude/easy-css-theming-with-variables-5616>

我最近不得不处理两个项目的主题，这是我学到的，

# CSS 变量太神奇了

```
--colour-blue = #0074D9;
--colour-blue-dark = #001F3F;
--colour-blue-light = #7FDBFF; 
```

我们在这里做的是定义我们的蓝色看起来像什么。所以任何时候我们需要蓝色，我们有标准的！

“但是等等，”我听到你说，“那只是一个标准的调色板！这和主题化有什么关系？”

# CSS 变量可以引用其他变量

```
--colour-primary = var(--colour-blue);
--colour-primary-active = var(--colour-blue-light);
--colour-primary-disabled = var(--colour-blue-dark); 
```

我们在这里做的是将*调色板颜色*从*设计颜色*中分离出来。(如果有人知道这些的正确名称，请告诉我！)

这意味着我们在一个交换变量的地方，CSS 将...工作。

# 应用主题

重要的事情先来！CSS 变量的作用域是它们所在的类。我想把它应用到整个 dom，因为我只是声明变量——但是你可以把它赋给你的顶层元素。

```
:root {
  --colour-blue = #0074D9;
  --colour-blue-dark = #001F3F;
  --colour-blue-light = #7FDBFF;

  --colour-orange = #FF851B;
  --colour-orange-dark = #FF4136;
  --colour-orange-light = #FFDC00;

  --colour-primary = var(--colour-blue);
  --colour-primary-active = var(--colour-blue-light);
  --colour-primary-disabled = var(--colour-blue-dark);
} 
```

通过把原色放在那里，我们定义了一个默认的主题。这意味着如果没有应用其他主题，这将生效。你也可以像我这样做，即*而不是*定义一个默认主题。我认为，在开发过程中，如果你错过了什么，那就更明显了。

接下来，我们将主题定义为简单的类

```
.theme-light {
  --colour-primary = var(--colour-blue);
  --colour-primary-active = var(--colour-blue-light);
  --colour-primary-disabled = var(--colour-blue-dark);
}

.theme-dark {
  --colour-primary = var(--colour-orange);
  --colour-primary-active = var(--colour-orange-light);
  --colour-primary-disabled = var(--colour-orange-dark);
} 
```

现在，在我们的根元素上，我们可以定义默认主题

```
<div class='theme-light'></div> 
```

# 预览主题

结果是，如果你将一个主题应用于一个元素，这个主题优先于顶层主题。因此，如果你想在主题中显示一个主题切换按钮，它将切换到，这很简单

```
<div class='theme-light>
  <button>Some Button</button>
  <button class='theme-dark'>Dark Theme</button>
</div> 
```

# 切换主题

切换主题就是用一个主题替换另一个主题的用法。这在 JS 中很容易做到——你可以保存一个主题组件列表并直接查询它们，或者你可以使用更新的`document.querySelectorAll()`方法。

```
function toggleTheme() {
  const lightElements = document.querySelectorAll('.theme-light');
  const darkElements = document.querySelectorAll('.theme-dark');

lightElements.forEach(element => {
  element.classList.remove('theme-light');
  element.classList.add('theme-dark');
})
darkElements.forEach(element => {
  element.classList.remove('theme-dark');
  element.classList.add('theme-light');
}) 
```

而要触发它:
`<button onclick='toggleTheme()'>Toggle Theme</button>`

# 记住用户的主题偏好

如果你在做客户端渲染，这就有点棘手了。如果你也在做 SSR，这可能会很棘手——但我不知道你会怎么做。也许是 Cookies 或自定义 http 头？反正！

## 存储主题

我使用本地存储，但是 cookie 也可以。要存储用户选择的主题，您可以在切换主题功能中添加这一行:

`localStorage.setItem('theme', 'theme-dark')`

## 应用主题

如果你在应用用户主题之前等待页面完全加载，你会看到一个初始主题的闪现——这可不好！我不认为它是完美的，但是我在我的 html 中的主题元素下面直接放了一个厚脸皮的脚本标签。

```
<div id='theme-element' class='theme-light'>
<script>const el = document.getElementById('theme-element')
el.classList.remove('theme-light')
el.classList.add(localStorage.getItem('theme') || 'theme-light')
</script> 
```

我敢打赌，如果你正在使用 React 或类似的，你可能只需要在组件中这样做。
`<div className={localStorage.getItem('theme') || 'theme-light}>`

# 按面积使用变量，而不是结果

在我做主题的第一个项目中，我已经事先写好了 CSS，并且必须改进主题。这导致了一个问题——在一个主题中共享一种颜色的区域，在另一个主题中却没有。这意味着我不能仅仅用`orange`替换`blue`！最后，我给这些区域命名，并把它们设置在主题中。

例如，我使用了

```
.header {
  background-color: var(--color-blue-dark);
}
.dropdown-list {
  background-color: var(--color-blue-dark);
} 
```

这里的问题是，现在我们希望标题是灰色的，下拉列表是橙色的！最后，我得到了

```
.header {
  background-color: var(--header-colour-background);
}
.dropdown-list {
  background-color: var(--dropdown-colour-background);
}

.theme-light {
  --header-colour-background = var(--color-blue-dark);
  --dropdown-colour-background = var(--color-blue-dark);
}

.theme-dark {
  --header-colour-background = var(--color-grey-light);
  --dropdown-colour-background = var(--color-orange);
} 
```

有没有更好的方法来实现这一点？我打赌有！如果你有什么建议，我很乐意听听。