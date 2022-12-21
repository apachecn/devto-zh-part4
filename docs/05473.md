# 在 Apex (Salesforce)上使用调度程序测试流程构建器

> 原文：<https://dev.to/airtonzanon/testing-a-process-builder-with-a-scheduler-on-apex-salesforce-43hi>

## 场景

您刚刚创建了一个提前 10 天运行的流程构建器，并且您想要为它创建一个 Apex 测试。

*   我有这个问题，因为在我目前的公司，我们正试图为所有新的过程构建者创建测试。

#### 问题

如果您更改对象的创建日期，它将不起作用，因为调度程序需要等待一段时间才能运行。

## 解

每次我们期望一个 process builder 运行，如果它没有立即动作，process builder 就会在`FlowInterview`表上创建一条记录，这是一种队列。

因此，如果在我们运行一个测试之后，我们在那个表中有一个记录，这意味着调度器工作了。

process builder 的设置是:每次创建任务时运行，如果它仍然`Not Started`，将在 10 天内关闭它。

然后是测试:

```
 @isTest
public class VerifyProcessBuilderTest {
    @isTest
    public static void verifySchedulerCreated() {
        Task task = new Task(
            Subject = 'Call Airton',
            RecordTypeId = '000a00000000AAA',
            Status = 'Not Started',
            Priority = 'High',
            Type = 'Call'
        );

        Test.startTest();

        insert task;

        List<FlowInterview> flowInterviews = [SELECT Id FROM FlowInterview];
        System.assertEquals(1, flowInterviews.size());
        Test.stopTest();
    }
} 
```

这将创建一个任务，但是注意它在`Test.startTest()`和`Test.closeTest()`调用之间，所以当我们插入任务时，它将触发流程构建器，下面我们将断言调度器是否工作。

## 结论

该测试不会测试 process builder 将做什么(这通常是我们在这种测试中所做的)，但是它将测试该 process builder 的操作是否是预定的，因此如果发生了变化，process builder 由于某种原因处于非活动状态，或者如果它不再被预定，该测试将在下一次部署中显示出来。

#### 结束语 1.1

请记住，每次我们部署流程构建器时，它都是不活动的，因此为了测试能够顺利运行，我们需要首先部署流程构建器，激活它，然后部署测试。(这不是最好的场景，但是，至少我们将有一些东西来测试流程构建器)。

如果你不想有这个问题，请阅读来自 Salesforce 的这个[发布说明](https://releasenotes.docs.salesforce.com/en-us/winter19/release-notes/rn_forcecom_flow_deploy_as_active.htm)。

#### 结论 1.2

几天前，一位同行和我谈论一个测试没有通过，因为它在`FlowInterview`表上发现了一些行，这是一个诡计，因为它应该没有行，在对我们的流程构建器进行调查后，我们发现了问题，我们忘记禁用我们不再使用的流程构建器。这就是这个测试的美妙之处，我们可以发现是否有什么东西触发了其他不应该触发的东西。

另外，我想提到的是，每次新的测试运行时，它都会清除所有的数据库，换句话说，当您运行新的测试时，表`FlowInterview`将是空的，如果某个流程构建器有一个调度程序，它将只有内容。