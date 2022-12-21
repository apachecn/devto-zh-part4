# 使用钩子和类型脚本在页面上绘制 React 组件

> 原文：<https://dev.to/ankursheel/react-component-to-fraw-on-a-page-using-hooks-and-typescript-2ahp>

最近，[阿里·斯皮特尔](https://dev.to/aspittel)的[如何在开发者的离线页面](https://dev.to/aspittel/how-to-create-the-drawing-interaction-on-dev-s-offline-page-1mbe)上创建绘图交互出现在我的 feed 中，看起来很酷。这让我想知道是否可以使用 Hooks 和 typescript 创建与 React 组件相同的东西。嗯，我写这篇文章的事实意味着我能够重新创造它。让我们看看我是怎么做到的。

如果你对最终产品感兴趣，可以查看一下 [Github 资源库](https://github.com/AnkurSheel/react-drawing-interaction)。在这篇文章的最后还有一个[沙盒](#sandbox)你可以玩。

这篇文章假设你已经知道如何使用 TypeScript 和钩子。

* * *

## 创建组件

我们需要做的第一件事是创建一个**画布**组件。画布需要占用一些空间，我们希望任何父组件能够覆盖这些空间，所以我们将添加`width`和`height`作为道具。但是，我们想添加一个合理的缺省值，这样我们就不必每次想使用这个组件时都添加这些道具。我们将添加一些*默认道具*来分别设置这些值为 *window.innerWidth* 和 *window.innerHeight* 。

```
import React from 'react';

interface CanvasProps {
    width: number;
    height: number;
}

const Canvas = ({ width, height }: CanvasProps) => {
     return <canvas height={height} width={width} />; };

Canvas.defaultProps = {
    width: window.innerWidth,
    height: window.innerHeight,
};

export default Canvas; 
```

* * *

## 让我们画

因为我们需要修改 canvas 元素，所以我们需要添加一个对它的引用。我们可以通过使用`useRef` hook 并修改我们的 *canvas* 元素来设置 ref。

```
const canvasRef = useRef<HTMLCanvasElement>(null);
return <canvas ref={canvasRef} height={height} width={width} />; 
```

### 设定状态

我们需要跟踪一些变量

*   鼠标位置。
*   不管我们是不是在画画。

我们可以通过添加`useState`钩子来做到这一点。
我们还将创建一个`Coordinate`类型来帮助跟踪鼠标位置。

```
type Coordinate = {
    x: number;
    y: number;
};

const Canvas = ({ width, height }: CanvasProps) => {
const [isPainting, setIsPainting] = useState(false);
const [mousePosition, setMousePosition] = useState<Coordinate | undefined>(undefined);
// ... other stuff here 
```

#### 按下鼠标开始绘图。

我们将在`useEffect`钩子中添加事件监听器。如果我们有一个对画布的有效引用，我们将向 *mouseDown* 事件添加一个事件监听器。卸载时，我们还会删除事件侦听器。

```
 useEffect(() => {
        if (!canvasRef.current) {
            return;
        }
        const canvas: HTMLCanvasElement = canvasRef.current;
        canvas.addEventListener('mousedown', startPaint);
        return () => {
            canvas.removeEventListener('mousedown', startPaint);
        };
    }, [startPaint]); 
```

*startPaint* 需要获取鼠标的当前坐标，并将`isPainting`设置为 true。我们还将把它包在一个`useCallback`钩子中，这样我们就可以在`useEffect`钩子中使用它。

```
 const startPaint = useCallback((event: MouseEvent) => {
        const coordinates = getCoordinates(event);
        if (coordinates) {
            setIsPainting(true);
            setMousePosition(coordinates);
        }
    }, []);

// ...other stuff here

const getCoordinates = (event: MouseEvent): Coordinate | undefined => {
    if (!canvasRef.current) {
        return;
    }

    const canvas: HTMLCanvasElement = canvasRef.current;
    return {event.pageX - canvas.offsetLeft, event.pageY - canvas.offsetTop};
}; 
```

#### 鼠标移动时画线

类似于 [*mouseDown* 事件监听器](#mousedown)，我们将使用`useEffect`钩子来添加 *mousemove* 事件。

```
useEffect(() => {
        if (!canvasRef.current) {
            return;
        }
        const canvas: HTMLCanvasElement = canvasRef.current;
        canvas.addEventListener('mousemove', paint);
        return () => {
            canvas.removeEventListener('mousemove', paint);
        };
    }, [paint]); 
```

*油漆*需要

*   检查我们是否在油漆。
*   获取新的鼠标坐标。
*   通过从画布中获取渲染上下文，从旧坐标到新坐标画一条线。
*   更新旧坐标。

```
const paint = useCallback(
        (event: MouseEvent) => {
            if (isPainting) {
                const newMousePosition = getCoordinates(event);
                if (mousePosition && newMousePosition) {
                    drawLine(mousePosition, newMousePosition);
                    setMousePosition(newMousePosition);
                }
            }
        },
        [isPainting, mousePosition]
    );

// ...other stuff here

const drawLine = (originalMousePosition: Coordinate, newMousePosition: Coordinate) => {
        if (!canvasRef.current) {
            return;
        }
        const canvas: HTMLCanvasElement = canvasRef.current;
        const context = canvas.getContext('2d');
        if (context) {
            context.strokeStyle = 'red';
            context.lineJoin = 'round';
            context.lineWidth = 5;

            context.beginPath();
            context.moveTo(originalMousePosition.x, originalMousePosition.y);
            context.lineTo(newMousePosition.x, newMousePosition.y);
            context.closePath();

            context.stroke();
        }
    }; 
```

#### 松开鼠标停止绘图

我们希望当用户释放鼠标或者将鼠标移出画布区域时停止绘制

```
useEffect(() => {
        if (!canvasRef.current) {
            return;
        }
        const canvas: HTMLCanvasElement = canvasRef.current;
        canvas.addEventListener('mouseup', exitPaint);
        canvas.addEventListener('mouseleave', exitPaint);
        return () => {
            canvas.removeEventListener('mouseup', exitPaint);
            canvas.removeEventListener('mouseleave', exitPaint);
        };
    }, [exitPaint]); 
```

在 *exitPaint* 中，我们只是将`isPainting`设置为`false`

```
const exitPaint = useCallback(() => {
        setIsPainting(false);
    }, []); 
```

* * *

而且，我们有一个可以重用的 React 组件。你可以在 [Github 库](https://github.com/AnkurSheel/react-drawing-interaction)中看到最终的代码。玩下面的沙盒。

* * *

## 沙盒

[https://codesandbox.io/embed/react-drawing-interaction-bo1ph?runonclick=1](https://codesandbox.io/embed/react-drawing-interaction-bo1ph?runonclick=1)

* * *

如果你有任何问题，请留言给我:)

* * *

更新:

*   更新于 2019 年 9 月 24 日:修复了`getCoordinates`中的错误代码。谢谢季斌指出这一点。