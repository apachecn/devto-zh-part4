# 了解类型脚本排除

> 原文：<https://dev.to/antonholmberg/understanding-typescripts-exclude-3bc1>

我最近开始做更多的打字稿。我以前有很多使用类型化语言的经验，但是在 TypeScript 中仍然有一些我一开始并不感到舒服的东西。

### 那种怪异的排斥型

在阅读 TypeScript 2.8 的发行说明时，我无意中发现*省略了*。不知道它是什么，我开始理解它。然而，自从
我发现*省略*被定义为*选择*和*排除*的组合后，问题就出现了。我就是
无论如何也想不出*排除*做了什么。

我找到的大多数关于 *Exclude* 的文章都会展示一个例子，说明
是如何与另一种类型结合使用的。感觉他们有点假设
读者已经知道*排除*做了什么。

### 让我们从联合类型开始

所以 TypeScript 有一个很棒的特性叫做*联合类型*。我认为
展示一个*工会类型*的例子比用文字解释更容易。

```
type Language = "swedish" | "danish" | "english" | "french":

const firstLanguage: Language = "swedish";
const secondLanguage: Language = "english";

// Will not compile
const thirdLanguage = "meowing" 
```

所以在上面的例子中，我们创建了一个叫做*语言*的类型。
*语言*类型的变量现在只能是我们在类型中定义的语言之一。在这种
情况下*喵喵叫*不是可接受的语言，因此
以上的程序将无法编译。

### 那么这是什么排除物呢？

这就是*排除*进来的时候。 *Exclude* 接受两个*联合类型*，类似地，
从第一个*联合类型*中减去第二个*联合类型*中的值。

```
type Language = "swedish" | "danish" | "english" | "french":
type NordicLanguage = Exclude<Language, "english" | "french">;

const firstLanguage: NordicLanguage = "swedish";
// This will not compile
const secondLanguage: NordicLanguage = "english"; 
```

所以在上面的例子中，我们创建了另一种类型，叫做 *NordicLanguage* 。这种
类型可以采用与*语言*相同的所有值，除了被排除的值
*英语*和*法语*。这和写作或多或少是一样的。

```
type Language = "swedish" | "danish" | "english" | "french":
type NordicLanguage = "swedish" | "danish"; 
```

### 一个很酷的用例

所以我最近遇到了一个问题，我的一个对象包含了多个相同类型的键。我还想存储哪些键当前被激活/选中。

事实证明。这是*排除*的完美案例。

```
type AvailableArea = Exclude<keyof Map, 'selectedArea'>;

type Climate = 'grass' | 'snow' | 'sand' | 'water';
interface Area {
  climate: Climate;
}

interface Map {
  selectedArea: AvailableArea;
  north: Area;
  south: Area;
  west: Area;
  east: Area;
} 
```

我们首先需要理解的是的*键是什么意思。* 

```
// Same as: type keys = "selectedArea" | "north" | "south" | "west" | "east";
type keys = keyof Map;

interface Map {
  selectedArea: AvailableArea;
  north: Area;
  south: Area;
  west: Area;
  east: Area;
} 
```

那么现在我们有了这个问题:我们真的希望 *selectedArea*
能够引用它自己吗？在这种情况下，答案是否定的。如果我创建一个带有硬编码键名的
*union 类型*，如果我开始添加更多的区域
比如 *southWest* 会怎么样？这些问题让我得出结论，如果我在这里使用 *Exclude* 可能是
最好的。

我们知道的*key 返回一个*联合类型*，其中的值可以是对象中的任意
键。我们现在需要做的就是“排除” *selectedArea* 然后我们
应该会得到我们想要的东西！* 

```
type AvailableArea = Exclude<keyof Map, 'selectedArea'>; 
```

这使我有可能在将来包含更多的领域，并且在我的应用程序中仍然保持
类型安全。

### 关闭思绪

希望有人发现这在某种程度上是有用的。下一次我可能会谈到*挑选*
，但是有大量的教程，一旦我理解了
*排除*，我发现*挑选*并没有那么难掌握。