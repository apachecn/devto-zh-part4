# 在 js 中使用游戏的 CSS 动画(只是为了好玩)

> 原文：<https://dev.to/tetragius/use-css-animation-for-game-in-js-just-for-fun-4h1c>

[https://codepen.io/tetragius/embed/KjMKyp?height=600&default-tab=result&embed-version=2](https://codepen.io/tetragius/embed/KjMKyp?height=600&default-tab=result&embed-version=2)

当一个元素在 CSS 动画的帮助下移动时，我们可以在 JS 代码中读取它的真实坐标，并在计算中使用它。于是 css 搞动画，JS 搞碰撞计算。

块的动画

```
@keyframes animation-a { 
    0% { 
       left: 400px;
    }
    50% { 
       left: 200px;
    }
    100% { 
       left: 400px;
    }
} 
```

块数组

```
const blocks = [  // left, bottom, width, animation-name, duration
    [240, 240, 700, null],
    [0, 180, 200, null],
    [400, 140, 100, 'animation-a', 5],
    [400, 90, 250, null],
    [250, 40, 150, 'animation-b', 3],
    [0, 0, 250, null],
    [400, 0, 670, null],
    [0, -40, 700, null],
]; 
```

为场景准备积木

```
function prepareBlocks() {
    for (let b in blocks) {
        let _block = blocks[b];
        let block = document.createElement('div');
        block.classList.add('block');
        block.style.left = `${_block[0]}px`;
        block.style.bottom = `${_block[1]}px`;
        block.style.width = `${_block[2]}px`;
        block.style.animationName = _block[3] || '';
        block.style.animationDuration = `${_block[4]}s` || '';
        scene.insertBefore(block, player);
        blocks[b] = block;
    }
} 
```