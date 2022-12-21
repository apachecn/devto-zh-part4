# 反应:何时使用 uselayouteffect 而不是 useEffect

> 原文：<https://dev.to/oieduardorabelo/react-quando-usar-uselayouteffect-ao-inves-de-useeffect-3ihp>

反应堆中有两个*挂钩*作用**作用**和**作用**作用，它们的作用似乎基本相同。

你称呼他们的方式看起来是一样的:

```
useEffect(() => {
  // efeitos colaterais
  return () => /* limpeza */
}, [dependency, array]);

useLayoutEffect(() => {
  // efeitos colaterais
  return () => /* limpeza */
}, [dependency, array]); 
```

但它们并不完全一样。让我们看看是什么让他们不同，什么时候使用每一个。(国家警察)；大卫:大多数时候你想要的是 **useEffect**

# useeffect 与 uselayouteffect 的区别

都是关于**正确的运行时间**。

**useEffect** 异步运行，在屏幕上绘制渲染后。

这看起来像是：

1.  以某种方式进行渲染(更改状态或重新渲染父零部件)
2.  react 渲染其组件(调用其组件)
3.  萤幕会以视觉方式重新整理
4.  只有到那时才会执行 **useEffect**

**useayouteffect**另一方面，它在渲染后但在屏幕刷新之前同步运行。意思是:

1.  以某种方式进行渲染(更改状态或重新渲染父零部件)
2.  react 渲染其组件(调用其组件)
3.  **useayouteffect**正在运行，React 正在等待其结束
4.  萤幕会以视觉方式重新整理

# 99%做节拍，使用效果

在大多数情况下，其效果会使某些状态或对象与不需要立即发生或在视觉上不会影响页面的内容同步。

例如，如果您正在服务器上搜索数据，则不会导致立即更改。

或者，如果您正在配置事件处理程序。

或在模式对话框出现或消失时重置任何状态。

多数情况下， **useEffect** 是正确的道路。

# Quando usarLayoutEffect

使用**useayouteffect**的正确时间是什么？你看着就知道了。从字面上来说；）

如果组件在状态更新时闪烁-例如在中，它首先在部分就绪状态下进行处理，然后在最终状态下重新渲染-这是一个很好的提示，表明该切换回“**useayouteffect”**。

这里有一个(人工的)例子，让你明白我的意思。

当您单击页面(【a】)时，状态会立即更改(T2【值】重置为 0)，重新渲染组件，然后执行效果-将值设置为任意数字并重新渲染。

因此，两个渲染将快速连续进行。

```
import React, {
  useState,
  useLayoutEffect
} from 'react';
import ReactDOM from 'react-dom';

const BlinkyRender = () => {
  const [value, setValue] = useState(0);

  useLayoutEffect(() => {
    if (value === 0) {
      setValue(10 + Math.random() * 200);
    }
  }, [value]);

  console.log('render', value);

  return (
    <div onClick={() => setValue(0)}>
      value: {value}
    </div>
  );
};

ReactDOM.render(
  <BlinkyRender />,
  document.querySelector('#root')
); 
```

**【a】:**一般情况下，将**【onclick】**处理程序放在沙发上不利于无障碍(使用按钮！)，这是一次性的演示。我只是想提一下！

尝试两种版本:

*   [含 uselayouteffect 的版本-渲染不闪烁](https://codesandbox.io/s/uselayouteffect-no-flash-ylyyg)
*   [具有 useEffect 的版本-闪烁渲染](https://codesandbox.io/s/useeffect-flash-on-render-yluoi)

请注意，即使组件已渲染两次，版本**useayouteffect**也仅在视觉上更新一次。另一方面，版本 **useEffect** 会以视觉方式呈现两次，您可以在其中快速看到值 0。

# 我应该使用效果还是使用布局效果？

多数情况下，**use effect**是正确的选择。如果您的代码在渲染过程中引起振荡，请切换到“**useayouteffect”**，看看这是否有帮助。

由于“**useayouteffect”**是同步的(阻止渲染)，因此在效果完成运行之前，应用程序不会直观地更新-我...。如果在效果上代码较慢，则可能会导致性能问题。再加上大部分的效果并不需要世界在它们发生的时候停下来。

# 克雷蒂托

*   [何时使用 LayoutEffect 而不是 useEffect](https://daveceddia.com/useeffect-vs-uselayouteffect/?ck_subscriber_id=419003618) ，escrito origination por[戴夫·塞德迪亚](https://twitter.com/dceddia)