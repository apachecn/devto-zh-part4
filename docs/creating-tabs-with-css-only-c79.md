# 仅使用 CSS 创建选项卡

> 原文：<https://dev.to/cadienvan/creating-tabs-with-css-only-c79>

## 【TL；博士]这是演示:[代码笔](https://codepen.io/Cadienvan/pen/PMoyrR)

### 如果你还没有...

看一看我的第一篇文章来正确理解这个系列的前提:[只用 CSS 创建一个横向菜单](https://dev.to/cadienvan/creating-a-lateral-menu-with-css-only-3mmp)

### 走吧...

正如我在上一篇文章中所做的那样，为了简单起见，我限制了 UI 和 UX 的工作量，以提供一个简单易用、易于解释和理解的解决方案。

### HTML...

就这样，简单明了:

```
<!DOCTYPE html>
<html>
  <body>
    <!-- Our First Tab Trigger -->
    <label for="firstTab">Toggle First Tab</label>
    <!-- Our Second Tab Trigger -->
    <label for="secondTab">Toggle Second Tab</label>

    <!-- Our First Checkbox -->
    <input type="radio" class="radio-tab-toggle" name="tabTest" id="firstTab" value="1" />
    <!-- Our First Tabbed Content -->
    <div>First Tab</div>

    <!-- Our Second Checkbox -->
    <input type="radio" class="radio-tab-toggle" name="tabTest" id="secondTab" value="2" />  </body>
    <!-- Our First Tabbed Content -->
    <div>Second Tab</div>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### CSS...

CSS 可能是我写过的最短最简单的，3 条规则，我们有自己的标签:

```
.radio-tab-toggle{
  margin: -1px;
  padding: 0;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0 0 0 0);
  clip: rect(0, 0, 0, 0);
  position: absolute;
}

.radio-tab-toggle + div {
  height: 0;
  overflow: hidden;
}

.radio-tab-toggle:checked + div {
  height: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们有了它..通过点击触发标签，我们选中了不可见的复选框，从而使我们的内容可见。

## 提高一点..

我在这里看到的唯一问题是缺少“关闭当前标签”功能。事实上，据我所知，一旦你选中了一个单选按钮组的某个组件，你就不能“取消选中”它了。所以我想我们可以通过一个简单的 JS 片段来提升它。

### JS...

第三贴，第三次求。我该怎么告诉你？

```
JS = false; 
```

Enter fullscreen mode Exit fullscreen mode

### 结论...

很好的实验，但是受限于没有 JS 就无法取消选中复选框。

给我一颗心，一只独角兽，或者任何你想要的东西！回头见！