# 反应钩子:使用滑块

> 原文：<https://dev.to/joelmturner/react-hooks-useslider-23em>

我们需要一个自动推进的反应图像滑块。我选择使用钩子来实现这个特性。这个勾手利用了丹·阿布瑞莫夫的一个`useInterval`勾手。

## 要求

这个组件需要做一些事情。

*   []应该接受一组幻灯片
*   []应该接受以毫秒为单位的持续时间
*   []应该在幻灯片之间播放动画
*   []应该自己在阵列中移动

## 多个间隔

下面是`useInterval`代码。

```
import React, { useState, useEffect, useRef } from 'react';

function useInterval(callback, delay) {
  const savedCallback = useRef();

  // Remember the latest callback.
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Set up the interval.
  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    if (delay !== null) {
      let id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
} 
```

在 JavaScript 中设置时间间隔可能会有问题，主要是因为清理(或缺乏清理)。使用`useEffect`，我们用返回函数`return () => clearInterval(id);`得到了一个很好的清理。

## 使用滑块

现在我们已经设置好了，我们可以利用它来帮助我们掌握时间。

```
import * as React from 'react';
import useInterval from './UseInterval';

function useSlider({
    total = 0, // the length of the slide array
    enabled = false, // pauses/disables the player
    useLoaded = false, // this allows for delayed loads like images or embeds
    speed = 1000, // speed in milliseconds to show each slide
    loop = true, // should it start back at the beginning
}) {
    const [offset, setOffset] = React.useState(0);
    const [items, setItems] = React.useState([]);

    function incrementOffset() {
        if (offset === total - 1) {
            setOffset(loop ? 0 : offset);
        } else {
            setOffset(offset + 1);
        }
    }

    function addItem(ref) {
        setItems([...items, ref]);
    }

    const loaded = useLoaded ? items.length === total : true;

    useInterval(() => {
        if (loaded && enabled && offset < total) {
            incrementOffset();
        }
    }, speed);

    return {
      offset, // this is the current index of the slider
      addItem // this takes a ref and adds it to the items array to see if all have loaded
    };
}

export default useSlider; 
```

## 滑块组件

我们的 slider 组件将所有幻灯片一个接一个地加起来，并将`.scroller`(绝对定位)移动到`.container`(相对定位)。这允许我们在幻灯片之间制作动画。这是我们组件的无状态结构。

```
.container {
  background-color: #ccc;
  margin: 0 auto;
  position: relative;
  overflow: hidden;
}

.scroller {
  position: absolute;
  transition: transform 350ms;
  height: 100%;
  display: flex;
}

.slide {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  transition: opacity 350ms;
} 
```

```
import React from "react";
import useSlider from "./useSlider";

const slides = [
  {
    title: "Slide 1",
    color: "#56777A"
  },
  {
    title: "Slide 2",
    color: "#84ACAC"
  },
  {
    title: "Slide 3",
    color: "#FBA434"
  }
];

function Slider() {

  const slideWidth = 300;

  return (
    <div
      className="container"
      style={{
        backgroundColor: slide.color,
        width: slideWidth,
        height: slideWidth
      }}
      >
      <div
        className="scroller"
        style={{
          // our counter offset will go here
          transform: `translate3d(-${offset * slideWidth}px,0,0)`,
          width: `${slides.length * slideWidth}px`
        }}
      >
        {slides.map((slide, index) => (
          <div
            key={slide.title}
            className="slide"
            style={{
              backgroundColor: slide.color,
              width: slideWidth,
              height: slideWidth
            }}
          >
            {slide.title}
          </div>
        ))}
      </div>
    </div>
  );
} 
```

## 把所有的东西放在一起

现在，我们可以将挂钩添加到滑块组件中。这将为我们提供该特性所需的所有状态。当它们都在一起时，我们得到一个滑块，它可以水平移动幻灯片，并在最后一张幻灯片后倒带。如果需要，您可以连接滑块道具来管理滑块选项。稍加修改也可以做成垂直的。

[https://codesandbox.io/embed/simple-react-hooks-interval-slider-vsmi1](https://codesandbox.io/embed/simple-react-hooks-interval-slider-vsmi1)

酷！符合要求。

*   [x]应该接受一组幻灯片
*   [x]应该接受以毫秒为单位的持续时间
*   [x]应该在幻灯片之间播放动画
*   [x]应该自己在数组中移动