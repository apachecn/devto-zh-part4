# 一个棘手的小测验

> 原文：<https://dev.to/vhoyer/a11y-tricky-quiz-2lci>

考虑下面的标记，屏幕阅读器会阅读哪个文本？

```
<html lang="en">
  <label for="btn">button this click</label>

  <span id="alt-btn">me button click</span>

  <button
    id="btn"
    aria-labelledby="alt-btn"
    aria-label="click this button"
  >
    click me
  </button>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

*   "点击按钮"
*   "点击我"
*   "单击此按钮按钮"
*   "我的按钮点击按钮"

[点击此处查看答案](https://dev.to/vhoyer/answer-a11y-tricky-quiz-2a95-temp-slug-3736491?preview=a823d9b8d081edcb2e65fb681fb6c84a32cd1614af49e2ad7a67e5f355967ec9d9fc387c38eb8fce9fee6ae7de33779fa72c2fbfa60f07bec5a66d64)