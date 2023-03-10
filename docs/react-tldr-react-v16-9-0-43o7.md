# React TldrReact v16.9.0

> 原文：<https://dev.to/maniekm/react-tldr-react-v16-9-0-43o7>

新反作用:

*   现在，当使用任何旧的不安全生命周期方法名称时，您会看到一条警告
*   使用“JavaScript:”URL 现在会记录一个警告
*   不支持使用渲染方法返回对象的“工厂”组件

新功能:

*   act()测试实用程序也接受异步函数
*   收集测量值的编程方式称为——测量应用程序渲染的频率以及渲染的“成本”

错误修复:

*   修复了在树中调用 findDOMNode()时的崩溃。
*   保留已删除的子树导致的内存泄漏也已修复。
*   useEffect 中的 setState 导致的无限循环现在会记录一个错误。(这类似于在类中调用 componentDidUpdate 中的 setState 时看到的错误。)

来源:https://reactjs.org/blog/2019/08/08/react-v16.9.0.html