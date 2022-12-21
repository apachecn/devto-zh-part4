# React Tldr 明智地使用 React.memo()

> 原文：<https://dev.to/maniekm/react-tldr-use-react-memo-wisely-506n>

在以下情况下使用 React.memo()。

*   您希望在函数组件中模仿 PureComponent。
*   您的组件经常呈现
*   在重新渲染过程中，通常会为组件提供相同的道具
*   你的组件包含了相当多的 UI 元素来推理道具的检查

来源:https://dmitripavlutin.com/use-react-memo-wisely/