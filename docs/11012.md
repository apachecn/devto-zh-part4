# 风格-成分和治疗

> 原文：<https://dev.to/stereobooster/styled-components-and-treat-2gjb>

可以说，临摹是一种更高形式的欣赏。这里，我第三次实现了样式化组件“模式”:

1.  [CSS-模块-组件](https://github.com/stereobooster/css-modules-components)
2.  [无样式组件](https://github.com/stereobooster/unstyled-components)
3.  [反应-治疗-风格](https://github.com/stereobooster/treat/tree/react-treat-styled/packages/react-treat-styled)

这次我为 [treat](https://seek-oss.github.io/treat/) 实现了样式化组件(更像是概念证明，而不是最终版本)。Treat 是可主题化的、静态提取的 CSS-in-JS，运行时间接近于零。还有其他静态提取的 CSS-in-JS 解决方案，如 [astroturf](https://github.com/4Catalyzer/astroturf) 和 [linaria](https://github.com/callstack/linaria) 。这些解决方案的主要问题是，它们不能与 CRA 开箱即用。treat 对 CRA 还不起作用，但是还有希望。

## 代码示例

下面是[示例 repo](https://github.com/stereobooster/react-treat-demo/tree/react-treat-styled) ，演示如何使用。

你像这样声明样式:

```
import { style } from "treat";

export const base = style({
  outline: "none",
  padding: "0.325rem 0.625rem"
});

export const themed = style(({ primary }) => ({
  background: primary
}));

export default [base, themed]; 
```

*   它有 TypeScript 支持，所以会抱怨非法 CSS。
*   它和 CSS 模块有相同的好处
*   它支持主题化

组件本身看起来是这样的:

```
import { styled } from "@stereobooster/react-treat-styled";
import styles from "./button.treat";

const Button = styled.button(styles);

const App = () => <Button onClick={() => alert("test") }></Button> 
```

下面是使用 treat 的权衡。我还没有在实际项目中尝试过，但是让我们看看。

你觉得请客怎么样？