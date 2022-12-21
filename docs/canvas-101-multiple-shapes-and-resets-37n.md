# 画布 101:多种形状和重置

> 原文：<https://dev.to/brianmontanaweb/canvas-101-multiple-shapes-and-resets-37n>

本教程将建立在我之前的帖子上； [Canvas 101:旋转形状](https://dev.to/brianmontanaweb/canvas-101-rotating-shape-28la)，专注于操作一个由类创建的对象填充的数组。

在上一篇教程中，我们使用`Square`类构建了一个使用`requestAnimationFrame`方法随时间更新的对象。现在我们将创建多个`Square`实例，在画布上制作它们的动画，并在它们完全离开屏幕时重置它们的位置。

让我们叉这支笔开始:
[https://codepen.io/brianmontanaweb/embed/jgWPmV?height=600&default-tab=result&embed-version=2](https://codepen.io/brianmontanaweb/embed/jgWPmV?height=600&default-tab=result&embed-version=2)

首先，我们将在构造函数的解构对象中用`xVelocity`更新`Square`类。我们将使用它在画布上移动正方形，并在移动时旋转它。设置一个默认值`0.1`，或者另一个类似的更小的值，在构造函数中我们将`xVelocity`设置为一个随机值，使用它的默认值作为最小随机值，1 作为`xVelocity`的最大随机值。

`this.xVelocity = Math.random() * (1 - xVelocity) + xVelocity;`

现在让我们检查一下`movement`函数，现在我们将值增加`0.1`，但是使用随机生成的`xVelocity`，我们可以随着时间的推移将值增加`xVelocity`。每次刷新你都会看到不同的速度。正方形将继续离开画布，但是我们可以检查正方形的当前位置，并使用此检查`shape.xPosition > canvasWidth + shape.width`将其重置到画布的左侧。在这样做的时候，我们想把新的位置设置为`-shape.width`，把它放在画布外面，这样它就会移动到画布里面，而不是出现在边缘。下面是对`movement`函数的最终更新。

```
function movement(shape) {
  if(shape.xPosition > canvasWidth + shape.width) {
    shape.xPosition = -shape.width;
  }
  shape.rotate += shape.xVelocity / 15;
  shape.xPosition += shape.xVelocity;
} 
```

厉害！我们看到`Square`实例在画布上被动画化和渲染！现在我们可以设置一个数组并创建多个`Square`实例。在顶部创建一个数组`const squares = [];`和一个总计数的值`let squareCount = 50;`，现在我们有一个数组来推送生成的`Square`的实例，同时随机化它的`xPosition`和`yPosition`。随机化将在`xVelocity`中使用相同的最小和最大阈值约束，我们可以这样想`Math.random() * (max - min) + min`。为了完成它，我们将使用一个`for`循环来用`squareCount`的限制填充我们的`squares`数组。

```
for (let i = 0; i < squareCount; i++) {
  squares.push(
    new Square({
      width: 40,
      height: 40,
      xPosition:
        Math.random() * (canvasWidth * 0.9 - canvasWidth * 0.1) +
        canvasWidth * 0.1,
      yPosition:
        Math.random() * (canvasHeight * 0.9 - canvasHeight * 0.1) +
        canvasHeight * 0.1
    })
  );
} 
```

因为已经填充了`squares`数组，所以我们需要更新`render`函数来确定`squares`数组中每个`Square`实例的更新值。让我们使用由`Array`对象提供的`forEach`方法，用`movement`函数更新每个元素的值，然后在画布上绘制结果。每个`Square`实例将绘制在最后一个之上，所以数组中的最后一个元素将绘制在所有前面的元素之上。

```
function render() {
  context.fillStyle = "lightsalmon";
  context.fillRect(0, 0, canvasWidth, canvasHeight);
  squares.forEach(square => {
    movement(square);
    context.save();
    context.fillStyle = "salmon";
    context.translate(square.xPosition, square.yPosition);
    context.rotate(square.rotate);
    context.fillRect(
      -square.width / 2,
      -square.height / 2,
      square.width,
      square.height
    );
    context.restore();
  });
  window.requestAnimationFrame(render);
} 
```

应该就是这样！现在所有的方块都将在画布上动画化，并在离开屏幕时重置它们的位置。在:D 下面的钢笔里看到结果

[https://codepen.io/brianmontanaweb/embed/Qevpdb?height=600&default-tab=result&embed-version=2](https://codepen.io/brianmontanaweb/embed/Qevpdb?height=600&default-tab=result&embed-version=2)