# 我在一次代码审查中学到了一件简单的事情:标记模板

> 原文：<https://dev.to/dvddpl/a-simple-thing-i-learned-during-a-code-review-tagged-templates-1hp5>

有一天，我在审查一些代码，我发现自己困惑地盯着这个片段:

```
fixture `A bunch of tests`
    .page `http://myapp.github.io/example`; 
```

Enter fullscreen mode Exit fullscreen mode

当然，我注意到了模板文字，我确信这是一个函数，它被传递了一个参数，然后返回一个 [**流畅的接口**](https://martinfowler.com/bliki/FluentInterface.html) ，但是我真的搞不懂它的语法。对我来说不像有效的 JS 脚本代码。

[![what is that](img/f0ff9780f1a6652e1bcd60b2f6b8476a.png)](https://i.giphy.com/media/tCL2IFQGi7COA/giphy.gif)

我查看了测试框架的[文档](https://devexpress.github.io/testcafe/documentation/test-api/test-code-structure.html#fixtures),该测试框架用于在 UI 上自动化端到端测试。(过去我们只是用[木偶师](https://github.com/GoogleChrome/puppeteer)来做一些非常基础的东西，在一些项目中，我们尝试了 [Cypress.io](https://www.cypress.io/) ，对于这个项目，团队/开发者选择了 [TestCafe](https://github.com/DevExpress/testcafe) 。所以这个测试框架对我来说是新的。)

一切都如其所愿:

```
// To declare a test fixture, use the fixture function.
fixture( fixtureName )
fixture `fixtureName` 
```

Enter fullscreen mode Exit fullscreen mode

因此..允许这种语法的魔力是什么？
我不记得我在谷歌上具体输入了什么，但是类似于 **javascript 模板文字作为方法**和[当然第一个结果](https://dev.to/dvddpl/often-your-solution-is-one-more-google-search-away-574a)足以用这样一件小事来启发我:

### 标记的模板

到目前为止，我一直使用模板文字，因为它们具有非常基本和非常有用的特性:

#### 字符串插值

这意味着能够写:

```
console.log(`Hi ${name}! Today is ${new Date().toString()}.`) 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
console.log('Hi ' + (name) + '! Today is ' + new Date().toString() +'.'); 
```

Enter fullscreen mode Exit fullscreen mode

#### 多行字符串

这允许您编写多行文本，而不必为每一行添加*加号和新行*。
忘了这个吧:

```
const twinkle = "Twinkle, twinkle, little star\n"+
"How I wonder what you are\n"+
"Up above the world so high\n"+
"Like a diamond in the sky\n"+
"Twinkle, twinkle little star\n"+
"How I wonder what you are" 
```

Enter fullscreen mode Exit fullscreen mode

遇见这个:

```
const twinkle = `Twinkle, twinkle, little star
How I wonder what you are
Up above the world so high
Like a diamond in the sky
Twinkle, twinkle little star
How I wonder what you are` 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我从来没有真正花时间去阅读模板文字的整个文档，并且我有太多次在[标记的模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates)处停止阅读文档...：

> 标签允许你用一个函数来解析模板文本。标记函数的第一个参数包含一个字符串值数组。其余的参数与表达式相关。

对我来说从来没有真正的意义，也无法想象现实世界的场景。

这一次，我试图理解为什么在编写测试用例时会使用带标签的模板。

我试图重写文档中的例子

```
function myTag(strings, personExp, ageExp) {
  var str0 = strings[0]; // "That "
  var str1 = strings[1]; // " is a "

  // There is technically a string after
  // the final expression (in our example),
  // but it is empty (""), so disregard.
  // var str2 = strings[2];

  var ageStr;
  if (ageExp > 99){
    ageStr = 'centenarian';
  } else {
    ageStr = 'youngster';
  }

  // We can even return a string built using a template literal
  return `${str0}${personExp}${str1}${ageStr}`;
}
var person = 'Mike';
var age = 28;

console.log(myTag`That ${ person } is a ${ age }`)
 // "That Mike is a youngster" 
```

Enter fullscreen mode Exit fullscreen mode

好，这里我们传递一个包含一些替换的字符串，函数在打印/连接该字符串之前在内部应用一些条件逻辑。没有模板字面量的语法，该方法如何被正常调用？

当我试着用

```
console.log(myTag("That " + person + " is a " + age )) 
```

Enter fullscreen mode Exit fullscreen mode

我拿到了

```
Tundefinedhyoungster 
```

Enter fullscreen mode Exit fullscreen mode

而用

```
console.log(myTag("That ", person, " is a ", age )) 
```

Enter fullscreen mode Exit fullscreen mode

输出为

```
TMikehyoungster 
```

Enter fullscreen mode Exit fullscreen mode

好吧，让我们再读一遍文件

> 标记函数的第一个参数包含一个字符串值数组。
> 
> 剩下的自变量都与表达式有关

这意味着为了达到同样的结果，你必须像这样调用它:

```
console.log(myTag(["That ", " is a "], person, age )) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，这样就很有道理了。而且标签化的功能立刻显得非常实用！

[![aha moment](img/205bbfc67dc8517cba96326f4ddc0b1d.png)](https://i.giphy.com/media/3oz8xOu5Gw81qULRh6/giphy.gif)

此外，标记函数不一定要返回一个字符串，它可以是任何东西。与示例中一样，变量/替换可以用于在返回格式化字符串之前处理一些操作和条件逻辑，并且可能在测试框架源代码中，字符串被内部保存，TestSuite 实例作为 fluent 接口返回。

即使这很简单，我也很高兴在审查一个经验不足的同事写的代码时发现了新的东西。太多有经验的开发人员将**代码审查视为挑剔、过度批评和居高临下**的机会，但这绝不应该发生。

> 永远不要错过学习的机会。永远不要低估你可以从代码评审中获得的知识。
> 
> 总是花一点点额外的时间去深入挖掘一些你不知道、不理解的东西，并且激起你的好奇心。

当你回顾其他人的代码时，有没有顿悟的瞬间？

* * *

由 Sergi Viladesau 在 Unsplash 上拍摄的封面图片