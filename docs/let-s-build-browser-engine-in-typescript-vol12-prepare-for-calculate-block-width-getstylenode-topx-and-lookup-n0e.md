# 让我们建立浏览器引擎！在 typescript vol12 中，准备计算块宽度- getStyleNode、toPx 和 lookup

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol12-prepare-for-calculate-block-width-getstylenode-topx-and-lookup-n0e>

```
test("getStyleNode anonymous block", () => {
  expect(() => {
    LayoutBox.Create(new BoxType.AnonymousBlock()).getStyleNode();
  }).toThrow();
});

test("getStyleNode block node", () => {
  expect(LayoutBox.Create(new BoxType.BlockNode(oneStyledNode)).getStyleNode()).toEqual(
    oneStyledNode
  );
});

test("getStyleNode inline node", () => {
  expect(LayoutBox.Create(new BoxType.InlineNode(oneStyledNode)).getStyleNode()).toEqual(
    oneStyledNode
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class LayoutBox {
  (snip)

  getStyleNode(): StyledNode {
    switch (this.boxType.format) {
      case BoxType.Format.AnonymousBlock:
        throw Error("Anonymous block box has no style node");
      case BoxType.Format.BlockNode:
      case BoxType.Format.InlineNode:
        return this.boxType.styledNode;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

toPx()

```
test("value to px color", () => {
  expect(new CssValue.ColorValue(new Color(0, 0, 0, 0)).toPx()).toEqual(0.0);
});

test("value to px keyword", () => {
  expect(new CssValue.Keyword("example").toPx()).toEqual(0.0);
});

test("value to px length em", () => {
  expect(new CssValue.Length(1.0, Unit.Em).toPx()).toEqual(0.0);
});

test("value to px length px", () => {
  expect(new CssValue.Length(1.0, Unit.Px).toPx()).toEqual(1.0);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export namespace CssValue {
  export class Length {
    (snip)

    toPx(): number {
      switch (this.unit) {
        case Unit.Em:
          return 0.0;
        case Unit.Px:
          return this.length;
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

查找

```
test("lookup target", () => {
  const expected = new CssValue.Keyword("example");
  expect(
    new StyledNode(text("no mean"), new Map([["target", expected]]), []).lookup(
      "target",
      "fallback",
      new CssValue.Keyword("no mean")
    )
  ).toBe(expected);
});

test("lookup fallback", () => {
  const expected = new CssValue.Keyword("example");
  expect(
    new StyledNode(text("no mean"), new Map([["fallback", expected]]), []).lookup(
      "no mean",
      "fallback",
      new CssValue.Keyword("no mean")
    )
  ).toBe(expected);
});

test("lookup none", () => {
  const expected = new CssValue.Keyword("example");
  expect(
    new StyledNode(text("no mean"), new Map([]), []).lookup("no mean", "no mean2", expected)
  ).toBe(expected);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class StyledNode {
  (snip)

  lookup(name: string, fallbackName: string, defaultValue: CssValue): CssValue {
    return this.value(name) || this.value(fallbackName) || defaultValue;
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
*   [让我们来打造浏览器引擎吧！在 typescript vol11 中构建布局树](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol11-build-layout-tree-3gom)
*   [让我们来打造浏览器引擎吧！在 typescript vol12 中准备计算块宽度- getStyleNode、toPx 和 lookup](https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol12-prepare-for-calculate-block-width-getstylenode-topx-and-lookup-n0e)