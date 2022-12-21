# 与@VX 互动中的条形图

> 原文：<https://dev.to/joelmturner/bar-chart-in-react-with-vx-bc7>

数据可视化对公司来说正变得越来越有价值，因为他们试图理解他们收到的所有数据。javaScript 中有许多不同的数据可视化解决方案， [d3](https://d3js.org/) 是最流行的一种。

在 React 中工作时，处理 d3 可能会令人沮丧，因为它们倾向于争夺 DOM。我们在 Sprinklr 使用了一个解决方案来帮助解决这个问题。

这个解决方案就是库 [@vx](https://vx-demo.now.sh/) 。它是 React 中包装 d3 的一组基本组件，用于构建图表库。组件中包含了一些很好的助手，使得使用`SVG`变得更好。它还没有作为稳定的版本发布，但是它可以满足我们的需求。

今天，我们将重点讨论如何构建一个条形图组件。以下是该组件的要求。

*   [ ]可以接受一维数据的数组
*   [ ]以共享比例呈现每个项目
*   [ ]应该有一个 x 和 y 轴

## 套餐

我们先从@vx 获取我们需要的包开始。我们需要形状、比例、轴、渐变(简单的背景色)和一些模拟数据来开始。

```
 yarn add @vx/shapes @vx/group @vx/scale @vx/axis @vx/gradient 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 npm install @vx/shapes @vx/group @vx/scale @vx/axis @vx/gradient --save 
```

Enter fullscreen mode Exit fullscreen mode

## 数据

现在我们有了自己的包，我们可以开始剔除我们的数据。我们将使用一些模拟数据开始，所以请随意创建自己的或使用这个数据集。

```
const defaultData1 = [
  {
    label: "Happy",
    value: 4000
  },
  {
    label: "Sad",
    value: 2000
  },
  {
    label: "Angry",
    value: 3000
  },
  {
    label: "Joyful",
    value: 4500
  },
  {
    label: "Anxious",
    value: 7000
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了数据的形状，我们可以添加一些帮助函数来访问这些项。这将帮助我们添加横过 x 轴的标签和沿 y 轴的值。稍后我们会看到这些是如何发挥作用的。

```
// accessors return the label and value of that data item
const x = d => d.label;
const y = d => d.value; 
```

Enter fullscreen mode Exit fullscreen mode

## 音阶

我们现在可以定义图表的最大高度和最大宽度。我们的组件将高度和宽度作为道具，然后我们可以添加一点填充。这将有助于我们定义该图表的比例。

```
// bounds
const xMax = width - 80;
const yMax = height - 80; 
```

Enter fullscreen mode Exit fullscreen mode

天平是魔法真正发生的地方。我花了一段时间才明白 d3 里的`domain`和`range`是怎么回事。根据我的理解，一般的经验法则是`domain`是最低和最高的数据点。`range`是我们想要绘制这些数据点的像素范围。

在我们下面的比例中，我们可以看到`range` ( `rangeRound`)是从`0`到`xMax`，这是我们图表的高度界限。@vx 给了我们一个助手，`rangeRound`，美化了数字。

`domain`是所有数据点的数组，解析为数据集的最低值(2000)和最高值(7000)。

padding 是来自@vx 的另一个助手，它为我们增加了条与条之间的条带或空间。

```
// scales
const xScale = scaleBand({
  rangeRound: [0, xMax],
  domain: data.map(x),
  padding: 0.4
});

const yScale = scaleLinear({
  rangeRound: [0, yMax],
  domain: [Math.max(...data.map(y)), 0]
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 条形图

酷，让我们来构建组件吧！我们将首先设置`svg`和`Group`来保存我们的图表。`Group`帮助我们放置斧头和铁条。

```
import React from "react";
import { Group } from "@vx/group";
import { LinearGradient } from "@vx/gradient";
import { scaleBand, scaleLinear } from "@vx/scale";
import { AxisLeft, AxisBottom } from "@vx/axis";

// accessors return the label and value of that data item
const x = d => d.label;
const y = d => d.value;

function BarChart({data, width, height}) {

// bounds
const xMax = width - 80;
const yMax = height - 80;

// scales
const xScale = scaleBand({
  rangeRound: [0, xMax],
  domain: data.map(x),
  padding: 0.4
});

const yScale = scaleLinear({
  rangeRound: [0, yMax],
  domain: [Math.max(...data.map(y)), 0]
});

return (
  
    <Group top={25} left={55}>
    </Group>
  
)}

export default BarChart; 
```

Enter fullscreen mode Exit fullscreen mode

*   [x]可以接受一维数据的数组

看起来不错。我们首先要添加的是 y 轴。为此，我们使用来自@vx 的`LeftAxis`。我们需要把我们的 yScale 传递给它，我们会给它一些其他的道具来进行造型。支柱`left`将轴推到足以显示`label`的位置，而`numTicks`限制 y 轴上显示的数值数量。

然后我们将把有相似道具的`AxisBottom`添加到`AxisLeft`中。它应该是这样的:

```
<Group top={25} left={55}>
  <AxisLeft left={10} scale={yScale} numTicks={4} label="Times Expressed" />
  <AxisBottom scale={xScale} label="Emotion" labelOffset={15} top={yMax} />
</Group> 
```

Enter fullscreen mode Exit fullscreen mode

*   [x]应该有一个 x 和 y 轴

现在我们可以循环数据并返回条形图。宽度、高度和 x 都使用比例来确定它们在图形中的绘制位置。

```
{data.map((d, i) => {
  const label = x(d);
  const barWidth = xScale.bandwidth();
  const barHeight = yMax - yScale(y(d));
  const barX = xScale(label);
  const barY = yMax - barHeight;

  return (
    <Bar
      key={`bar-${label}`}
      x={barX}
      y={barY}
      width={barWidth}
      height={barHeight}
    />
  );
})} 
```

Enter fullscreen mode Exit fullscreen mode

*   [x]以共享比例渲染每个项目

## 完成

不错！去了应该不错。我们还将添加背景颜色的`LinearGradient`。这里是全部:

```
import React from "react";
import { Group } from "@vx/group";
import { LinearGradient } from "@vx/gradient";
import { scaleBand, scaleLinear } from "@vx/scale";
import { AxisLeft, AxisBottom } from "@vx/axis";

// accessors return the label and value of that data item
const x = d => d.label;
const y = d => d.value;

function BarChart({data, width, height}) {

// bounds
const xMax = width - 80;
const yMax = height - 80;

// scales
const xScale = scaleBand({
  rangeRound: [0, xMax],
  domain: data.map(x),
  padding: 0.4
});

const yScale = scaleLinear({
  rangeRound: [0, yMax],
  domain: [Math.max(...data.map(y)), 0]
});

return (
  
    <LinearGradient
      from={`#e9e9e9`}
      to={`#fff`}
      id={`gradientFill`}
    />
    <rect
      width={width}
      height={height}
      fill={`url(#gradientFill)`}
      rx={5}
    />
    <Group top={25} left={55}>
      <AxisLeft left={10} scale={yScale} numTicks={4} label="Times" />
      {data.map((d, i) => {
        const label = x(d);
        const barWidth = xScale.bandwidth();
        const barHeight = yMax - yScale(y(d));
        const barX = xScale(label);
        const barY = yMax - barHeight;

        return (
          <Bar
            key={`bar-${label}`}
            x={barX}
            y={barY}
            width={barWidth}
            height={barHeight}
          />
        );
      })}
      <AxisBottom scale={xScale} label="Emotion" labelOffset={15} top={yMax} />
    </Group>
  
)}

export default BarChart; 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金

给你的条添加一点平滑的 CSS 过渡，比如:

```
.vx-bar {
  transition: height 150ms, y 150ms;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当数据改变时，它将平滑地移动到下一个高度。您可以在下面的操作中看到这一点。

[https://codesandbox.io/embed/react-bar-chart-with-vx-yi48e](https://codesandbox.io/embed/react-bar-chart-with-vx-yi48e)