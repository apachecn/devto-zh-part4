# 使用 React 学习 TypeScript 第 2 部分(接口是什么、为什么和如何)

> 原文：<https://dev.to/analizapandac/learning-typescript-with-react-part-2-the-what-why-and-how-of-interfaces-1033>

⚡tl；理解什么是接口，为什么使用它们以及如何使用它们可以帮助你编写更多可重用的代码和创建令人敬畏的应用程序。

在[之前的文章](https://dev.to/analizapandac/learning-typescript-with-react-part-1-3ohn)中，我们谈到了类型，它是什么，如何使用它们以及为什么使用它们。如果你还没看，我推荐你先看。

在这篇文章中，我们将了解什么是接口，为什么你应该关心它，以及如何使用它来编写更多可重用的代码。

我们开始吧。

### **什么是接口？**

接口是 TypeScript 中将多个类型批注组合成单个命名批注的核心方法。

这是一种创建新类型的方法，它描述了对象的属性名及其类型。记住我们之前讨论过的类型(数字、布尔、字符串等。)?接口和那些类型是一样的。

### **你为什么要关心接口？**

对我来说，接口是 TypeScript 语言最好的特性之一。让我给你看看它有多棒。

让我们从下面的例子开始。

您有一个名为`renderVideo`的函数，它接受`video`作为参数，在这个参数中，您用应该出现在视频对象中的所有属性进行了注释。

```
const video = {
  videoId: '',
  title: '',
  description: '',
  thumbnailURL: ''
};

const renderVideo = (video: { videoId: string; title: string; description: string; thumbnailURL: string }): void => {

} 
```

即使参数注释很长，上面的代码也没问题，函数参数和返回值被正确地注释了。

**然而**，想象一下，如果你增加更多接受视频的功能。

```
const video = {
  videoId: '',
  title: '',
  description: '',
  thumbnailURL: ''
};

const renderVideo = (video: { videoId: string; title: string; description: string; thumbnailURL: string }): void => {

}

const getVideoSummary = (video: { videoId: string; title: string; description: string; thumbnailURL: string }): void => {

}

// another function has a `video` parameter
// another one
// and another one
// *shouts* "DJ Khaled" 
```

啊啊啊。我不知道你怎么想，但是对我来说，上面的代码看起来不太好。

有许多长注释被重复了几次。代码也更难阅读。

现在再次想象(我们在这里想象了很多)你需要对一个视频做以下一个或多个改变:

*   将发布日期添加为新属性
*   将`thumbnailURL`重命名为`imageURL`
*   将`videoId`的类型从`string`更改为数字

将这些新的变化应用到上面的代码意味着你需要在很多地方改变所有的视频注释。

这需要大量的工作，我们不希望这样，那么我们如何解决这个问题呢？

**简单，通过使用一个接口。**

### **如何创建一个界面？**

我们可以把这个长视频注释转换成一个叫做`Video`的界面。

> *要创建一个接口，使用 **`interface`** 关键字，后跟一个代表对象的名称，在本例中为 **`Video`** 。*
> 
> *名称应该以大写字母*开头，就像我们如何创建 React 组件和一组花括号一样，在花括号中声明对象的所有属性和方法及其类型。

这就是创建`Video`界面的方法。

```
interface Video {
  videoId: string;
  title: string;
  description: string;
  thumbnailURL: string;
} 
```

### **如何使用接口？**

由于接口是自定义类型，我们可以像普通类型一样将它注释到变量中。

```
let item: Video; 
```

通过使用`Video`接口，我们可以将前面的例子重写为:

```
interface Video {
  videoId: string;
  title: string;
  description: string;
  thumbnailURL: string;
}

const renderVideo = (video: Video): void => {};
const getVideoSummary = (video: Video): void => {};

// another function that uses a video
// another one
// and another one

const video = {
  videoId: "",
  title: "",
  description: "",
  thumbnailURL: ""
};

renderVideo(video); 
```

这段代码看起来比上一段好很多，对吗？

它看起来更干净，一眼就能看出这个参数是一种类型的`Video`。

最重要的是，我们只能在一个地方，即接口声明中添加、删除或重命名属性或更改属性类型。厉害！

> 💡如果您使用 VS 代码，我强烈建议您这样做，您可以通过按住`ctrl/cmd`键并将鼠标悬停在接口名称上来查看接口的所有属性名称和类型。
> [![](img/54d99325dbc546b2ef112f3ede392cba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r1DULaJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rm64kjn21s83qhptjvdm.gif) 
> 挺酷的吧？

### **界面在 app 中是如何使用的？**

除了使用`Video`接口，我还创建了另一个接口来表示 Youtube API 响应的结构。

```
interface YoutubeVideo {
  id: { videoId: string };
  snippet: {
    publishedAt: string;
    title: string;
    description: string;
    thumbnails: { medium: { url: string }; default: { url: string } };
    channelTitle: string;
    channelId: string;
  };
}

interface YoutubeAPIResponse {
  data: { items: YoutubeVideo[] };
} 
```

该接口用于`fetchChannelVideos`功能。

```
export const fetchChannelVideos: (
  args: FetchVideosArguments
) => Promise<Video[]> = async ({
  shouldUseDefaultVideos = false,
  searchQuery
}) => {
 const requestURL = '';
 const response:YoutubeAPIResponse = await axios.get(requestURL);
}; 
```

由于接口是一种类型，这意味着 TS 编译器也对带注释的数据进行错误检查。

通过为 API 响应结构创建一个接口并将其注释到变量中，我们确保了不会意外地访问一个不存在的属性。

所以如果我们试图访问不包含在 API 响应中的`comments`，TypeScript 编译器会给我们一个错误。

### **编写带有接口的可重用代码**

既然我们已经讨论了接口是什么、为什么和如何，那么让我们来谈谈如何使用它来编写更多可重用的代码。

假设您有一个组件来呈现来自 Youtube 用户和频道的评论。

首先我们这样写:

```
interface User { name: string; avatar: string; }
interface Channel { name: string; avatar: string; }

const renderUserComment: (user: User, comment: string) => React.ReactNode = (
  user,
  comment
) => { /* return user comment */ };

const user = { name: "Ana", avatar: "ana.png" };
const channel = { name: "Tedx Talks", avatar: "tedxtalks.png" };

const renderChannelComment: (
  channel: Channel,
  comment: string
) => React.ReactNode = (channel, comment) => {/* return channel comment */};

renderUserComment(user, 'hello there');
renderChannelComment(channel, 'hi, thanks for watching'); 
```

有两个单独的接口来表示用户和通道，以及单独的函数来呈现这些评论，即使它们做同样的事情，呈现作者的评论。

#### **我们如何重写这段代码来删除重复的代码呢？**

虽然我们可以用一个[联合类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)来表示评论作者可以是一个`User`或者一个`Channel`、

```
const renderComment: (author: User|Channel, comment: string) => React.ReactNode = (
  author,
  comment
) => { /* return comment */ }; 
```

在这种情况下，最好创建一个界面来代表这两种类型的用户。

让我们使用一个叫做`Author`的更通用的接口来重写它。

```
interface Author { name: string; avatar: string; }

const renderComment: (author: Author, comment: string) => React.ReactNode = (
  author,
  comment
) => { /* return comment */};

const user = { name: "Ana", avatar: "ana.png", github: "analizapandac" };
const channel = { name: "Tedx Talks", avatar: "tedxtalks.png", channelId: "UCsT0YIqwnpJCM-mx7-gSA4Q" };

renderComment(user, 'hello there');
renderComment(channel, 'hi, thanks for watching'); 
```

尽管`user`和`channel`代表两种不同类型的数据，**满足被视为`author`** 的要求，即`name`和`avatar`，因此`renderComment`函数对两个对象都有效。

> 上面的例子是非常基本的，但这里的要点是，我们可以使用一个通用接口来表示不同的对象，这些对象可以使用不同的功能。这是使用接口编写更多可重用代码的一种方式。

### **关闭思绪**

除了了解接口是什么、为什么以及如何使用之外，我希望我也向您展示了接口的强大功能，以及您如何使用它来创建令人惊叹的应用程序。

关于接口还有很多，尤其是如何将它与其他 TypeScript 特性(如类)一起使用，并编写更好的代码。

我将继续学习更高级的 TS 概念，使用它们创建新的令人兴奋的项目并与您分享。

我希望你也会这样做😊

### **项目 Github 回购**

下面是这个应用的源代码:[https://github.com/analizapandac/tedflix](https://github.com/analizapandac/tedflix)

如果你愿意，你可以克隆它并在你的本地使用源代码。

你可以在[https://tedflix.netlify.com/](https://tedflix.netlify.com/)看到部署的应用。

* * *

这是之前的文章:

[![analizapandac image](img/01116b53a5580c3eee186c68441a0f2b.png)](/analizapandac) [## 使用 React 学习 TypeScript 第 1 部分

### 安娜·丽莎·潘达克 7 月 1 日 196 分钟阅读

#typescript #react #beginners #webdev](/analizapandac/learning-typescript-with-react-part-1-3ohn)

* * *

如果你对下一步该怎么做有任何反馈、建议或指点，请在下面的评论中告诉我。提前感谢。