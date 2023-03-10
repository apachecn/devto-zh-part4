# ETHEReact:构建一个全栈去中心化的电子商务应用，部署到以太坊虚拟机并使用 ReactJS 进行交互

> 原文：<https://dev.to/pruthvikumarbk/ethereact-build-a-full-stack-decentralized-e-commerce-app-deploy-to-ethereum-virtual-machine-interact-using-reactjs-54l7>

[![A picture is worth a thousand words!](img/06ce9a8c98b6b8f79d9d2892bcb0b682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4m0zpG4_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AEzRAe89i9ucaUn-bpjO_qQ.png)

## TL；速度三角形定位法(dead reckoning)

*   这不是一篇解释区块链或营销密码的文章。

*   这篇文章的重点是方便任何开发人员和/区块链爱好者在区块链上构建一个原型；端到端。到本文结束时，您将构建一个基本的分散式电子商务平台。

*   在本文中，您将在 Solidity 中编写两个合同*(一个给供应商&另一个给客户)*，编译您的合同并将其迁移到 EVM，创建一个简单的 ReactJS 前端，通过 web3.js 将您的前端连接到 EVM

好了，既然你感兴趣，让我们开始编码吧！没有绒毛的权利！

### 先决条件:

*   本文假设您了解如何在开发机器上安装 *NodeJS* ，如何从 *npm* 下载更新的包，并且对 *ReactJS 有基本的了解。*

*   您已经在开发机器上安装了最新版本的 [NodeJS](https://nodejs.org/en/) 。

*   install[Truffle](https://truffleframework.com/)NPM install-g Truffle Truffle 是以太坊的一个开发和测试框架。使用 Truffle，您可以在以太坊网络上编译、测试和部署智能合约。

*   install[test RPC](https://www.npmjs.com/package/ethereumjs-testrpc)NPM install-g ether eum js-test RPC test RPC 是基于 Node.js 的以太坊客户端，用于测试并使开发以太坊应用程序更快。

*   在其他文件夹中安装 [Create-React-App](https://github.com/facebook/create-react-app) 。可能在你的根里。npm 安装-g 创建-反应-应用程序

*   为您的应用程序创建一个文件夹。可以称之为 MyDApp mkdir MyDApp &更改为新创建的文件夹 cd MyDApp

*   这将创建前端。生产级 React 只需发出一条命令即可构建！不能再好了！对吗？为了这个演示，我们将不构建包；但是，运行应用程序即可。npm 在同一文件夹中启动；您应该在端口 3000 上运行 ReactJS smooth(除非它被指示在任何其他端口上运行)

[![](img/e33468c5e15381f6cfa53ad1de55b186.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F_72A0r3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AUV1QhlwqwLkOY7nWxayFXA.png)

### 后端:

满足所有先决条件后，让我们专注于为我们的 DApp 创建一个功能性的区块链后端。

*   更改到应用程序的根文件夹 cd MyDApp

*   为后端创建专用结构。mkdir 后端和 cd 后端

*   通过 truffle init 初始化 Truffle(在 Windows 10 中，如果这导致*Microsoft JScript 运行时错误，*将您的命令更改为 truffle.cmd init)这将初始化文件夹结构以创建和部署智能合约。

*   导航到名为 truffle.js 的文件，确保它包含以下内容:

*   在 contracts 文件夹中，现在创建一个名为 Supplier.sol 的新文件，让我们编写一些 [Solidity](https://solidity.readthedocs.io/en/v0.4.25/) 来定义我们的供应商后端逻辑。

上述担保合同包括:

*   状态变量:把这些看作是整个契约中可用的全局变量。

*   交易:这些是在区块链中添加&/更新&/删除数据的功能。

*   Getters:这些也是函数；但是，只能从区块链读取数据。

在处理区块链时，理解交易功能需要花费“汽油”是很重要的。这意味着，像存储新数据、从区块链更新和删除现有数据这样的操作不是免费的！解释 GAS 不在本文范围内。这个 [stackexchange 回答](https://ethereum.stackexchange.com/questions/3/what-is-meant-by-the-term-gas)很好地解释了这个单元。

然而，从区块链读取数据是免费的！Getter 函数帮助我们实现了这一点。

上述合同授权我们的后端支持电子商务平台中的“供应商”。

我们需要客户代码。在 contracts 文件夹中，创建一个名为 Customer.sol 的文件，让代码更加可靠。很有趣，不是吗？

现在，让我们编写一些将这些合同迁移到区块链网络的逻辑。

在里面。/MyDApp/BackEnd/migrations 文件夹中，创建一个名为 2_deploy_contract.js 的新文件，该文件应包含以下代码

恭喜你！你刚刚为你酷炫的去中心化电商完成了一个区块链后端的编码。

现在，让我们开始工作。

Solidity 是一种编译语言。让我们使用 truffle compile 来编译我们的代码(在 Windows10 上，您可能想要进行 truffle.cmd 编译)

没有编译时错误？恭喜你。现在，我们必须将合同迁移到以太坊网络。打开新的命令提示符/终端，并导航到。/MyDApp/BackEnd/。是时候让以太坊在你的机器上本地运行了。使用 test RPC-l 900000000-p 8484 来完成此操作

*   -l 9000000000 表示网络的气体极限。在生产系统上，这是真金白银！(当然是密码)。在您的机器上，您拥有控制权！(感觉很有钱吧？)

*   -p 8484 指示 RPC 服务器必须侦听的端口。

[![Congratulations! You’re running private Ethereum on your machine!](img/a58545f6a10eac67d7e7ed0e43bdad0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6SqgxDnP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2844/1%2AknIdXNKXemmNh9Mn-OErXQ.png)

让 cmd/终端保持运行。再打开一个，改成。/MyDApp/BackEnd。我们要把我们的合同转移到我们刚刚开始的网络中。我们使用 truffle migrate 来实现(同样，在 Windows10 上，您可能希望使用 truffle.cmd migrate)

[![On successful migration, you should have a screen that looks like above.](img/ab92b5aef68157633fbc6363b334fe60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vgKJkat3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2382/1%2AD77simLqAM9-K1PqXSVgDg.png)

当您的合同迁移到网络上时，请注意运行 rpc 服务器的另一个终端上的活动。区块链已经把你的合同记录到它的系统里了！唷，我们在飞！！！

**合同地址'&其他配置**

对于您的前端与区块链后端的通信，它需要一些配置信息。让我们获取这些信息并保存起来以备将来参考。

*   获取部署到网络的每个合同的地址。

*   打开另一个 cmd/终端并导航至。/MyDApp/BackEnd 使用 truffle 控制台获取 truffle 控制台(在 Win10 上，truffle.cmd 控制台)

*   使用 Supplier.address 获取供应商合同地址存储此地址。我们很快就需要这个。

*   使用 Customer.address 获取客户合同地址存储此地址。我们很快就需要这个。

*   对于 [web3](https://github.com/ethereum/web3.js/) 要与以太坊联姻作出反应，需要签约 ABI。同样，解释 ABI 超出了本文的范围；但是，这个 [stackexchange 回答](https://ethereum.stackexchange.com/questions/234/what-is-an-abi-and-why-is-it-needed-to-interact-with-contracts)做得很好。

*   使用 JSON.stringify(Supplier.abi)获取供应商合同的 ABI。一旦去掉开始和结束引号，终端就会抛出一个巨大的字符串，本质上就是 JSON。使用 [JSONlint](https://jsonlint.com/) 来帮助你格式化 JSON。存储这个 JSON。我们很快就需要这个。

*   类似地，再次使用 JSON.stringify(Customer.abi)获取客户合同的 ABI，删除第一个和最后一个报价，格式化并保存它以供将来参考。

就是这样。你已经为你的酷派去中心化电商 app 进行了区块链后端的编码、编译、迁移、获取配置！

只剩下前端了。我们开始吧！

**前端:**

作为先决条件的一部分，我们在全球范围内安装了 create-react-app &为我们自己创建了一个 react app。让我们转到那个目录。光盘。/MyDApp/前端

我们需要为 React 连接到区块链后端提供配置信息。让我们使用下面的代码来实现这一点:

*   在/src 目录中创建一个名为 EthereumSetup.js 的配置文件。这是你从前面的步骤中抓住供应商和客户 ABI 的时机。

*   为供应商创建前端组件，将产品添加到市场并处理采购订单。在/src 文件夹中，创建一个名为 Suppliers.js 的文件

*   注意事务函数(第 115、127、140 行)是如何需要 gas 来完成事务的。如果你想知道我如何预先确定气体限制为 20000；答案是，随机！在测试网络中，您将不必担心气体限制。当你使用公共网络时，这是一个很重要的考虑因素，因为我们谈论的是真金白银。事务可用的 gas 越高，网络完成事务的优先级就越高。

*   现在，与 Suppliers.js 类似，我们需要前端逻辑和组件，以便客户查看市场中的可用项目并下订单。在/src 目录下，创建一个名为 Customers.js 的文件

*   另外，请注意事件侦听器。在组件被加载到 DOM 后触发这些事件监听器是很重要的。您可以选择观看特定区块的事件，或者选择从 0 到最新。使用后者，您将从第一个区块开始捕捉区块链中记录的所有事件！

*   供应商和客户组件已经准备就绪，我们离完成前端代码只有一步之遥。让我们搞定它。

*   找到 App.js 并确保它反映了以下代码:

*   就是这样。您已经完成了分散式应用的前端代码！相当惊人！DApp，从一无所有到拥有一大笔钱，你已经走了很长的路！恭喜你！！

*   要激活站点，请打开一个 cmd/terminal 并更改为。/MyDApp/FrontEnd 并发出此命令— npm start

*   哇哦。！！你能看到区块链后端的活动吗？这是所有来自前端的事件观察者从后端订阅事件发射器。对于了解 pub-sub 的人来说；这有点类似！你现在有一个功能区块链供电的去中心化电子商务应用程序！

*   如果你不知道下一步该做什么，查看 App.js 中第 22 到 27 行的说明。

## 结论

这篇文章帮助你从头开始创建一个完整的堆栈 DApp。你将理解如何在 Solidity 中编写智能合约，启动你的区块链后端，并通过 RPC 服务器将其暴露给 HTTP。然后在前端使用 Web3 与以太坊后端结合来反应前端。因此以太行动！

我会很兴奋地从你新获得的技能中了解到你制造的新产品。我将非常乐意在这个激动人心的旅程中支持你。你可以在这里找到[ether act 的代码库](https://github.com/PruthviKumarBK/Decentralized_eCom)。

如有更多问题/反馈/意见，您可以联系我@[pruthvikumar.123@gmail.com](mailto:pruthvikumar.123@gmail.com)

快乐编码，

[普鲁斯维](http://www.apricity.co.in)|【http://www.apricity.co.in】T2