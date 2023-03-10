# 如何拆分内容保留 html 格式的可编辑文本？[已解决]

> 原文：<https://dev.to/itsarnavb/how-do-you-split-contenteditable-text-preserving-html-formatting-g9d>

我使用 contenteditable divs 来表示编辑器中的段落。我已经实现了大多数常用的编辑功能

当涉及任何 HTML 格式时，拆分段落会中断。

例如，添加一个空格并按 enter 键会导致

`&`
T1】

当然， 是代表一个空格的单个字符。

## 解决方案(更新)

好吧，我想通了。

innerHTML 和 innerText 对 DOM 操作没有用。

然而，选择和范围 API 对此非常有用。`extractContents`自动拆分元素而不破坏标记。

```
function splitNode(selection, root) {
  let range = selection.getRangeAt(0);
  let {firstChild, lastChild} = root;

  let previousRange = document.createRange();
  previousRange.setStart(firstChild, 0);
  previousRange.setEnd(range.startContainer, range.startOffset);

  let nextRange = document.createRange();
  nextRange.setStart(range.endContainer, range.endOffset);
  nextRange.setEnd(lastChild, lastChild.length);
  return {
    previous: previousRange,
    current: range,
    next: nextRange,
  };
}

let ranges = splitNode(document.getSelection(), contentEditableDiv);
let nextFragment = ranges.next.extractContents(); 
```