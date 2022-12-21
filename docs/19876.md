# 你不同意哪种编程最佳实践？

> 原文：<https://dev.to/nicolasamabile/what-programming-best-practice-do-you-disagree-with-2pf6>

我最近在一次采访中被问到这个问题，这是我的回答:

### 纯粹的单元测试和浅薄的渲染思想

特别是对于 ReactJS，有许多测试库允许你进行浅层渲染(例如 [enzyme](https://airbnb.io/enzyme/) )，这意味着如果你有一个组合的组件，测试实际上不会渲染内部组件，所以你不能对其进行断言。如果您想要涵盖这些内部组件，您需要为每个部分编写特定的测试。

例如:

```
<div>
   <Form>
      <Username />
      <Email />
   </Form>
</div> 
```

有了这个结构，如果你想为这个组件写一个测试，第一个`div`会被呈现，但是定制的`Form`组件不会，无论是`Username`还是`Email`组件，它们都会被嘲笑。您将不得不单独测试它们，这遵循了纯粹的单元测试思想，但是您不能确保这些组件一起正常工作。
如果你遵循这种模式，你很快就会得到一套纯粹的单元测试，这些测试真的令人困惑，难以理解。

> ![Kent C. Dodds profile image](img/94f207e09e64133620a88b3ae5533c42.png)肯特 c .多兹[@肯特多兹](https://dev.to/kentcdodds)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)你的测试越不像你的软件被使用的方式，他们给你的信心就越少。2018 年 2 月 18 日上午 02:35[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=965052178267176960)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=965052178267176960)35[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=965052178267176960)176

测试应该类似于软件的使用方式。即使不是纯粹的单元测试(技术上你可以称之为集成测试)，在一天结束的时候，唯一重要的事情是你可以自信地发布你的应用。

**资源:**

*   [@kentcdodds](https://twitter.com/kentcdodds)
*   [测试 React 应用程序，v2](https://frontendmasters.com/courses/testing-react/)

图片由[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3808487)的 Gerd Altmann 提供