# 一种固执己见的测试角度的方法

> 原文：<https://dev.to/gparlakov/an-opinionated-approach-to-testing-angular-492f>

<figure>[![](img/5b7278d2aa80364a4a966fb0cef1b88e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Szqxxwv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbiRyJxXPSuGSXADNxgyhQw.png) 

<figcaption>棱角分明外加俏皮话。Logos 出自[https://worldvectorlogo.com/logo/angular-icon](https://worldvectorlogo.com/logo/angular-icon)[https://freebiesupply.com/logos/jest-logo/](https://freebiesupply.com/logos/jest-logo/)</figcaption>

</figure>

#### 通过几年的写作、阅读和依靠测验而获得信息。

##### 原载于[https://medium . com/ng-gotchas/an-opinious-approach-to-testing-angular-4c f14 ef 7463 f](https://medium.com/ng-gotchas/an-opinionated-approach-to-testing-angular-4cf14ef7463f)

首先，让我展示一下**的基本要点**，然后我们将进入**的细节**。

> 免责声明:我不会试图说服你单元测试是一个好主意。如果你觉得不是，去读点别的吧。如果您在日常工作中不依赖单元测试，这里表达的观点可能会显得武断和错误。

**1。模板**中没有逻辑——只是调用一个事件**处理程序**公共**方法**。我们可以**信任 Angular** 去做它的那部分(那已经被测试过了— [例子](https://github.com/angular/angular/blob/master/packages/common/test/directives/ng_if_spec.ts))，那就是召唤我们。并且**我们**做我们的部分——仅**测试**我们的**代码**。

**2。**包含**的所有依赖**作为**可注入的**构造函数参数。Document，Date.now()，requestAnimationFrame 等。

**3。**模板使用的所有**属性**和**方法**都是* *public ** 。这对你来说可能是显而易见的，但最初对我来说不是。而语言服务部门并没有告诉我其他的事情。

**4。**中的**每个测试**都有一个设置功能，为测试设置所有需要的状态和基础设施。

**5。**在设置函数中使用**构建器模式**来**设置****依赖关系**和**构造**被测类。

**6。**使用 Jest([Jest . io](https://jestjs.io/))【Jest 的快照功能(这个有点用)】

7 .**。**对于按钮点击和输入类型，即用户使用用户界面的目的，创建一些实际使用用户界面的用户界面(又名端到端)测试

好的——现在来看一些细节:

**1。在**模板**中没有逻辑**——只是调用事件处理程序的公共方法——这样我们可以信任 Angular 做它的部分(它已经被测试过了[例子](https://github.com/angular/angular/blob/master/packages/common/test/directives/ng_if_spec.ts))。**我们只测试****我们的代码**。考虑同一模板的以下两个版本: