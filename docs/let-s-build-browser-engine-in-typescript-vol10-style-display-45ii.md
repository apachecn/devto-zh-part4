# 让我们建立浏览器引擎！在 typescript vol10 样式显示中

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol10-style-display-45ii>

```
test("style node display inline default", () => {
  expect(new StyledNode(elem("no mean", new Map([]), []), new Map([]), []).display()).toBe(
    Display.Inline
  );
});

test("style node display none", () => {
  expect(
    new StyledNode(
      elem("no mean", new Map([["id", "target"]]), []),
      new Map([["display", new CssValue.Keyword("none")]]),
      []
    ).display()
  ).toBe(Display.None);
});

test("style node display block", () => {
  expect(
    new StyledNode(
      elem("no mean", new Map([["id", "target"]]), []),
      new Map([["display", new CssValue.Keyword("block")]]),
      []
    ).display()
  ).toBe(Display.Block);
});

test("style node display inline", () => {
  expect(
    new StyledNode(
      elem("no mean", new Map([["id", "target"]]), []),
      new Map([["display", new CssValue.Keyword("no mean")]]),
      []
    ).display()
  ).toBe(Display.Inline);
});

test("style node display inline2", () => {
  expect(
    new StyledNode(
      elem("no mean", new Map([["id", "target"]]), []),
      new Map([["display", new CssValue.ColorValue(new Color(0, 0, 0, 0))]]),
      []
    ).display()
  ).toBe(Display.Inline);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
 export enum Display {
  Inline,
  Block,
  None
}

export class StyledNode {
  (snip)

  display(): Display {
    const displayValue = this.value("display");
    if (displayValue === null) {
      return Display.Inline;
    }
    switch (displayValue.format) {
      case CssValue.Format.Keyword:
        switch (displayValue.keyword) {
          case "block":
            return Display.Block;
          case "none":
            return Display.None;
          default:
            return Display.Inline;
        }
    }
    return Display.Inline;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [我们来搭建一个浏览器引擎吧！第 1 部分:入门](https://limpet.net/mbrubeck/2014/08/08/toy-layout-engine-1.html)
*   [姆布鲁贝克/罗宾逊](https://github.com/mbrubeck/robinson)
*   [sanemat/js-toy-engine](https://github.com/sanemat/js-toy-engine)
*   [sanemat/ts-toy-engine](https://github.com/sanemat/ts-toy-engine)

## 系列

*   [让我们来打造浏览器引擎吧！在 typescript vol0 玩具浏览器引擎](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol0-toy-browser-engine-egm)
*   [让我们来打造浏览器引擎吧！在 typescript vol1 画布上用颜色](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol1-canvas-with-color-2nd7)
*   [让我们来打造浏览器引擎吧！在 typescript vol2 中显示命令](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol2-display-command-3466)
*   [让我们来打造浏览器引擎吧！在 typescript vol3 布局框中，尺寸](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol3-layout-box-dimensions-4613)
*   [让我们来打造浏览器引擎吧！在 typescript vol4 布局树中显示列表](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol4-layout-tree-to-display-list-c54)
*   [让我们来打造浏览器引擎吧！在 typescript vol5 中 DOM 快捷方式](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol5-dom-shortcut-2l81)
*   [让我们来打造浏览器引擎吧！在 typescript vol6 CSS 快捷方式](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol6-css-shortcut-1958)
*   [让我们来打造浏览器引擎吧！在 typescript vol7 选择器中匹配](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol7-selector-matching-1faf)
*   [让我们来打造浏览器引擎吧！在打字稿 vol8 特异性](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol8-specificity-1c4c)
*   [让我们来打造浏览器引擎吧！在 typescript vol9 中 DOM 和规则到样式树](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol9-dom-and-rules-to-style-tree-18kp)
*   [让我们来打造浏览器引擎吧！在打字稿中 vol10 风格显示](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol10-style-display-45ii)