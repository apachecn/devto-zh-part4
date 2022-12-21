# 如何产生可读的代码

> 原文：<https://dev.to/jaffparker/how-to-produce-readable-code-566l>

这是一系列不定长度文章的第一部分。

多年来，我在不同的团队中工作，并建立了一个团队，与团队使用的实际工具相比，我越来越重视代码质量。如果您的代码符合一定的质量标准，那么无论您用什么语言编写，或者使用什么框架，任何人都可以参与进来。

我想分享我的经验，通过让代码库易于阅读和理解来帮助初创公司扩大招聘范围。

# 什么东西可读？

[![Chang can't read your code](img/8d0bb1d20ee107b5d13865dfc3286261.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2fwzYal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ih4airdl2vdzvg5ct7tv.jpg)

我到底在说什么？`// comments`量够吗？也许是丰富的`s p a c e s`？缩进？文档？..

如果做得好，所有这些都很重要。如果你有不明确的代码，给它加上清晰的注释。你需要确保你的缩进和空格是一致的。并且您确实应该从一开始就编写文档(这可能只是本系列中的一篇文章)。

在这篇文章中，我将谈论英语。也就是语言。西班牙语或汉语也可以，但它们会极大地限制你的招聘范围，所以就用英语吧。

# 为什么语言在编码中很重要

在大多数语言中，人们用名词来标识物体，用动词来表达对这些物体的动作。我们就是这样读英语的。

如果我们开始替换动词和名词，就不可能理解单词的意思。这个句子的动词和名词互换了:

> 如果我们开始替换动词和名词，就不可能理解说话的意思。

这到底是什么，对吧？我想把我的眼睛刻出来写这个。

那么我们为什么要编写这样的代码呢:

```
function user() {
  return {
    id: '1',
    name: 'Mr. Poopy Butthole'
  }
}

if (user()) {
  const gottenUser = user()
} 
```

这在人类语言中没有意义。我们不是这样读句子的。虽然这是一个小例子，但是如果你有一个完全无视人类语言语法的项目，它将很快变得混乱。记住事情的真相需要花费你更多的脑力。

我将如何修复代码:

```
function hasUser() {
  return true
}
function getUser() {
  return {
    id: '1',
    name: 'Mr. Poopy Butthole'
  }
}

if (hasUser()) {
  const user = getUser()
} 
```

我改变了什么？

首先，我为`if`语句引入了一个新函数。它返回一个布尔值，这使得它本身更清晰，而且**这个名字**表明它返回一个布尔值。怎么会？试着问一个问题:有用户吗？答案是是或不是——布尔！

第二，我把`user()`函数改名为`getUser()`。因为函数是动作，所以函数名只适合使用动词。

第三，我可以用现在可用的名词来表示变量。这同样更有意义，因为变量是对象，因此通常应该是名词。

现在试着像读英语一样读代码。你能很快明白发生了什么吗？我不怀疑你有。

# 规则

一个规则来统治他们:

> 你必须能够在第二天像阅读英语一样阅读你的代码。

如果你不能，你搞砸了，重做。

现在，我不是说你必须不停地写、读、再写、读、再写...直到感觉对了。有一套非常清晰的规则可以帮助你把事情做好。事实上，这些规则还会帮助您明确分离关注点，这只是一个双赢的局面！我们开始吧:

*   ### 永远用动词来命名你的函数。

    避免使用像`user()`或`post()`这样的函数名，尤其是避免让它们处理整个 CRUD 的诱惑。而是引入*对*对象的动作:`getUser()`、`addUser()`、`modifyPost()`、`deletePost()`。你马上就能看到每个函数的作用。

*   ### 描述名字中的功能，但不要超过 1-2 个动词

    这是一个棘手的问题。总是在名字中包含你的函数所做的一切。如果您的函数创建了一个用户并发送了一封欢迎电子邮件，将其命名为`createUserAndSendWelcomeEmail()`。你可能会发现，即使有两个动词，这也变长了。这是一个标志，让你把它分成两个功能:`createUser()`和`sendWelcomeEmail()`。然后，您可以从发送欢迎电子邮件的`createUser`中发出一个事件。

    这实现了两个目标。首先，它使你的函数做什么变得很明显。第二，它迫使你按照关注点来划分你的功能，并且保持简短！

*   ### 总是用名词给你的变量命名，并在必要的地方加上形容词

    你的变量包含数据或对象，在英语中是用名词描述的。因此，为了让*对变量*对象*起*作用的函数清晰明了，你的变量名必须是名词。`users`或`posts`是可以接受的。

    记住这一点，在变量名中一定要包含相关的形容词，尤其是在一个上下文中有不止一个相同数据类型的情况下。举个例子，你获取了所有的帖子，然后过滤掉了便便屁眼先生写的帖子。你应该有`allPosts`和`postsByPoopy`，而不是有`posts`和`posts2`。这有很大的不同！

这些规则万无一失。遵循它们，你将不可避免地产生可读的代码。

这些规则的一个坏的好的例子怎么样？

```
// ❌ BAD

function users(id) {
  if (id) {
    return userByIdFromDatabase(id)
  } else {
    return allUsersFromDatabase()
  }
}

function posts(post, userId) {
  if (!post) {
    return allPostsFromDatabase(userId)
  } else {
    return updatePostInDb(post)
  }
}

const user = users('1')
const postsFromDb = (null, user.id)
const posts2 = postsFromDb.filter(post => post.author === 'Mr. Poopy Butthole')

posts2[0].title = 'I write bad code'
posts(posts2[0]) 
```

```
// ✔️ GOOD

function getUserById(id) {
  return getUserByIdFromDatabase(id)
}
function getAllPosts() {
  return getPostsFromDatabase()
}
function updatePost(id, post) {
  return updatePostInDatabase(id, post)
}

const user = getUserById('1')
const allPosts = getAllPosts()
const firstPostByPoopy = allPosts.filter(post => post.author === 'Mr. Poopy Butthole')[0]

updatePost(
  firstPostByPoopy.id, 
  {
    ...firstPostByPoopy,
    title: 'I write good code!'
  }
) 
```

差别真大！第一个例子无法用英语阅读，函数名含糊不清，不能反映它们的功能。但是第二个例子完全不同！这些函数和它们的目的都很清楚，每个变量都是可以理解的，我相信你不会花时间去阅读。

* * *

正如我在开始提到的，我是不同团队的一员，并且已经建立了一个团队。拥有可读的代码有助于我们每次都雇佣不具备我们特定工具经验的优秀开发人员(这在拥有各种标准和框架的 JS 中尤其重要)。像对待人类语言一样对待代码将会让你的新开发人员花时间来提高效率，而不是试图理解你写的东西。

[![Team party](img/196912a4da23873b65022a7d4011882f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JkZW79GX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0mmoylh09x78wo53eyh.gif)

你有其他好的可读代码实践吗？大家在评论里讨论一下吧！