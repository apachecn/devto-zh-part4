# 最后，使用 TypeScript 枚举的一种简单方法

> 原文：<https://dev.to/cubiclebuddha/finally-an-easy-way-to-use-typescript-enums-3kj0>

TypeScript 提供的最好的东西之一是 JavaScript 中根本不存在的标准`enum`类型。是的，是的...Github 中有*千个*enum 库，有各种各样的实现。但是在语言中建立一个标准是一件很棒的事情。

TL；dr:我将展示一个很酷的库，它让与枚举的交互成为一种快乐的体验。

# 那么有什么问题呢？

尽管打字稿枚举很棒...它们导致了一些冗长或者不可读的代码。

比如，假设您有一个订购食物的枚举:

```
export enum FoodPreference {
    vegan,
    vegetarian,
    meatEater
} 
```

太美了。但是现在你需要写一些表示逻辑。让我们首先展示一种简单但非常难懂的方式来编写表示逻辑:

```
const presentationString = filter === FoodPreference.vegan
            ? 'Do you have a nut preference?'
            : filter === FoodPreference.vegetarian
            ? 'Which protein would you like?'
            : 'What type of meat would you like?'; 
```

就像人们对食物的偏好一样...有些人喜欢嵌套三角形。我不是那种人。

虽然我喜欢`const`带来的不变性好处，但是嵌套 ternaries 的“眼睛扫描能力”存在严重问题。此外，我最近描述了当一个新的 case 被添加到 enum 中时，像这个例子这样的失败方法是如何将代码置于风险之中的。

[![cubiclebuddha image](img/0cb60ed477567b63e3be4a9e1ec21337.png)](/cubiclebuddha) [## “防御性编程”实际上是健康的吗？

### 隔间佛 5 月 8 日 195 分钟阅读

#discuss #help #typescript #javascript](/cubiclebuddha/is-defensive-programming-actually-healthy-5flj)

# 越可读，越有防御性，但可悲的是冗长的选项

因此，我们可以通过使用我在我的[其他](https://dev.to/cubiclebuddha/is-defensive-programming-actually-healthy-5flj)文章:
中描述的 [assertUnreachable](https://dev.to/cubiclebuddha/is-defensive-programming-actually-healthy-5flj) 函数，用我们的防御技术重写上面的代码

```
function askFirstQuestion(pref: FoodPreference): string {
    switch(pref){
        case(FoodPreference.vegan): {
            return 'Do you have a nut preference?';
        }
        case(FoodPreference.vegetarian): {
            return 'Which protein would you like?';
        }
        case(FoodPreference.meatEater): {
            return 'What type of meat would you like?'
        }
        default: {
            assertUnreachable(pref)
        }
    }
} 
```

嗯，还是要打很多字。我甚至不太喜欢 switch cases，因为已知的问题，比如失败。虽然 [assertUnreachable](https://dev.to/cubiclebuddha/is-defensive-programming-actually-healthy-5flj) 消除了很多 switch 案例的问题，但我仍然觉得我们可以做得更好。

# 仰望天空！是`ts-enum-util`来救援了！

一些很棒的人决定做一个库来简化许多枚举操作。查看他们的自述文件以获得更多信息，但是现在让我们看看这个`ts-enum-util`如何改进我们的代码:

```
function createPresentationString(pref: FoodPreference): string {
    return $enum.mapValue(pref).with({
        [FoodPreference.vegan]: 'Do you have a nut preference?',
        [FoodPreference.vegetarian]: 'Which protein would you like?',
        [FoodPreference.meatEater]: 'What type of meat would you like?',
    })
} 
```

这不是更清楚了吗？当然更短了。

# 那么这只是关于代码少吗？

等等，在你说这只是语法糖之前...让我们展示图书馆作者添加到他们图书馆的 TLC(温柔、爱和关怀)。

观察向枚举中添加新条目时会发生什么:

[![](img/378ee133d5d3ac06c4e1def14a3c07e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cmYgtkWa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zje0l6vzbusfbn5glz0.png)

是不是很酷？我们得到了 [assertUnreachable](https://dev.to/cubiclebuddha/is-defensive-programming-actually-healthy-5flj) 的所有好处，而没有嵌套 ternaries 的可读性问题或 switch 语句的冗长性(以及其他已知问题)。

# 思想？

所以我非常喜欢这种新方法。但是我很想听听你的想法，所以请在 [twitter](https://twitter.com/cubiclebuddha) 或评论中回复。此外，一定要给 Jeff Lau 的 [ts-enum-util](https://github.com/UselessPickles/ts-enum-util) 库投一两颗 Github 星。