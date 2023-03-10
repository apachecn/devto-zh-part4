# 什么是测试计划/测试方法？以及如何准备？

> 原文：<https://dev.to/priteshusadadiya/what-is-the-test-plan-test-approach-and-how-to-prepare-it-351>

<small>这篇文章最初发表在我的[自己的博客](https://www.priteshusadadiya.com/post/preparing-testplan-testschedule/)<small></small></small>

 <small>当我开始我的载体时，测试文档和计划是我最不喜欢的事情。主要是因为对我来说，编码比其他任何东西都重要，我让其他人(高级工程师)来负责测试计划和文档。

但是随着你在你的领域里越来越进步，你开始明白为什么这些事情很重要。在测试任何软件时，我们都需要注意很多事情。

在这篇文章中，让我解释一下每个人在设计任何给定软件产品的测试过程和文档时应该遵循的几点。

1.  创建测试计划。
2.  准备测试时间表。
3.  不同测试类型的简介
4.  在任何生产发布之前，应完全满足退出标准。

### 如何准备测试计划/测试方法。

任何项目都需要准备大量的文件和各种表格。

**为什么？**
因为这些文档对于理解项目是必不可少的，因为它们是整个应用程序的蓝图。

就 QA 而言，测试计划(也称为测试方法)也属于同一范畴。

因此，让我们分解整个文档流程。

```
In General Test Plan Includes the following data.

1\. Test Scope
   - In Scope
   - Out of Scope
2\. Test Schedule
3\. Test Types
4\. Test Environment
5\. Test Approach
6\. Exit Criteria
7\. Open Risks/Issues
   - Risks
   - Issues 
```

Enter fullscreen mode Exit fullscreen mode

#### 1。测试范围:

“将在测试周期中包含的区域/功能”被称为测试范围。

换句话说

> 测试范围是定义需要测试哪些特性和功能，然后确保所有定义的特性和功能都得到彻底验证的过程。

***举个例子，***
对于智能搜索(根据终端用户输入的关键字过滤数据)的实现有了新的要求。

现在，当这个特性出现在新版本的 QA 池中时，整个测试的焦点应该仅仅是这个特性，而不是其他。这可以作为测试范围的类别。

现在想象一些其他的需求也需要被包含到下一个版本中，所以这些项目将被包含到那个版本的测试范围中。

此外，该过程可以分为两个部分。

```
1\. In Scope: - Features / Functions that are to be tested
2\. Out of Scopes: - Features / Functions not to be tested 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。测试时间表:

所有要测试的东西都应该按照时间表进行(建议的和批准的交付期限)。

测试时间表可以包括各种阶段。下面列出了其中一些，

```
 A. Requirement Understanding
    B. Test Plan creation
    C. Test Cases creation
    D. Test Execution in Different Environments
    E. QA Sign-off* 
```

Enter fullscreen mode Exit fullscreen mode

您必须分别提供每个阶段的开始日期和结束日期，

以下是对各个阶段的描述，这有助于确定每个阶段的潜在开始和结束日期。

***A .需求理解*** :这个阶段开始之前需要发生的事情很少，

```
1\. All the requirements should be clear and properly documented.
2\. All Support document should be available.
3\. Basic structure of the given requirements should be available, 
    like wire-frames, Design mocks, Data flow etc. 
```

Enter fullscreen mode Exit fullscreen mode

*   如果正确遵循以上三点，不同阶段的大部分时间框架都可以从需求本身推导出来。

***B .测试计划创建*** :准备这个文档本身要花多少时间？

对于测试计划的每一个阶段 **【需求理解】** 是你将真正知道完成事情需要付出的努力的部分。

c .测试用例的创建 :一旦需求被确定并被适当地记录，你将能够很容易地从它们中起草测试用例，并且也能够提供同样的时间框架。

***D .测试用例执行*** :这是一个有点棘手的部分，因为这将取决于不同测试环境的细节。

例如，如果你在 Chrome、Firefox、IE 和 5 个不同的设备上运行一个测试，那么你将有一个在一个周期内执行 8 次的测试用例。
考虑到这一点和其他细节，比如，

```
 - How May Test Cycle you are performing?
    - What is the Scope of each Cycle? 
    - Each cycle can take different amount of time to complete. 
```

Enter fullscreen mode Exit fullscreen mode

将决定每个测试循环所需的时间。

***例如*** ，第一个周期总是比第二个周期长一些，因为在第一次迭代中发现 bugs 缺陷的概率很高。
随着周期的变化，在每次迭代中发现的 bugs 缺陷数量会变少。

基于上面的细节，您可以找到测试用例执行的可能开始和结束日期。

E. QA 签核 :你如何确定测试已经完成？显然，您需要某种标准来确保您对测试中的应用程序按预期工作有信心。
该标准将被设定为预先商定的出口门定义，一旦达到，测试将完成。

#### 3。测试类型/类别:

整体应用将包括以下测试类型/技术*:

以下测试深度/测试技术列表用于评估应用质量:

```
-   1\. FT: Feature > Basic Feature Testing
-   2\. G: GUI > Validate look and feel of the application
-   3\. DB: Database > Verify DB interaction and CRUD procedures
-   4\. E2E: End to End > Validate E2E business flows and system interaction
-   5\. BR: Business Rule > Validate specific business rules with positive/negative conditions
-   6\. SVC: Service Level Testing > Validate web service level features 
        (i.e. API`s, WS calls, etc.)
-   7\. EH: Error Handling > Validate Application Error handling
-   8\. BVA: Boundary Value Analysis > Review boundary values for feature/system interaction
-   9\. ST: State Transition > Validate application state 
        (i.e. workflow, session, restful state validation, etc.)
-   10\. BT: Branch Tree/Decision Matrix > Validate inputs/outputs of a group of rules 
-   11\. DF: Data Flow > Validate system interaction
        with a concentration on ETL and CRUD procedures between system components 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。测试环境:

将执行测试用例的所有环境的列表。

#### 5。测试方法:

在这里，您应该提供要测试的特性和其他项目的描述，以及用于发现缺陷的总体测试深度和测试方法。

#### 6。退出标准:

在 QA 签发生产放行之前，必须满足退出标准。

换句话说，“在提供签准之前应该完成的所有事情都被称为退出标准”

以下是退出标准的一些例子

```
 1\. All Sprint Stories/Items Accepted by QA and it is Ready for Production
    2\. Manual Regression Tests (Blocker and Critical Test Cases) have passed
    3\. No Critical or Blocker defects outstanding.
    4\. Automation Regression Suite Successfully Passed.
    5\. Performance Tests executed successfully.
    6\. Browser Compatibility works as expected. 
```

Enter fullscreen mode Exit fullscreen mode

*   如果可能，包括以上所有内容，并有一个字段来标记标准是否适用。如果是，则标记为“是”，否则标记为“否”。
*   为了更好地理解，还应针对每个标准提供注释。

#### 7。未解决的风险或问题:

包括所有尚未修复的 bugs 问题。

*   添加问题与其描述和优先级的适当链接。
*   这些信息是非常重要的，并且会对构建是否应该进入下一个阶段(产品发布)产生影响。

希望这些信息能有用。

### 感谢阅读！</small>