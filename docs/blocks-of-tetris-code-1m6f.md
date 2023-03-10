# 俄罗斯方块代码块

> 原文：<https://dev.to/remy/blocks-of-tetris-code-1m6f>

在我抽搐的一个小时里，我决定让 T2 尝试制作一个俄罗斯方块的克隆版，灵感来自俄罗斯方块在 2019 年 6 月 35 岁生日的消息。鉴于这是一个小时的编码游戏远未完成，但我有形状，我有下降块，大约 4 个多小时(离线)，我有一个完整的工作克隆原来的 *NES* 俄罗斯方块游戏。

我想分享一些(基于阵列的)开发亮点和我用来让游戏工作的技巧。

## 研究

最初我决定只是观察俄罗斯方块是如何工作的，并复制我小时候玩 Gameboy 版本时的记忆。在 twitch 会议结束后，我决定进行一些阅读，以清楚地了解块是如何旋转的，时间的增加是什么(当 10 行的每个块的速度增加时)，以及各种其他见解。

俄罗斯方块游戏有一个维基百科，但它主要基于俄罗斯方块公司(TTC)自己发布的 T2 官方指南，这些指南是基于 2001 年(及以后)版本的俄罗斯方块——我不想要。我想要老派的原创游戏(再说一次，因为这是我玩过的所有游戏)。

令人欣慰的是，有人[煞费苦心地反汇编了 NES 的代码，并记录了](https://meatfighter.com/nintendotetrisai/)俄罗斯方块是如何工作的，以便他们可以构建一个俄罗斯方块人工智能。从这里，我能够提取我想要的关于游戏的细节，方块旋转，方块选择和使用的随机函数和计时。

## 代表游戏记忆

游戏本身在 HTML5 画布上运行(我认为这是最适合这项特殊工作的技术)，但在我的 Twitch 编码尝试中，我试图检测画布本身的碰撞(当块撞到一个使用的像素时)。这根本无法扩展，并很快成为一个令人头痛的问题。

因为游戏被明确定义为 10x20，所以很快转移到用某种数组来表示内存是有意义的。因为块本身可以用一个值来表示，所以类型化数组是一个很好的选择。在我的类型化数组中，我可以选择存储`0`和`1`来表示可用和不可用，或者我可以使用大写字母的数值(四联骨牌被命名为 T，L，J，S，Z，O)。

无论哪种方式，初始化内存都很简单，事实上重置游戏得益于`.fill`方法:

```
const ROWS = 20;
const COLS = 10;

const memory = new Uint8Array(ROWS * COLS);
const reset = () => memory.fill(0); 
```

除了内存数组之外，为了进行调试，我还将内存的状态复制到另一个数组中，这样我就可以在内存的当前状态中来回翻页，以确保游戏按照我的预期运行。

## 四角旋转

记住我的内存是以线性数组的形式存储的，这意味着我会以同样的方式存储我的四联骨牌。

使用按位运算允许“简单”的块旋转。我说简单，是因为这是针对单个预定数字的单个操作。理解 XOR 运算是如何工作的也很有用。

如果你有一个值比如`9`，在二进制中(前缀为`0b`)就是`0b1001`。我们来看看 xor 是如何工作的:

```
// 9 in binary is
1 0 0 1

// where a 1 appears, it will flip the bit in that position,
// so the XOR operand we use is
1 0 1 0

// So 0b1001 (decimal 9) ^ (xor) 0b1010 (decimal 10)
   1 0 0 1
^ 1 0 1 0
   -------
= 0 0 1 1 // decimal 3

// importantly the same XOR operand can be used to flip
// 3 _back_ to 9:
   0 0 1 1
^ 1 0 1 0
   -------
= 1 0 0 1 // decimal 9 
```

下面是 T 形的四分音符是如何进行的:

```
// using 0 for empty and 1 for filled, the T tetromino can be
// represented in a 9 element array, where I've added wrapping
// to make it easier to visualise
0 0 0
1 1 1
0 1 0

// which is the binary representation
0b000111010 === 58

// now applying an XOR operation we're able to flip bits
0b000111010 ^
0b010100000 ===
0b010011010

// and that binary, in a 3x3 square is
0 1 0
0 1 1
0 1 0 
```

这整个过程使得代码中的块旋转非常简单，也很容易表示。然而，这个过程只能在一个方向上旋转形状，它不能用来做 180 度旋转。我可以使用另一个 xor 值(T tetromino 的旋转是`160`)，但是如果值反过来也可以。

在四棱锥上使用反转将我的形状旋转 180 度:

```
// the start position for the T block is
0 0 0
1 1 1
0 1 0

// which is actually
0 0 0 1 1 1 0 1 0

// which reversed is
0 1 0 1 1 1 0 0 0

// and now wrapped into 3x3 square
0 1 0
1 1 1
0 0 0 
```

要完成 270 度旋转，需要结合 xor 和反向方法。可能有更简单的方法来做到这一点，但它为我的目的工作。

## 碰撞测试

由于内存保存在一个(相对)简单的数组中，所以当 tetromino 向左、向右或向下移动时的碰撞测试相对简单。在允许 tetromino 移动之前，我测试了内存中是否有任何东西被写到数组中 tetromino 想要移动到的特定 X/Y 位置。

所以在我们的内存中，有一些函数可以将 X/Y 转换成一个索引是很有用的(因为内存是以线性方式保存的):

```
export const getIndexForXY = (x, y, width = COLS) => {
  if (x < 0) { // too far left
    throw new Error(`out of bounds: x(${x}) < 0`);
  }

  if (x >= COLS) { // too far right
    throw new Error(`out of bounds: x(${x}) > COLS(${COLS})`);
  }

  if (y >= ROWS) { // offscreen at the bottom
    throw new Error(`out of bounds: y(${y}) > ROWS(${ROWS})`);
  }

  // the actual formula
  return width * y + x;
}; 
```

使用反函数也很有用，可以从索引:
中得到 X/Y

```
export const getXYForIndex = i => {
  const x = i % COLS;
  const y = (i / COLS) | 0;

  return { x, y };
}; 
```

然后，正如我所说的，这是一个[检查 tetromino 的位置和形状](https://github.com/remy/tetrisy/blob/be715b0d6521ee22d02c5aff5c17b8320ef0e5c0/src/memory.js#L97-L118)的问题，并确保内存是“自由的”(值为`0`)，用于偏移`x`和`y`的形状的整个高度和宽度。

## 清除整行

在这个项目中，我最喜欢的一段代码可能是清除每一行并向下移动四联骨牌的逻辑。同样，因为我使用的是一个数组，所以我需要的*只有*两个方法是`copyWithin`和`fill`。

实用的逻辑是:对于给定的行号，取上面所有的行，向下推一行。

实际的逻辑是使用`copyWithin`将数组元素复制到`COLS`索引(第二行的开始)。从`0`(左上)到被删除线(用`getIndexForXY(COLS - 1, y - 1) + 1)`实现)的*端的索引选择数组元素。*

然后，用零填充最上面一行(从`0`到`COLS`):

```
export const removeLine = y => {
  // copy all the content above down to and over this line
  memory.copyWithin(COLS, 0, getIndexForXY(COLS - 1, y - 1) + 1);

  // clear the top line last
  memory.fill(0, 0, COLS);
}; 
```

* * *

我喜欢用类型化数组编码，部分是因为我可以玩低级的位运算，但也因为它让我更多地思考数组方法以及它们如何帮助我的软件设计。

*原载于[雷米夏普的 b:log](https://remysharp.com/2019/09/10/blocks-of-tetris-code)T3】*