# JS 中的 CSS>编写自己的引擎

> 原文：<https://dev.to/aexol/css-in-js-write-your-own-engine-3lbi>

# 这个为什么？！

现在先进的框架像 React，Vue，Angular 等等。在 JS 语法中使用 CSS。你应该在 JS minimal engine 中编写你自己的 CSS 来理解在引擎盖下发生了什么。

## Javascript

```
function o2s(o, className) {
  var elm = new Option();
  Object.keys(o).forEach(function() {
    (elm.style)[a] = o[a];
  });
  return `.${className}{\n${elm.getAttribute("style")}\n}`;
}
/**
 * Class responsible for small css functionalities rendered in HTML like menu
 *
 * @export
 * @class CSSMiniEngine
 */
export class CSSMiniEngine {
  classes = [];
  /**
   * add css class with css params
   *
   * @memberof CSSMiniEngine
   * @param {Partial<CSSStyleDeclaration>} o
   * @param {string} className
   */
  addClass = (o, className) => {
    this.classes.push(o2s(o, className));
  };
  /**
   * compile to style tag in head
   *
   * @memberof CSSMiniEngine
   */
  compile = () => {
    const head = document.head || document.getElementsByTagName("head")[0];
    const style = document.createElement("style");
    head.appendChild(style);
    style.type = "text/css";
    style.appendChild(document.createTextNode(this.classes.join("\n")));
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

## 打字稿

```
function o2s(o: Partial<CSSStyleDeclaration>, className: string) {
  var elm = new Option();
  Object.keys(o).forEach(function(a: string) {
    (elm.style as any)[a as any] = o[a as any];
  });
  return `.${className}{\n${elm.getAttribute("style")}\n}`;
}
/**
 * Class responsible for small css functionalities rendered in HTML like menu
 *
 * @export
 * @class CSSMiniEngine
 */
export class CSSMiniEngine {
  classes: string[] = [];
  /**
   * add css class with css params
   *
   * @memberof CSSMiniEngine
   * @param {Partial<CSSStyleDeclaration>} o
   * @param {string} className
   */
  addClass = (o: Partial<CSSStyleDeclaration>, className: string) => {
    this.classes.push(o2s(o, className));
  };
  /**
   * compile to style tag in head
   *
   * @memberof CSSMiniEngine
   */
  compile = () => {
    const head = document.head || document.getElementsByTagName("head")[0];
    const style = document.createElement("style");
    head.appendChild(style);
    style.type = "text/css";
    style.appendChild(document.createTextNode(this.classes.join("\n")));
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用法举例

### 实例化

```
const cssEngine = new CssMiniEngine() 
```

Enter fullscreen mode Exit fullscreen mode

### 创建类

```
const className = "MyClass"
cssMiniEngine.addClass(
      {
        visibility: "visible",
        position: "fixed",
        background: "transparent",
        border: "0",
        textAlign: "center",
      },
      className
); 
```

Enter fullscreen mode Exit fullscreen mode

### 使用它

在纯 js 中

```
const div = document.createElement("div")
div.classList.add(className) 
```

Enter fullscreen mode Exit fullscreen mode

在反应中

```
export const MyDiv = () => (
  <div className={className}>Hello</div>
) 
```

Enter fullscreen mode Exit fullscreen mode

最后调用

```
cssEngine.compile() 
```

Enter fullscreen mode Exit fullscreen mode

所以它会将你所有的类作为样式标签插入到 DOM 中
谢谢阅读！