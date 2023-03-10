# 使用 REACT PROFILER API 测量组件性能

> 原文：<https://dev.to/suryaki28658640/measuring-component-performance-using-react-profiler-api-54h>

React 应用程序中的良好性能很容易实现，也很容易错过，但错过性能漏洞可能会比我们想象的更昂贵。React 提供了一些优秀的工具来度量性能，任何可度量的东西都可以得到更好的监视和控制。

React profiler 是 Google chrome 扩展中 React devtools 的一部分，它提供了一种测量和记录应用程序性能的极好方法。在每一个组件级别，它确保了在正在进行的应用程序开发中对性能方面的了解。新的分析器提供了应用程序组件的整合视图，其中呈现了相对排名详细信息，并以不同图表的形式按提交进行分组，这些图表包括火焰图、排名图和特定组件图。

最新的 React 16.9 已经发布，其中的新特性和显著的错误修复让开发人员的工作变得更加容易。

React 的这个新版本提供了 React Profiler API，现在可以直接测量组件性能。它也是 devtools profiler 内部使用的 API。它提供了一种新的方法，通过以编程方式跟踪性能，来度量每个组件级别的性能。

借助新版本，在每个组件级别测量和控制性能变得更加容易:

渲染的频率
每次渲染的时间
初始挂载的基准时间或最坏情况下的渲染时间

在大型应用程序中，profiler API 特别有助于获得性能视图以及回归对特定组件的影响。

如何使用 REACT PROFILER API？

新的 Profiler API 可以作为附加标记包含在每个组件中，通过给它一个惟一的 id 并包含一个通用的 render 处理程序来测量它的性能。

const my grid =(props)= > {
return(

)；
}；

在任意数量的组件周围使用 Profiler 标记来测量它们的性能，分配一个唯一的 id 值以便于识别，从而有助于在 Profiler 树中唯一地跟踪每个组件。定义一个通用的处理程序，在组件每次渲染时调用，用于第一次安装和每次后续更新。这可以被定义为带有分析数据处理逻辑的公共实用函数，并且可以在分析任何组件时跨应用程序使用。

处理器接收可以适当地用于测量和跟踪组件的性能参数的剖析细节。

函数 onRenderCallback(
id，
phase，
actualDuration，
baseDuration，
startTime，
commitTime，
interactions
){
//处理分析细节的逻辑
console.log('组件'，id，'，phase，'，phase，'，更新所用的时间'，actualDuration，baseDuration，startTime，commitTime，interactions)；
}

来源:[https://walking tree . tech/measuring-component-performance-using-react-profiler-API/](https://walkingtree.tech/measuring-component-performance-using-react-profiler-api/)