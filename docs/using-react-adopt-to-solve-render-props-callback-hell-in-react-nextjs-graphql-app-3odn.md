# 使用 react-adopt 解决 React NextJS GraphQL 应用中的“渲染道具回调地狱”

> 原文：<https://dev.to/nabendu82/using-react-adopt-to-solve-render-props-callback-hell-in-react-nextjs-graphql-app-3odn>

在完成韦斯·博斯的[高级反应](https://advancedreact.com/)课程后，我正在构建一个演示印度餐厅计费应用程序。

在我的第一篇文章中，我把菜单从普通菜单改成了[汉堡菜单](https://dev.to/nabendu82/creating-a-hamburger-menu-in-react-nextjs-app-1hpb)。
在第二篇文章中，我已经给出了通过 [cloudinary](https://dev.to/nabendu82/cloudinary-for-image-management-in-react-nextjs-app-5f7) 上传图片的细节。
第三，我们学会了使用 [gmail](https://dev.to/nabendu82/sending-transactional-emails-through-gmail-in-react-nextjs-graphql-app-1ap5) 发送交易邮件。

在应用程序中，我们在前端使用 Apollo 客户端，从 Prisma 数据库中获取数据。因此，我们使用变异和查询组件。在其中一个组件中，我们面临致命的“渲染道具回调地狱”，有三级回调。

这里可以找到 **Bill.js** [的启动代码。](https://github.com/nabendu82/BillingRestro/blob/c3728cbb0bfc5c0d8ab4e32e3c4eaab3df661d0b/frontend/components/Bill.js)

[![The three level of render props](img/211eb893f73e2548306b37866bf63dc1.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ZTa4_CKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2394/1%2AB2Lblttn9-rNymA98w5QuA.png) *三级渲染道具*

这里的**用户**是一个通用的查询组件。

[![User component](img/7d0ef7180e8d3bf8fe1b75a0cf2fa9f9.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--eZ0ZfVWh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2066/1%2Ag2XfC37R3-zYNfW-J0H52g.png) *用户组件*

为了解决这个问题，我们使用了一个名为 [react-adopt](https://github.com/pedronauck/react-adopt/blob/master/README.md) 的第三方工具。

所以，让我们用 npm 安装**react——采用**并在我们的组件中使用它。这里，我们将创建一个组合组件并使用 adopt。

在这里，我们在对象中写入三个不同的键，其值将等于我们将要替换的突变/查询。

[![Composed](img/26fe3bf3cee22c1f0e3722010aca9eda.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--vHTjQNbd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2AVIZrK1Piiphphcy1f_V58Q.png) *沉稳*

我们现在将看到反应-采纳的好处。首先，我们将使用**合成**组件替换**用户**组件，并更改顶层渲染道具，以使用 adopt 中的所有键。

[![Replacing User component](img/724da89389bac18d66ba04b62397ee2a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--OQHoIsbU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2606/1%2A7wywBejF0ejvMD4rgyde7g.png) *替换用户组件*

现在，是时候删除切换车突变。继续删除它的开头和结尾。

[![Mutation deleted](img/53ef0945001b5e0ae3b938b598f08997.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Gs38UEB2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2636/1%2AoXMWoinfAZV4N9UuFzmNjg.png) *突变删除*

接下来，是时候删除查询组件了。

[![Query component deleted](img/3f4dfd9f228dde57386ebf54a0105722.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--OpuIrUo---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2788/1%2A8eB2sreLuMjM0l2DcMSkTg.png) *查询组件删除*

现在，如果我们回到前端检查，我们会发现一切正常。但是如果我们打开控制台，我们会发现三个警告。

[![Warning spoiler](img/7d81830434fbc65dc6153d0df1126597.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--7o2ww-qK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2880/1%2ArZqSS9kE9OhdlRxNL3D_dg.png) *警告剧透*

为了解决这个问题，我们必须为每个值在**组合**组件中创建子组件。

[![Our updated Composed](img/707841f917ae67553bf05db310bdbf9d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--odCDslcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2800/1%2ALs9bKFd_MiGrpjg5-MnTmw.png) *天华更新沉稳*

这将解决我们的警告问题，我们得到一个最小的和可读的代码，与上层渲染道具。

你可以在这里找到 **Bill.js** [的更新代码。](https://github.com/nabendu82/BillingRestro/blob/45f7da36d592316bb7a499bbe3d8d926c905235e/frontend/components/Bill.js)