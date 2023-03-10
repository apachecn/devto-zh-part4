# 让我们建立浏览器引擎！在 typescript vol11 中构建布局树

> 原文：<https://dev.to/sanemat/let-s-build-browser-engine-in-typescript-vol11-build-layout-tree-3gom>

我不确定，我得用哪种方法来保持节点平衡。

```
test("LayoutBox#getInlineContainer anonymous block", () => {
  const layoutBoxAnonymousBlock = new LayoutBox(
    new Dimensions(
      new Rect(1, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0)
    ),
    new BoxType.AnonymousBlock(),
    []
  );
  expect(layoutBoxAnonymousBlock.getInlineContainer()).toEqual(layoutBoxAnonymousBlock);
});

test("LayoutBox#getInlineContainer inline node", () => {
  const layoutBoxInlineNode = new LayoutBox(
    new Dimensions(
      new Rect(1, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0)
    ),
    new BoxType.InlineNode(oneStyledNode),
    []
  );
  expect(layoutBoxInlineNode.getInlineContainer()).toEqual(layoutBoxInlineNode);
});

test("LayoutBox#getInlineContainer block node 1", () => {
  const layoutBoxAnonymousBlock = new LayoutBox(
    new Dimensions(
      new Rect(1, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0)
    ),
    new BoxType.AnonymousBlock(),
    []
  );
  const layoutBoxBlockNode = new LayoutBox(
    new Dimensions(
      new Rect(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0)
    ),
    new BoxType.BlockNode(oneStyledNode),
    [layoutBoxAnonymousBlock]
  );
  expect(layoutBoxBlockNode.getInlineContainer()).toEqual(layoutBoxAnonymousBlock);
});

test("LayoutBox#getInlineContainer block node 2", () => {
  const layoutBoxBlockNode = new LayoutBox(
    new Dimensions(
      new Rect(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0),
      new EdgeSizes(0, 0, 0, 0)
    ),
    new BoxType.BlockNode(oneStyledNode),
    []
  );
  expect(layoutBoxBlockNode.getInlineContainer()).toEqual(
    LayoutBox.Create(new BoxType.AnonymousBlock())
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
export class LayoutBox {
  (snip)

  // NOTE: This may modify this.children :trollface:
  getInlineContainer(): LayoutBox {
    switch (this.boxType.format) {
      case BoxType.Format.AnonymousBlock:
      case BoxType.Format.InlineNode:
        return this;
      case BoxType.Format.BlockNode:
        if (
          this.children.length === 0 ||
          this.children[this.children.length - 1].boxType.format !== BoxType.Format.AnonymousBlock
        ) {
          this.children.push(LayoutBox.Create(new BoxType.AnonymousBlock()));
        }
        return this.children[this.children.length - 1];
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
test("buildLayoutTree 1", () => {
  const displayNone = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("none")]]),
    []
  );
  expect(() => {
    buildLayoutTree(displayNone);
  }).toThrow();
});

test("buildLayoutTree 2", () => {
  const displayBlock = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("block")]]),
    []
  );
  expect(buildLayoutTree(displayBlock)).toEqual(
    LayoutBox.Create(new BoxType.BlockNode(displayBlock))
  );
});

test("buildLayoutTree 3", () => {
  const displayInline = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("no mean")]]),
    []
  );
  expect(buildLayoutTree(displayInline)).toEqual(
    LayoutBox.Create(new BoxType.InlineNode(displayInline))
  );
});

test("buildLayoutTree 4", () => {
  const childDisplayBlock = new StyledNode(
    elem("no mean", new Map([["id", "target2"]]), []),
    new Map([["display", new CssValue.Keyword("block")]]),
    []
  );
  const displayBlock = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("block")]]),
    [childDisplayBlock]
  );
  expect(buildLayoutTree(displayBlock)).toEqual(
    new LayoutBox(
      new Dimensions(
        new Rect(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0)
      ),
      new BoxType.BlockNode(displayBlock),
      [LayoutBox.Create(new BoxType.BlockNode(childDisplayBlock))]
    )
  );
});

test("buildLayoutTree 5", () => {
  const childDisplayInline = new StyledNode(
    elem("no mean", new Map([["id", "target2"]]), []),
    new Map([["display", new CssValue.Keyword("no mean")]]),
    []
  );
  const displayBlock = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("block")]]),
    [childDisplayInline]
  );
  expect(buildLayoutTree(displayBlock)).toEqual(
    new LayoutBox(
      new Dimensions(
        new Rect(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0)
      ),
      new BoxType.BlockNode(displayBlock),
      [
        new LayoutBox(
          new Dimensions(
            new Rect(0, 0, 0, 0),
            new EdgeSizes(0, 0, 0, 0),
            new EdgeSizes(0, 0, 0, 0),
            new EdgeSizes(0, 0, 0, 0)
          ),
          new BoxType.AnonymousBlock(),
          [LayoutBox.Create(new BoxType.InlineNode(childDisplayInline))]
        )
      ]
    )
  );
});

test("buildLayoutTree 6", () => {
  const childDisplayNone = new StyledNode(
    elem("no mean", new Map([["id", "target2"]]), []),
    new Map([["display", new CssValue.Keyword("none")]]),
    []
  );
  const displayBlock = new StyledNode(
    elem("no mean", new Map([["id", "target"]]), []),
    new Map([["display", new CssValue.Keyword("block")]]),
    [childDisplayNone]
  );
  expect(buildLayoutTree(displayBlock)).toEqual(
    new LayoutBox(
      new Dimensions(
        new Rect(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0),
        new EdgeSizes(0, 0, 0, 0)
      ),
      new BoxType.BlockNode(displayBlock),
      []
    )
  );
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
 export function buildLayoutTree(styleNode: StyledNode): LayoutBox {
  let root: LayoutBox;
  switch (styleNode.display()) {
    case Display.None: // NOTE: I'm not sure "Uncovered Line"
      throw Error("Root node has display: none.");
    case Display.Block:
      root = LayoutBox.Create(new BoxType.BlockNode(styleNode));
      break;
    case Display.Inline:
      root = LayoutBox.Create(new BoxType.InlineNode(styleNode));
      break;
    default:
      /* istanbul ignore next */
      throw Error("never");
  }

  for (const child of styleNode.children) {
    switch (child.display()) {
      case Display.Inline:
        root.getInlineContainer().children.push(buildLayoutTree(child));
        break;
      case Display.Block:
        root.children.push(buildLayoutTree(child));
        break;
      case Display.None:
        // do nothing
        break;
    }
  }
  return root;
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