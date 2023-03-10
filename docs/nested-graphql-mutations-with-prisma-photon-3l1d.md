# Prisma 2 光子嵌套 GraphQL 突变

> 原文：<https://dev.to/codemochi/nested-graphql-mutations-with-prisma-photon-3l1d>

Prisma 的 Photon 是一个令人惊奇的包，它负责从全栈 web 应用程序的后端对数据执行 CRUD 操作的所有繁重工作。我们之前已经展示了如何使用 Photon 轻松创建 GraphQL api [ [1](https://www.codemochi.com/blog/2019-07-08-prisma-2-nextjs-docker/) 、 [2](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/) 、 [3](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/) ]。在这个例子中，用户可以有很多博客，但是我们必须先创建一个用户，然后再创建博客。

**我们正在做的事情的视频:**

[https://www.youtube.com/embed/H0jlvMmzpR0](https://www.youtube.com/embed/H0jlvMmzpR0)

你可能想知道是否有更直接的方法来执行单个嵌套突变，既创建用户又创建博客，事实证明 Photon 使这变得非常容易。

突变是一个 GraphQL 术语，指的是改变数据的操作。这是我们过去用来创建用户和帖子的两个变种。

**创建用户**

```
mutation  signupUserMutation($name:  String!,  $email:  String!)  {  signupUser(data:  {  name:  $name,  email:  $email  })  {  ...UserFragment  }  } 
```

**创建博文**

```
mutation  createDraftMutation(  $title:  String!  $content:  String!  $authorEmail:  String!  )  {  createDraft(title:  $title,  content:  $content,  authorEmail:  $authorEmail)  {  ...PostFragment  }  } 
```

为了创建嵌套变异，我们在用户创建变异的`posts`字段中使用了`create`方法。我们可以指定一个我们想要添加到新用户和光子的所有帖子的数组，以及 Prisma 的 Nexus 插件将创建新用户和我们想要的所有新帖子。

**先创建一个用户，然后将几个博客关联到该用户**

```
mutation  {  signupUser(  data:  {  email:  "stephen@codemochi.com"  name:  "Stephen"  posts:  {  create:  [  {  title:  "First Post",  content:  "Yay",  published:  true  }  {  title:  "Second Post",  content:  "Oh yeah",  published:  false  }  ]  }  }  )  {  id  name  email  posts  {  id  title  content  }  }  } 
```

[![With great power comes great responsibility- don't mess it up!](img/33d9c88945d6806583f8a3e23be67976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tQujYL8G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/17767c497b059ee4edc59714d986a4dc/78067/power.jpg)

还记得《蜘蛛侠》里他们说的“能力越强，责任越大”吗？嗯，在这里也是如此。Nexus 将自动生成我们指定的查询和突变。我们需要注意的一个问题是，由于我们使用的是 Nexus 创建的 stock create user 变体，而不是一个定制的解析器，这个变体在允许的方面有更多的灵活性。

过去，我们定制了一个 createDraft 变体，看起来像这样:

```
t.field('createDraft', {
  type: 'Post',
  args: {
    title: stringArg(),
    content: stringArg({ nullable: true }),
    authorEmail: stringArg(),
  },
  resolve: (_, { title, content, authorEmail }, ctx) => {
    return ctx.photon.posts.create({
      data: {
        title,
        content,
        published: false,
        author: {
          connect: { email: authorEmail },
        },
      },
    })
  },
}) 
```

我们选择这样做，而不是简单地做`t.crud.createOnePost({ alias: ‘createDraft’ })`，因为使用长格式版本，允许我们精确地控制我们将接受哪些参数，以及它们如何被传递到 photon `create`方法中。在上面嵌套变异的情况下，我们允许用户输入`published`字段的布尔值，这允许他们指定他们是否希望它被发布，这在`createDraft`变异中是明确禁止的，因为我们将每个帖子的已发布字段设置为 false。

如果您对用户在特定模型项目上的灵活性感到满意，那么整个讨论可能就无关紧要了。如果您仍然希望进行嵌套变异，但是您希望限制用户可以更新的字段，我们可以创建一个自定义变异，我们可以在其中限制可以传递给它的参数。创建一个新的突变，如下所示:

```
t.field('createUserAndDrafts', {
  type: 'User',
  args: {
    name: stringArg(),
    email: stringArg(),
    posts: arg({ type: 'PostCreateManyWithoutPostsInput' }),
  },
  resolve: (_, { name, email, posts }, ctx) =>
    ctx.photon.users.create({
      data: {
        email,
        name,
        posts: {
          create: posts.create.map(({ title, content }) => ({
            title,
            content,
            published: false,
          })),
        },
      },
    }),
}) 
```

在这里，我们在上面的`graphql`部分中使用的突变将保持不变，但是我们只使用标题和内容，并且我们正在为在解析器中发布设置我们自己的值。

从那里来的还有更多！
[点击这里给我们您的电子邮件，我们会在发布新内容时通知您。](https://gmail.us20.list-manage.com/subscribe?u=37f38485b2c7cff2f3d9935b5&id=e3bc056dde)我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-09-19-prisma-photon-nesting/) 。