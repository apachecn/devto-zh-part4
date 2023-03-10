# 编写代码供他人阅读

> 原文：<https://dev.to/josemunoz/writing-code-for-others-to-read-82b>

> 大多数软件工程师主要关注的是如何更好地编写代码。这很有道理。这是在合理规模的团队中成为伟大工程师的重要一步。
> 
> 然而，成功的公司会不断成长。迟早，软件工程团队将超越几十个人，在那里每个人都可以很容易地与其他人交谈。人们将被分散在不同的楼层。将在不同地点开设新的办事处。面对面的交流开始不够了。电子邮件、聊天或视频电话等渠道变得更加重要。这种增长的速度因公司而异:有些公司需要数年时间。对于一些真正成功的公司来说，这发生得要快得多。
> 
> -格里利是俄国人

当你作为一名开发人员获得第一份工作时，你将不可避免地阅读、理解和维护其他人的代码。编码是一个人逻辑能力的表达，每个头脑都是一个自己的世界，所以随着你获得经验，你会学会通过编码风格来识别人。在一个小团队中，这没什么大不了的，但是随着人们来来去去，这开始成为一个问题。

在理想的情况下，组织保持一个[风格指南](https://github.com/airbnb/javascript)，这有助于保持代码库的同质性，如果这不是你的情况，你的代码应该表达意图，讲述一个故事，这将使错误更容易识别和定位。我们可以做一些事情来保持我们的代码*干净*。以下是我个人对罗伯特·c·马丁的`The Clean Code`系列丛书的过于简化的观察，带有适量的经验偏见。

# 使用有意义的名字

变量名应该表达意图，明确变量的用途。不要害怕使用长名字，只要确保你遵循了语言的大小写约定。变量应该在其上下文中命名，松散耦合的逻辑很少有几个资源做同样的事情，所以在上下文中命名允许您重用在另一个文件中有意义的变量名。函数名应该被认为是动词，是对事件的反应。当命名一个函数时，考虑一下*这个函数将被用来做什么。*

而不是写这个:

```
 button.onclick = () => {
  const anchor = document.createElement('a')
  anchor.download = 'my download.pdf'
  anchor.src = '/some/resource/url'
  anchor.click()
 } 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写:

```
const downloadPdf = // some implementation
button.onclick = downloadPdf 
```

Enter fullscreen mode Exit fullscreen mode

# 奉承是你的朋友

Currying 是一种将带有多个参数的函数的求值转化为一系列函数的求值的技术，每个函数只有一个参数。Curried 函数在函数组合的上下文中特别有用，它允许我们创建专门的[高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)，可以提供更多的语义。

而不是写这个:

```
const SomeComponent = props => {
  return (
    <Button
      onClick={() => props.navigate('somewhere-else')}
    />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写:

```
const SomeComponent = props => {
  const goTo = route => () => props.navigate(route)

  return (
    <Button
      onClick={goTo('somewhere-else')}
    />
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

# 评论也可以是函数！

如果您正在编写很大一部分逻辑，您可能会觉得需要解释这些代码是做什么的，在处理该文件时，自然会考虑留下注释供另一个合作者阅读。每当你发现自己写了一个注释来解释一部分代码背后的原因时，就把它当成一个机会，用一个有意义的名字把那部分代码重构为它自己的函数。这并不意味着注释是不好的，对于遗留代码不能被重构的注释或需要被重新审视的事情，存在有效的案例`//#TODO: vacuum cat`

而不是写这个:

```
const myBigImperativeFunction = async () => {

 if(isCold){
   const clothes = await fetch('/api/clothes')
   if(clothes.sweater){
     if(clothes.sweater.thickness > 3){
       clothes.sweater = await fetch('/api/sweaters?thickness=4')
     }
   } else {
      clothes.sweater = await fetch('/api/sweater')
   }

   const coffee = await fetch('/api/beverages/coffee')
   this. energy += cofee;
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写:

```
const wearWarmSweater = //some implementation
const getSomeCoffee = //some implementation

if(isCold){
  await wearWarmSweater()
  await getSomeCoffee()
} 
```

Enter fullscreen mode Exit fullscreen mode

# 保持物品干燥

逻辑归约是任何开发人员最强大的工具之一。具有大量流控制的代码很难发现重复的工作，但是现在你的代码明确地表达了它的意图，更容易发现可以减少和重用的东西。

而不是写这个:

```
const rightIcon = {
  size: 16,
  name: 'plus',
  color: '#0064D2',
}

const leftIcon = {
  size: 16,
  name: 'minus',
  color: '#0064D2',
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写:

```
const smallCeruleanSign = name => ({
  name,
  size: 16,
  color: '#0064D2',
})

const rightIcon = smallCeruleanSign('plus')
const leftIcon = smallCeruleanSign('minus') 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

编写干净的代码使每个人的生活变得更容易，你的用户将受益于可读和可理解的代码所带来的生产力的提高。甚至在个人层面上，干净的代码允许我们理解 6 个月后的项目，它允许团队更快地接纳新的工程师，并且它有助于发现可以以粒度方式优化的工作。让我知道你做了什么来保持你的代码干净，我很乐意从评论区学习一些技巧。