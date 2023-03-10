# Redux:故障及解决方案。

> 原文：<https://dev.to/m_t_nt/redux-troubles-and-solutions-2kea>

# Redux 就在我们中间。

这是最受欢迎的国家经理之一。

它是可预测的，使用简单。它有助于组织存储和变异应用程序的数据。如果我们考虑到动作和 redux 是 redux 的一部分，我们可以说 redux 是一个 app 所有领域逻辑(又名业务逻辑)的存储。

# 一切都像看上去那么美好吗？

尽管非常清晰和简单，但我们在使用 redux 时可能会遇到问题。

应用程序的数据放在 redux 状态中的一个普通 javascript 对象中。它可以用通常的方法得到，我们只需要**知道路径**。

我们如何构建这些数据？只有两种选择:平面列表和层次模型。

例如，对于只有一个简单计数器的应用程序来说，平面列表是一个完美的选择。对于一个更复杂的应用程序，我们需要使用一个层次结构，其中每个下一个深度阶段都有关于整个数据的有限信息。这没问题，但是一旦数据变得更加分层**，获取数据的路径就会变得更加复杂**。

```
const dataHierarchy = {
  user: {
    id,
    name,
    experience,
    achievements: {
      firstTraining,
      threeTrainingsInRow,
    },
  },
  training: {
    currentSetId,
    status,
    totalAttemptsCount,
    attemptsLeft,
    mechanics: {
      //...
    },
  },
}; 
```

在这个例子中，用户的数据用`user`路径存储，并且它有子数据`achievements`。而成就应该对所有用户的数据一无所知。

某些`mechanics`也是如此——它不应该知道用户的尝试次数还剩多少，它是一个训练的数据。

* * *

没有模块化使用方式的分层数据结构导致**需要知道到达目标数据**的完整路径**以及我们想要使用它的每个位置**。换句话说，它造成了数据结构和数据显示系统的**耦合，并导致重构或/和重构数据的困难。**

[![](img/5757ebb620e2162a60497f2f1cb36200.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jvl-cFKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e0i34kyq8ferbowzu1eo.png)

有人可能会说有一些强大的 IDE，它可以通过一个命令改变很多路径。但是很难改变复杂的路径或者在变量中有自己部分的路径。

* * *

还有一件事:测试。redux 文档中有一篇关于测试的大文章。但是，如果我们想测试的不是简单的单元，比如减速器或动作生成器，该怎么办呢？

数据、动作和缩减器通常是相互关联的。逻辑相关数据的单一树，通常由几个必须一个接一个测试和一起测试的 reducers 服务。

将选择器添加到此列表中。其结果取决于减速器和其他因素。

因此...我们可以将它们作为独立的单元进行测试，但是在这种情况下，我们需要使用除了逻辑或约定之外没有任何引用的对象。

* * *

好吧，如果我们做一个结构，比如一个用户数据，包括朋友列表，喜欢的歌曲和其他东西，以及一个通过动作和 reducers 控制这些数据的方法。而且，也许我们确实为整个功能写了很多测试。现在我们在兄弟项目中也需要同样的东西...

**如何简单的分享代码？**

[![I need help](img/3f4b1be93bf6f2240e8a0e4766d92080.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sqoVCRj6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30yp89qm776c0h3g5sbd.gif)

# 寻找解决办法

在决定如何保持优势和消除劣势之前，我们需要研究数据生命周期中的依赖关系:

*   动作报告是否由用户引起的事件
*   Reducers 对动作做出反应，改变或不改变数据的状态
*   数据突变本身就是一个事件，可以成为另一个数据突变的原因![](img/60ae07a2d90f2d00ae4622c07a5be6ed.png)

这里的控制器是处理用户交互和状态数据突变的抽象。它决不能是一个单独的实体，它通常散布在组件上。

# 将所有冗余单元合并到一个黑盒中

如果动作、归约器和选择器将被合并到一个模块中，并教会它不依赖于某个路径来访问自己的数据，那会怎么样？

如果(比如)`user`的所有动作都是通过调用实例的方法:`user.addFriend(friendId)`来完成会怎样？而数据将由 getter 得到:`user.getFriendsCount()`？

如果它能够通过简单的导入来导入`user`的全部功能呢？

```
const userModule from ‘node_modules/user-module’; 
```

似乎不错？尤其是考虑到你不会需要写很多支架:
npm 包 [redux-module-creator](https://www.npmjs.com/package/redux-module-creator) 提供了所有用于创建的功能**非耦合、可重用、可测试的 redux-modules** 。

每个模块由控制器、减速器和动作组成，具有以下优点:

*   只要调用 method-integrator，就可以把它集成到你想要的任何地方的商店里。对于改变位置，我们只需要改变积分器调用及其参数:
    [![](img/e400d9bec288cd2bf64c14d1f3d1d7c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gKwjT6KB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x81qignw9nv3794fkhwt.png)

*   控制器通过存储曾经传递到`integrator()`中的路径，在存储器中具有其自己的数据部分的 ref。这样就不需要知道数据使用的路径:
    [![](img/f06c78a5c3e7146a12535348bf67f472.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9QD5msfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3n4u03iugn693nkhdgqs.png)

*   控制器是所有选择器、适配器等的容器；

*   有机会订阅“自己的”数据的变化；

*   Reducers 可以使用`this` -模块实例来接收模块的动作类型。这样就不需要导入大量的动作，降低了出错的概率；

*   动作成为模块的一部分后，就有了使用的上下文:它不再是`trainigFinished`，现在是`readingModule.actions.trainingFinished`；

*   动作现在在一个名称空间中，它使得在不同的模块中使用相同的动作名称成为可能；

*   每个模块可以被实例化几次，并且每个实例可以被集成到商店上的不同部分中；

*   来自同一个模块的不同实例的动作有不同的动作类型，因此可以对某些实例的动作做出反应。

结果，我们得到了一个黑盒——一个可以自己管理自己数据的模块，并且有一个 API 与外部代码交互。

但是它仍然有单向数据流、清晰性和可预测性。由于它仍然是相同的 redux 和 reducers，你可以任意组合它来构建你的应用需要的任何结构。