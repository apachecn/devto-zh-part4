# 玩具浏览器引擎当前 WIP 状态

> 原文：<https://dev.to/sanemat/wip-toy-browser-engine-current-status-4f4n>

# 目标:了解如何将 html 和 css 转化为图像。

如果我写简单的 html 和 css:

```
<html>
  <body>
    <div class="outer">
      <div class="inner" />
    <div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
* { display: block; padding: 12px; }
.outer { background: #800000; }
.inner { background: #0000ff; } 
```

Enter fullscreen mode Exit fullscreen mode

我执行这个:

```
node_modules/.bin/ts-node example/toy-engine-cli.ts --css=index.css --html=index.html | display 
```

Enter fullscreen mode Exit fullscreen mode

我会得到这个图像！

[![](img/aaf55451f83d92901a1191ce7b3ac215.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3_TjoLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pa1z12o4e3ryrtitrc1q.png)

## 当前状态

```
node_modules/.bin/ts-node example/demo.ts | display 
```

Enter fullscreen mode Exit fullscreen mode

有用！

[![](img/aaf55451f83d92901a1191ce7b3ac215.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3_TjoLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pa1z12o4e3ryrtitrc1q.png)

回头看看管道。

[![](img/53eef37d34715810f94e69d97ac35672.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B5rasokw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ti6rl65eya6sm4xd5cbn.png)

[https://github.com/sanemat/ts-toy-engine](https://github.com/sanemat/ts-toy-engine)
T3】https://dev . to/sane mat/let-s-build-browser-engine-in-typescript-vol 0-toy-browser-engine-EGM

### HTML -(HTML 解析器)- > DOM

还没有！

#### 快捷方式

```
// <html>
//   <body>
//     <div class="outer">
//       <div class="inner" />
//     <div>
//   </body>
// </html>

const domNode = elem("html", new Map([]), []);
const body = elem("body", new Map([]), []);
const outer = elem("div", new Map([["class", "outer"]]), []);
const inner = elem("div", new Map([["class", "inner"]]), []);
domNode.children.push(body);
body.children.push(outer);
outer.children.push(inner);

// console.dir(domNode, { depth: null });
//
// DomNode {
//   children: [
//     DomNode {
//       children: [
//         DomNode {
//           children: [
//             DomNode {
//               children: [],
//               nodeType: Element {
//                 format: 0,
//                 element: ElementData {
//                   tagName: 'div',
//                   attributes: Map { 'class' => 'inner' }
//                 }
//               }
//             }
//           ],
//           nodeType: Element {
//             format: 0,
//             element: ElementData {
//               tagName: 'div',
//               attributes: Map { 'class' => 'outer' }
//             }
//           }
//         }
//       ],
//       nodeType: Element {
//         format: 0,
//         element: ElementData { tagName: 'body', attributes: Map {} }
//       }
//     }
//   ],
//   nodeType: Element {
//     format: 0,
//     element: ElementData { tagName: 'html', attributes: Map {} }
//   }
// } 
```

Enter fullscreen mode Exit fullscreen mode

### 【CSS-(CSS 解析器)- >规则

还没有！

#### 快捷方式

```
// * { display: block; padding: 12px; }
// .outer { background: maroon; /* 800000 */ }
// .inner { background: blue; /* 0000ff */ }

const stylesheet = new Stylesheet([]);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, []))],
    [
      new Declaration("display", new CssValue.Keyword("block")),
      new Declaration("padding", new CssValue.Length(12, Unit.Px))
    ]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, ["outer"]))],
    [new Declaration("background", new CssValue.ColorValue(new Color(128, 0, 0, 255)))]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, ["inner"]))],
    [new Declaration("background", new CssValue.ColorValue(new Color(0, 0, 255, 255)))]
  )
);

// console.dir(stylesheet, { depth: null }); 
```

Enter fullscreen mode Exit fullscreen mode

### DOM+规则- >风格树

```
const styleRoot = styleTree(domNode, stylesheet);
// console.dir(styleRoot, { depth: null });
// Stylesheet {
//   rules: [
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector { tagName: null, id: null, classValue: [] }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'display',
//           value: Keyword { format: 0, keyword: 'block' }
//         },
//         Declaration {
//           name: 'padding',
//           value: Length { format: 1, length: 12, unit: 0 }
//         }
//       ]
//     },
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector {
//             tagName: null,
//             id: null,
//             classValue: [ 'outer' ]
//           }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'background',
//           value: ColorValue {
//             format: 2,
//             colorValue: Color { r: 128, g: 0, b: 0, a: 255 }
//           }
//         }
//       ]
//     },
//     Rule {
//       selectors: [
//         Simple {
//           format: 0,
//           selector: SimpleSelector {
//             tagName: null,
//             id: null,
//             classValue: [ 'inner' ]
//           }
//         }
//       ],
//       declarations: [
//         Declaration {
//           name: 'background',
//           value: ColorValue {
//             format: 2,
//             colorValue: Color { r: 0, g: 0, b: 255, a: 255 }
//           }
//         }
//       ]
//     }
//   ]
// } 
```

Enter fullscreen mode Exit fullscreen mode

### 风格树- >布局树

```
const viewport = new Dimensions(
  new Rect(0, 0, 200, 100),
  new EdgeSizes(0, 0, 0, 0),
  new EdgeSizes(0, 0, 0, 0),
  new EdgeSizes(0, 0, 0, 0)
);

const layoutRoot = layoutTree(styleRoot, viewport);
// console.dir(layoutRoot, { depth: null });
// LayoutBox {
//   dimensions: Dimensions {
//     content: Rect { x: 12, y: 12, width: 176, height: 72 },
//     padding: EdgeSizes { left: 12, right: 12, top: 12, bottom: 12 },
//     border: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 },
//     margin: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 }
//   },
//   boxType: BlockNode {
//     format: 0,
//     styledNode: StyledNode {
//       node: DomNode {
//         children: [
//           DomNode {
//             children: [
//               DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               }
//             ],
//             nodeType: Element {
//               format: 0,
//               element: ElementData { tagName: 'body', attributes: Map {} }
//             }
//           }
//         ],
//         nodeType: Element {
//           format: 0,
//           element: ElementData { tagName: 'html', attributes: Map {} }
//         }
//       },
//       specifiedValues: Map {
//         'display' => Keyword { format: 0, keyword: 'block' },
//         'padding' => Length { format: 1, length: 12, unit: 0 }
//       },
//       children: [
//         StyledNode {
//           node: DomNode {
//             children: [
//               DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               }
//             ],
//             nodeType: Element {
//               format: 0,
//               element: ElementData { tagName: 'body', attributes: Map {} }
//             }
//           },
//           specifiedValues: Map {
//             'display' => Keyword { format: 0, keyword: 'block' },
//             'padding' => Length { format: 1, length: 12, unit: 0 }
//           },
//           children: [
//             StyledNode {
//               node: DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               },
//               specifiedValues: Map {
//                 'display' => Keyword { format: 0, keyword: 'block' },
//                 'padding' => Length { format: 1, length: 12, unit: 0 },
//                 'background' => ColorValue {
//                   format: 2,
//                   colorValue: Color { r: 128, g: 0, b: 0, a: 255 }
//                 }
//               },
//               children: [
//                 StyledNode {
//                   node: DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   },
//                   specifiedValues: Map {
//                     'display' => Keyword { format: 0, keyword: 'block' },
//                     'padding' => Length { format: 1, length: 12, unit: 0 },
//                     'background' => ColorValue {
//                       format: 2,
//                       colorValue: Color { r: 0, g: 0, b: 255, a: 255 }
//                     }
//                   },
//                   children: []
//                 }
//               ]
//             }
//           ]
//         }
//       ]
//     }
//   },
//   children: [
//     LayoutBox {
//       dimensions: Dimensions {
//         content: Rect { x: 24, y: 24, width: 152, height: 48 },
//         padding: EdgeSizes { left: 12, right: 12, top: 12, bottom: 12 },
//         border: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 },
//         margin: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 }
//       },
//       boxType: BlockNode {
//         format: 0,
//         styledNode: StyledNode {
//           node: DomNode {
//             children: [
//               DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               }
//             ],
//             nodeType: Element {
//               format: 0,
//               element: ElementData { tagName: 'body', attributes: Map {} }
//             }
//           },
//           specifiedValues: Map {
//             'display' => Keyword { format: 0, keyword: 'block' },
//             'padding' => Length { format: 1, length: 12, unit: 0 }
//           },
//           children: [
//             StyledNode {
//               node: DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               },
//               specifiedValues: Map {
//                 'display' => Keyword { format: 0, keyword: 'block' },
//                 'padding' => Length { format: 1, length: 12, unit: 0 },
//                 'background' => ColorValue {
//                   format: 2,
//                   colorValue: Color { r: 128, g: 0, b: 0, a: 255 }
//                 }
//               },
//               children: [
//                 StyledNode {
//                   node: DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   },
//                   specifiedValues: Map {
//                     'display' => Keyword { format: 0, keyword: 'block' },
//                     'padding' => Length { format: 1, length: 12, unit: 0 },
//                     'background' => ColorValue {
//                       format: 2,
//                       colorValue: Color { r: 0, g: 0, b: 255, a: 255 }
//                     }
//                   },
//                   children: []
//                 }
//               ]
//             }
//           ]
//         }
//       },
//       children: [
//         LayoutBox {
//           dimensions: Dimensions {
//             content: Rect { x: 36, y: 36, width: 128, height: 24 },
//             padding: EdgeSizes { left: 12, right: 12, top: 12, bottom: 12 },
//             border: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 },
//             margin: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 }
//           },
//           boxType: BlockNode {
//             format: 0,
//             styledNode: StyledNode {
//               node: DomNode {
//                 children: [
//                   DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   }
//                 ],
//                 nodeType: Element {
//                   format: 0,
//                   element: ElementData {
//                     tagName: 'div',
//                     attributes: Map { 'class' => 'outer' }
//                   }
//                 }
//               },
//               specifiedValues: Map {
//                 'display' => Keyword { format: 0, keyword: 'block' },
//                 'padding' => Length { format: 1, length: 12, unit: 0 },
//                 'background' => ColorValue {
//                   format: 2,
//                   colorValue: Color { r: 128, g: 0, b: 0, a: 255 }
//                 }
//               },
//               children: [
//                 StyledNode {
//                   node: DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   },
//                   specifiedValues: Map {
//                     'display' => Keyword { format: 0, keyword: 'block' },
//                     'padding' => Length { format: 1, length: 12, unit: 0 },
//                     'background' => ColorValue {
//                       format: 2,
//                       colorValue: Color { r: 0, g: 0, b: 255, a: 255 }
//                     }
//                   },
//                   children: []
//                 }
//               ]
//             }
//           },
//           children: [
//             LayoutBox {
//               dimensions: Dimensions {
//                 content: Rect { x: 48, y: 48, width: 104, height: 0 },
//                 padding: EdgeSizes { left: 12, right: 12, top: 12, bottom: 12 },
//                 border: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 },
//                 margin: EdgeSizes { left: 0, right: 0, top: 0, bottom: 0 }
//               },
//               boxType: BlockNode {
//                 format: 0,
//                 styledNode: StyledNode {
//                   node: DomNode {
//                     children: [],
//                     nodeType: Element {
//                       format: 0,
//                       element: ElementData {
//                         tagName: 'div',
//                         attributes: Map { 'class' => 'inner' }
//                       }
//                     }
//                   },
//                   specifiedValues: Map {
//                     'display' => Keyword { format: 0, keyword: 'block' },
//                     'padding' => Length { format: 1, length: 12, unit: 0 },
//                     'background' => ColorValue {
//                       format: 2,
//                       colorValue: Color { r: 0, g: 0, b: 255, a: 255 }
//                     }
//                   },
//                   children: []
//                 }
//               },
//               children: []
//             }
//           ]
//         }
//       ]
//     }
//   ]
// } 
```

Enter fullscreen mode Exit fullscreen mode

### 布局树- >图片

完成的

## 代码

```
// node_modules/.bin/ts-node example/demo.ts | display
import { elem } from "../src/dom";
import {
  Color,
  CssValue,
  Declaration,
  Rule,
  Selector,
  SimpleSelector,
  Stylesheet,
  Unit
} from "../src/css";
import { styleTree } from "../src/style";
import { Dimensions, EdgeSizes, layoutTree, Rect } from "../src/layout";
import { paint } from "../src/painting";
import * as Jimp from "jimp";

// <html>
//   <body>
//     <div class="outer">
//       <div class="inner" />
//     <div>
//   </body>
// </html>

// * { display: block; padding: 12px; }
// .outer { background: maroon; /* 800000 */ }
// .inner { background: blue; /* 0000ff */ }

const domNode = elem("html", new Map([]), []);
const body = elem("body", new Map([]), []);
const outer = elem("div", new Map([["class", "outer"]]), []);
const inner = elem("div", new Map([["class", "inner"]]), []);
domNode.children.push(body);
body.children.push(outer);
outer.children.push(inner);

const stylesheet = new Stylesheet([]);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, []))],
    [
      new Declaration("display", new CssValue.Keyword("block")),
      new Declaration("padding", new CssValue.Length(12, Unit.Px))
    ]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, ["outer"]))],
    [new Declaration("background", new CssValue.ColorValue(new Color(128, 0, 0, 255)))]
  )
);
stylesheet.rules.push(
  new Rule(
    [new Selector.Simple(new SimpleSelector(null, null, ["inner"]))],
    [new Declaration("background", new CssValue.ColorValue(new Color(0, 0, 255, 255)))]
  )
);

const styleRoot = styleTree(domNode, stylesheet);
const viewport = new Dimensions(
  new Rect(0, 0, 200, 100),
  new EdgeSizes(0, 0, 0, 0),
  new EdgeSizes(0, 0, 0, 0),
  new EdgeSizes(0, 0, 0, 0)
);

const layoutRoot = layoutTree(styleRoot, viewport);
const canvas = paint(layoutRoot, viewport.content);
Jimp.create(canvas.width, canvas.height)
  .then(value => {
    let buffer = value.bitmap.data;
    for (let i = 0; i < canvas.pixels.length; i++) {
      buffer[i * 4] = canvas.pixels[i].r;
      buffer[i * 4 + 1] = canvas.pixels[i].g;
      buffer[i * 4 + 2] = canvas.pixels[i].b;
      buffer[i * 4 + 3] = canvas.pixels[i].a;
    }
    return value.getBufferAsync(Jimp.MIME_PNG);
  })
  .then(value => {
    process.stdout.write(value);
  })
  .catch(error => {
    console.error(error);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 下一个动作

*   HTML -(HTML 解析器)-> DOM
*   CSS -(CSS 解析器)->规则