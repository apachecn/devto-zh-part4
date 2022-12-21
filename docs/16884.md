# 在 Libra 网络上写下你的第一笔交易

> 原文：<https://dev.to/daudr/write-your-first-transaction-on-the-libra-network-5f5e>

[**这篇文章之前已经发表在我的主博客**](https://blog.daudr.me/libra-simple-smart-contract) 上

前几天我[发表了一篇关于 Libra 网络的文章](https://blog.daudr.me/facebook-libra)，今天我们将看看如何在它上面建立一个交易。

[![Libra logo](img/0c567f3e957ebbda3fc3281d1283a871.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--viRkyY2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mw029rhi99hu6fh1dpcs.png)

> 天秤座标志

## 本地安装测试网

今天我们不会使用 [**Move**](https://blog.daudr.me/facebook-libra) ，这种新的编程语言是为了在 Libra 网络中构建模块而创建的，而只是终端(目前唯一支持的操作系统是 **Linux** 和 **macOS** )。安装`testnet`需要一些先决条件:

*   `git`安装在您的机器上；
*   如果您使用的是 macOS，则已安装

## 克隆 Libra 核心 GitHub 库

要在本地安装`testnet`,只需在终端上运行这个命令

`git clone https://github.com/libra/libra.git`

## 设置

要安装 Libra 核心，进入`libra`目录并运行安装脚本来安装依赖项，在您的`terminal`
上运行这些命令

```
cd libra
./scripts/dev_setup.sh 
```

该脚本只做这些事情:

*   installes`rustup`-`rustup`是 Rust 编程语言的安装程序，`Libra Core`是在其中实现的。
*   安装所需版本的`rust-toolchain`。
*   安装`CMake` -来管理构建过程。
*   安装`protoc` -一个用于协议缓冲区的编译器。
*   安装`Go` -用于构建协议缓冲区。

## 故障排除

如果安装失败，请尝试运行以下命令:

*   更新`Rust`:

从你的 libra 目录运行`rustup`更新。

*   从 libra 目录重新运行安装脚本:

`./scripts/dev_setup.sh`

## 构建 Libra CLI 客户端并连接到 Testnet

要连接到 Libra `testnet`上运行的验证器节点，运行命令:

`./scripts/cli/start_cli_testnet.sh`

这个命令利用`cargo` ( `Rust`的包管理器)构建并运行客户端，并将客户端连接到`testnet`上的验证器节点。

一旦客户机连接到`testnet`上的一个节点，您将看到下面的输出。要随时退出客户端，请使用`quit`命令。

```
usage: <command> <args> 
Use the following commands: 
account | a   Account operations
query | q   Query operations
transfer | transferb | t | tb
  <sender_account_address>|<sender_account_ref_id>
  <receiver_account_address>|<receiver_account_ref_id>
  <number_of_coins> [gas_unit_price (default=0)]
  [max_gas_amount (default 10000)] Suffix 'b' is for blocking.  
  Transfer coins from account to another.
help | h   Prints this help
quit | q!   Exit this client
Please, input commands:
libra% 
```

## 故障排除

如果您遇到构建失败，尝试从 libra 目录中删除`cargo lock`文件:

`rm Cargo.lock`

如果您的客户端没有连接到测试网络:

*   检查您的互联网连接。
*   确保您使用的是最新版本的客户端。获取最新的 Libra 内核并重新运行客户端:

`./scripts/cli/start_cli_testnet.sh`

## 创建新账户

一旦您的客户端连接到`testnet`，您就可以运行 CLI 命令来创建新帐户。我将指导您为两个用户创建帐户。

### 第一步:检查客户端是否正在运行

libra%命令行提示符表示您的 Libra CLI 客户端正在运行。要查看`account`命令的帮助信息，请输入如下所示的“account:

```
libra% account
usage: account <arg>
Use the following args for this command:
create | c   Create an account. Returns reference ID to use in other operations
list | la   Print all accounts that were created or loaded
recover | r <file path>   Recover Libra wallet from the file path
write | w <file name>   Save Libra wallet mnemonic recovery seed to disk
mint | mintb | m | mb <receiver account> <number of coins>   Mint coins to the account. Suffix 'b' is for blocking 
```

### 第二步:创建第一个账户

注意，使用 CLI 创建帐户不会更新`blockchain`，它只是创建一个本地密钥对。
要创建第一个帐户，请输入以下命令:

`libra% account create`

该命令将生成如下输出:

```
>> Creating/retrieving next account from wallet
Created/retrieved account #0 address 1h3n34fafae4147b2a105a0be2f91238adcfaaadf93fc0868e7a0253c4a8 
```

#0 是帐户的索引，十六进制字符串是帐户的地址。索引只是一种引用帐户的方式。帐户索引是本地 CLI 索引，可在其他 CLI 命令中使用，以便用户方便地引用他们创建的帐户。该指数对区块链毫无意义。只有当通过铸造将钱添加到账户中，或者通过从另一个用户转账将钱转移到账户中时，才会在区块链上创建账户。请注意，您也可以在 CLI 命令中使用十六进制地址。账户索引只是账户地址的方便包装。

### 第三步:创建第二个账户

要创建第二个帐户，请重复帐户创建命令:

`libra% account create`

### 【可选】第四步:列出账户

要列出您创建的帐户，请输入以下命令:

`libra% account list`

这是你将会看到的:

```
User account index: 0, address: 3ed8e5fafae4147b2a105a0be2f81972883441cfaaadf93fc0868e7a0253c4a8, sequence number: 0
User account index: 1, address: 8337aac709a41fe6be03cad8878a0d4209740b1608f8a81566c9a7d4b95a2ec7, sequence number: 0 
```

一个帐户的序列号表示从该帐户发出的交易的数量。每当从该帐户发送的交易被执行并存储在区块链中时，该值就会增加。

## 向账户中添加天秤座币

在`testnet`上铸造硬币和向账户添加硬币是通过`faucet`完成的。`Faucet`是和`testnet`一起运行的服务。这项服务只是为了方便`testnet`铸造硬币而存在，不会为`mainnet`而存在。它创造了没有现实价值的天秤座。假设你已经创建了两个账户，索引分别为 0 和 1，你可以按照下面的步骤将 Libra 添加到这两个账户中。

### 第一步:将天秤座币加入账户

要创建 Libra 并添加到第一个帐户，请输入以下命令:

`libra% account mint 0 125`

*   0 是帐户的索引。
*   125 是 Libra 要加入账户的金额。

成功的`account mint`命令也将在`blockchain`上创建账户。

成功时的样本输出:

```
>> Minting coins
Mint request submitted 
```

注意，当请求被提交时，意味着它已经被成功地添加到了`mempool`(在`testnet`上的一个验证器节点的)中。并不一定意味着会圆满完成。稍后，我们将查询帐户余额以确认铸造是否成功。

要创建 Libra 并添加到第二个帐户，请输入以下命令:

`libra% account mint 1 50`

*   1 是帐户的索引。
*   50 是 Libra 要加到账户上的金额。
*   一个成功的帐户铸币命令也将在区块链上创建帐户。在区块链上创建账户的另一种方法是从任何其他账户转账。

### 第三步:查看账户余额

要检查第一个帐户的余额，请输入以下命令:

`libra% query balance 0`

成功时的输出示例:

`Balance is 100`

## 提交交易

```
libra% query sequence 0
>> Getting current sequence number Sequence number is: 0
libra% query sequence 1
>> Getting current sequence number Sequence number is: 0 
```

在`query sequence 0`中，0 是第一个账户的索引。两个帐户的序列号都为 0 表示到目前为止，这两个帐户中的任何一个都没有执行任何交易。

## 转账

要提交将 10 Libra 从第一个帐户转移到第二个帐户的交易，请输入以下命令:

`libra% transfer 0 1 10`

*   0 是第一个帐户的索引。
*   1 是第二个帐户的索引。
*   10 是天秤座从第一个账户转到另一个账户的数字。

这将生成:

```
>> Transferring
Transaction submitted to validator
To query for transaction status, run: query txn_acc_seq 0 0 <fetch_events=true|false> 
```

您可以使用命令`query txn_acc_seq 0 0 true`(transaction by account and sequence number)来检索关于您刚刚提交的交易的信息。第一个参数是发送方帐户的本地索引，第二个参数是帐户的序列号。

您刚刚在`testnet`将您的事务提交给一个验证器节点，它包含在验证器的`mempool`中。这并不一定意味着您的交易已经执行。理论上，如果系统运行缓慢或过载，需要一些时间才能看到结果，您可能需要通过查询帐户来检查多次。要查询索引为 0 的帐户，可以使用命令`query account_state 0`。

**阻塞转移命令**:您可以使用`transferb`命令(如下图所示)代替`transfer`命令。只有在交易提交给区块链后，`transferb`才会提交交易并返回客户提示。

`libra% transferb 0 1 10`

## 查询转账后的序号

```
libra% query sequence 0
>> Getting current sequence number Sequence number is: 1
libra% query sequence 1
>> Getting current sequence number Sequence number is: 0 
```

第一个帐户的序列号 1(索引 0)表示到目前为止已经从该帐户发送了一笔交易。第二个帐户的序列号 0(索引 1)表示到目前为止还没有从该帐户发送交易。每次从账户发送交易时，序列号增加 1。

## 转账后查看账户余额

要检查两个帐户的最终余额，请像在此步骤中一样再次查询每个帐户的余额。如果您的交易(`transfer`)成功执行，您应该在第一个帐户中看到 90 Libra，在第二个帐户中看到 60 Libra。

```
libra% query balance 0
Balance is: 90
libra% query balance 1
Balance is: 60 
```

## 恭喜恭喜！

您已经在天秤座`testnet`上成功执行交易，并转移了 10 枚**天秤座币**！