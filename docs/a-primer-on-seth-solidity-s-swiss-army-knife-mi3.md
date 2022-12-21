# 塞思入门，Solidity 的瑞士军刀

> 原文：<https://dev.to/liamzebedee/a-primer-on-seth-solidity-s-swiss-army-knife-mi3>

厌倦了用 JS 脚本写`new Web3`只是为了检查平衡？为把事情转换成 BigNumber 的摩擦而烦恼，加载`Contract.json`，处理承诺只是为了做一些简单的事情？让我给你介绍一下`seth`。使用`seth`，您可以利用命令行的速度和灵活性轻松地与智能合约进行交互。这是戴称赞的 MakerDAO 团队开发的一套工具的一部分。在这篇文章中，你将从 0 走向英雄。

## 塞斯简介

Github 上的 [Seth 参考指南](https://github.com/dapphub/dapptools/tree/master/src/seth)很棒，但是它遗漏了一些经验，如果你不是 CLI 的高级用户，这些经验不会很明显。我将从这些用例的角度解释一些非常简单的技术:

*   部署块菌合同
*   保存合同的部署地址
*   调用方法和传递参数
*   从不同账户进行交易
*   转换单位(数字、eth)

### 安装 seth

`seth`是`dapptools`套件的一部分，该套件是一个超级最小软件包(比如 5kB)。摘自他们的 [Github 指令](https://github.com/dapphub/dapptools) :

```
curl https://dapp.tools/install | sh 
```

Enter fullscreen mode Exit fullscreen mode

### 部署松露契约

Seth 基于 unix 哲学，将文件放在核心位置。您可能已经习惯了像 Truffle 这样的整体框架，当它处理业务时，您只能坐在后座。运行`truffle compile`，会生成`build/contracts/ContractName.json`形式的契约工件。运行`truffle migrate`，它将自动获取这些合同的字节码并部署它们。

这可能看起来很可怕，但实际上我们自己做细节并不复杂！

[![](img/7d64d5ab0777d8bbe9f23c8fb2bc4555.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--okDWUZei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/784r3viwrckuhwkblkoz.png)
[![](img/f1b0dca8a8470c8a93b85d48e31d547f.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--jne747f_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4x41j7q1s4tpby6tbqcx.png)

但是等等，`bytecode`和`deployedBytecode`有什么区别？嗯，当你在以太坊中部署一个合同时，你做了两件事:

1)您正在创建一个契约，它将运行时代码存储在链
上 2)您正在调用构造函数

简单地说——`deployedBytecode`只是运行时，而`bytecode`包含了构造函数的代码。

让我们看看使用 seth 部署合同有多简单。首先，我们要安装`jq`，一个可以解析 JSON 的 CLI 工具。你可以在找到[的安装说明。](https://stedolan.github.io/jq/download/) 

```
# Get the bytecode from the Truffle artifact
export CODE=$(cat build/contracts/ERC20.json | jq -r .bytecode) 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置 seth 的环境

运行以下命令来设置 seth 需要知道的环境变量:

```
# Connect to the local node
export ETH_RPC_URL=http://127.0.0.1:8545

# Disable below if not on Truffle
export ETH_GAS=4712388

# Use the unlocked RPC accounts
export ETH_RPC_ACCOUNTS=yes

# Get the 1st account and use it
export ETH_FROM=$(seth accounts | head -n1 | awk '{ print $1 }') 
```

Enter fullscreen mode Exit fullscreen mode

#### 部署合同

```
# We can write it simply
seth send --create $CODE

# Passing variables to the constructor
# In this example, the constructor is ERC20Detailed
# constructor(string name, string symbol, uint decimals)
seth send --create $CODE TokenName TOK 18 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的代码，您将得到如下输出:

```
seth-send: Published transaction with 3227 bytes of calldata.
seth-send: 0xb330d69093fd6b19d5afecffebf6734f77ef8d7cf47dabb87ad840a7d4b8e57f
seth-send: Waiting for transaction receipt....
seth-send: Transaction included in block 36.
0x243e72b69141f6af525a9a5fd939668ee9f2b354 
```

Enter fullscreen mode Exit fullscreen mode

最后一行是合同地址。

### 保存合同的调配地址

我在 Bash 方面并不熟练——但是我知道一件事:当我想保存部署地址时，我并不是在重新发明轮子。使用 Sourcegraph(这是 grep+git 的类固醇)，我只是[在 dapphub 的所有回购](https://sourcegraph.com/search?q=repo:%5Egithub%5C.com/dapphub+seth+send+)中搜索`seth send`，并能够抓取下面的行。

```
ERC20_ADDR=$(seth send --create $CODE --status 2>/dev/null)

# or even terser
ERC20_ADDR=$(seth send --create $(cat build/contracts/ERC20.json | jq -r .bytecode) --status 2>/dev/null) 
```

Enter fullscreen mode Exit fullscreen mode

`2>/dev/null`基本上将 STDERR(见[标准流](https://en.wikipedia.org/wiki/Standard_streams))重定向为 null。seth 的所有杂项记录都被忽略，我们只剩下合同地址。

**如果部署失败**，就没有`.catch`或者难以负担的承诺要处理。`$ERC20_ADDR`变量将为空。如果我们在一个脚本中自动执行，明智的做法是确保它在失败时尽早退出。看看这个:

```
#!/bin/bash
set -ex

# Connect to the local node
export ETH_RPC_URL=http://127.0.0.1:8545

# Disable below if not on Truffle
export ETH_GAS=4712388

# Use the unlocked RPC accounts
export ETH_RPC_ACCOUNTS=yes

# Get the 1st account and use it
export ETH_FROM=$(seth accounts | head -n1 | awk '{ print $1 }')

ERC20_ADDR=$(seth send --create $(cat build/contracts/ERC20.json | jq -r .bytecode) --status 2>/dev/null)

echo -n $ERC20_ADDR > ERC20.deployment 
```

Enter fullscreen mode Exit fullscreen mode

嘣！我们刚刚将地址保存到一个文件中，以便以后加载！

### 调用方法和传递参数

这可能是赛斯最好的部分。使用这个工具调用契约上的方法再简单不过了，因为**你不必加载 JSON ABI"** 来与契约交互。

例如，假设我们正在测试这个令牌契约，我们想为自己铸造一些令牌。这有多简单:

```
# Get the contracts address
export ERC20_ADDR=$(cat ERC20.deployment)

seth send $ERC20_ADDR "mint(uint256)" 1 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果一个方法有返回值，你只需要向 seth 展示如何解码它。我们传递的是方法签名，但形式比完整方法简单得多- `method(<types>)(<return-types>)`。

```
# Get the balance of our address
seth send $ERC20_ADDR "balanceOf(address)(uint)" $ETH_FROM 
```

Enter fullscreen mode Exit fullscreen mode

### 不同账户交易

我们经常在契约逻辑中使用`msg.sender`。到了测试的时候，我们可能希望使用不同的帐户进行交互。Seth 使用`ETH_FROM`变量使这变得非常简单。

我们已经开始使用全局`ETH_FROM`了，但是使用每个调用也很简单。

```
export BUYER=$(seth accounts | sed -n 2p | awk '{ print $1 }')

seth send --from $BUYER --value 402343423423423 $EXCHANGE "ethToTokenSwapInput(uint256,uint256)" 3 1000000000000 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子从我分配的名为$BUYER 的特定帐户调用 Uniswap exchange 上的`ethToTokenSwapInput`。它还利用`--value`在事务中发送一些以太网。

### 转换单位(数字，eth)

这让我想起了我最后一次重要的关于赛斯转换单位的教学。在以太坊中，我们默认处理`uint256`，以太和其他代币*通常是*设计为显示 18 位小数。Seth 使这些不同格式之间的转换变得很容易。

```
seth --to-dec 0000000000000cb803 eth
# 833539

seth --from-wei <decimal-amount>
# 0.123

seth --to-wei <decimal-value>
# 123

seth --to-ascii 67646179206d617465
# ... ;) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

作为以太坊生态系统的开发者，我认为`seth`是一项了不起的投资。考虑到全新工具平台固有的复杂性，`seth`是一种在区块链软件上检查、试验和迭代的实用方法。

☕️:如果你喜欢这个指南，可以考虑在 Twitter 上关注我。

😎我喜欢学习和教学，并经常这样做。以下是一些你可能会感兴趣的链接:

*   我的[令人敬畏的坚实模式](https://github.com/liamzebedee/awesome-solidity-patterns)回购
*   [如何将库链接到 sol-编译器生成的 Solidity 契约中](https://dev.to/liamzebedee/how-to-link-libraries-into-solidity-contracts-generated-by-sol-compiler-34b3)
*   我的博客，[常规魅力](https://liamz.co/blog/)，有一些更深入的内容
*   [@liamzebedee](https://twitter.com/liamzebedee) 查看科技领域有趣的链接/讨论