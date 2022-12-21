# 如何测试以太坊智能合约:审计最佳实践

> 原文：<https://dev.to/smartym/how-to-test-ethereum-smart-contracts-audit-best-practices-53kg>

当开发一个基于区块链的项目时，首先要做的是确保智能合约的安全性和代码质量。否则，你可能会损失一大笔钱。

你可能听说过，平价以太坊钱包因为有代码漏洞损失了 3000 万美元。为此，Satoshi Pie fond 损失了 32.4%的资产。2016 年，攻击者黑了 DAO，拿走了 5000 万美元。

这些例子清楚地表明，即使是一个微小的缺陷或错误也会导致不良后果。因此，智能合约测试是创建智能合约的关键部分。

他们审计的目的是发现代码错误，检查程序逻辑，确保一切正常。高质量的审计将使你消除风险和焦虑，并节省你的精力和资源。

## 如何准备测试智能合约

准备具有重要的作用，帮助审计员定义项目目标，更快地定义漏洞，并改进测试过程。

此外，请记住，准备工作从您开始创建智能合同的那一刻就开始了。这就是为什么了解智能合同漏洞以及防范这些漏洞的方法非常重要。

### 我建议遵循以下建议:

**1。提供详细的规格说明**

[提供一个清晰的技术规范总是一个好主意](https://smartym.pro/blog/how-to-write-software-requirements-document/)，因为它帮助区块链开发者清楚地理解 smart 契约的目标、用例以及工作原理。

虽然拥有它很重要，但许多公司并不创造它。然而，大多数工程师表示，该文件能够理清思路，增加项目成功的机会。

在规范中，描述智能合约的预期行为:每个功能应该如何工作，不应该如何工作，以及所有功能应该如何协同工作。使用图表，因为它们也可以让你看到可能的替代方案，并了解人们是如何误解你的代码的。

**2。记录部署智能合同的流程**

尽管您的智能合约的代码可能很棒，但是在部署它之前还有一些重要的步骤要做。为了避免灾难和节省团队的努力，提供部署过程的文档。

一个记录良好的过程，包括合同的部署顺序、哪个编译器版本将用于哪个合同等。，将帮助您防止智能合约漏洞。

**3。清理项目库**

一个没有混乱的高质量的智能合同代码将促进审计员的工作并使其自动化。为测试过程准备项目时，删除任何未使用和不必要的文件、合同或代码片段。

此外，您应该指定哪些契约仅用于审计(意味着它们不是最终系统的一部分)，以及哪些契约是从测试代码中重用的，或者是从验证代码中继承的。

## 如何测试以太坊智能合约

智能合同审计的过程类似于任何其他代码的测试:在预定义的环境中创建一组标准方法调用，并为它们的结果编写语句。

**审计是一个过程，包括:**

*   测试开发
*   智能合同状态更改的测试
*   事件测试
*   误差测试
*   检查邮件发件人

为了审计您的项目，使用行为驱动开发(BDD)实践是很方便的，它和测试一起使您能够创建文档和用例。现在有各种各样的框架、库和其他工具用于审计智能合同。

虽然 Solidity 中的测试开发受限于这种编程语言的能力，但是您可以使用 JavaScript、Truffle framework、奇偶校验以及其他可靠的技术。

## 用 Truffle 审计智能合同

今天  是以太坊最流行的框架。Truffle 是一个 Node.js 框架，用于编译、链接和部署智能合约。该框架以完全模块化的方式编写，使工程师能够选择他们需要的功能。

在 Truffle v.2 中，测试是在 JavaScript 上创建的，而在 Truffle v.3 中，开发人员增加了在 Solidity 上编写测试的能力，其语法类似于 JavaScript。

Truffle 提供了大量有用的功能，包括[二进制管理](https://en.wikipedia.org/wiki/Binary_repository_manager)，库链接，定制部署支持，迁移框架，可脚本化部署，访问数百个外部包，外部脚本运行器，以及使用 Mocha 和 Chai 的自动化合同审计——避免失败的一切都是智能合同。

说到区块链系统通常不能很快运行的事实，审计人员使用区块链测试客户端，例如 TestRPC，它几乎完全模拟了以太坊客户端的 JSON RPC API 的工作。

除了标准方法之外，TestRPC 还实现了许多额外的方法，比如 evm_increaseTime 和 evm_mine。应用 TestRPC 的一个很好的替代方法是使用一个标准的客户机，例如，Parity，它运行在 dev 模式下，可以立即确认事务。

要开始使用 Truffle framework，请通过 npm 安装它:

*npm 安装-g 块菌*

然后，执行 truffle init 命令来创建项目结构:

*$ mkdir 坚固性测试示例
$ cd 坚固性测试示例/
$松露初始化*

合同必须位于合同/目录中，测试必须位于测试/目录中。当你编译契约时，Truffle 期望每个契约都放在一个单独的文件中，并且契约名等于文件名。

## 使用 Truffle 框架进行测试开发

测试使用 JavaScript 对象，这些对象表示处理契约的抽象，在对象操作和以太坊客户端的 JSON RPC 方法调用之间建立映射。这些对象是在您编译*的源代码时自动创建的。sol 文件。

所有方法的调用都是异步的，并返回承诺，因此消除了跟踪事务确认的顾虑。为了避免大量不可读的代码，使用 async/await 编写测试。

此外，为了简化工作，最好不要混合迁移和测试实例的创建。相反，在调用每个测试函数之前，通过使用创建新契约实例的代码在测试中创建它们。为此，可以使用 beforeEach 异步函数。

*const congressional params = {*
*minimumQuorumForProposals:3，*
*minutesForDebate: 5，*
*marginofvotesformamajory:1，*
*congress leader:accounts[0]*
*}；*
*让国会；*
*before each(async function(){*
*congress = await congress . new(…object . values(congressional params))；*
*})；*

## 如何测试事件

以太坊中的事件可用于以下目的:

*   从更改协定状态的方法返回值
*   创造改变智能合约状态的历史
*   作为一个便宜方便的信息库

让我们举下面的例子。您需要检查当您调用新的 proposal 方法，向合同添加一个句子时，是否创建了一个事件记录 Proposal Added。

为此，首先，在测试中创建一个 [DAO](https://en.wikipedia.org/wiki/The_DAO_(organization)) 的成员，并代表它创建一个提议。然后为 ProposalAdded 事件创建一个订阅者，并检查在调用 newProposal 方法后，事件是否发生，以及其属性是否与传输的数据相对应。

*一个 newProposalfunction 代码:*
*/*函数创建一个新的建议*/*
*函数 newProposal(*
*地址受益人，
uint etherAmount，
string JobDescription，
bytes transactionBytecode*
*)*
*only members
returns(uint proposal id)*

## 如何测试智能合约状态的更改

在以太坊中，addMember 方法负责更改智能合约的状态。为了测试这个方法，检查它是否记录了关于成员结构数组的 DAO 参与者的信息。

*/*make member*/
function add member(address target member，string memberName)only owner {
uint id；*
*if(memberId[target member]= 0){
memberId[target member]= members . length；
id = members . length++；*
*members[id]= Member({ Member:target Member，memberSince: now，name:memberName })；*
*} else {
id = memberId[target member]；
成员 m =成员[id]；
}*
*membership changed(target member，true)；*
*}*
*函数 remove member(address target member)only owner {*
*if(memberId[target member]= = 0)throw；*
*for(uint I = memberId[target member]；i
*成员[i] =成员[I+1]；*
*}*
*删除成员[members . length-1]；*
*members . length-；*
*}**

使用带有测试帐户的数组，在测试中添加契约参与者。然后检查成员函数是否返回输入数据。

需要注意的是，每次调用 addMembermethod 时，都会创建一个事务，并更改区块链状态，即信息记录在分布式分类帐中。

## 如何测试消息发送者的错误和检查

停止契约方法工作的一般方法是使用 throw 指令创建异常。如果您的任务是限制对方法的访问，则可能需要一个异常。

让它实现一个修饰符来检查一个调用了方法的帐户地址:如果它不符合条件(调用方法的不是契约所有者)，就会创建一个异常。

## 避免聪明合同错误的一些建议

**1。使用可靠性测试覆盖率**

对于智能合同审计，使用可靠性覆盖。它将帮助您测量测试覆盖率，并定义尚未测试或需要更多关注的代码片段。当然，100%的测试覆盖率不是万能的，所以从一开始就写一个高质量的测试代码也是很重要的。

**2。使用棉绒**

通过使代码更容易阅读和审查，Linters 使以太坊开发人员能够提高代码质量。有许多棉绒你可以使用。例如，Solcheck 是一个用于 Solidity 代码的 JavaScript linter。

Solhint 是一个 linter，帮助工程师避免 Solidity 智能合同中的错误和漏洞。Solhint 提供安全性和样式指南验证。

**3。进行静态分析**

在智能合约测试中，静态分析允许发现代码漏洞和不安全性。

要进行代码分析，您可以使用这样的工具，如分析代码并检测常见漏洞的 Oyente，允许在 EVM 支持下进行动态二进制分析的蝎狮，以及能够可视化智能合约功能控制流并识别潜在不安全性的 Solgraph。

**4。为失败做准备**

尽管经过了精确的测试，智能合约仍可能存在一些错误或漏洞，您应该随时为失败做好准备。因此，请确保您的代码能够响应智能合约错误。

为此，创建一个有效的升级计划来修复错误和增强代码。通过建立费率限制和最大使用量来管理风险金额。另外，如果出了问题，暂停你的合同。