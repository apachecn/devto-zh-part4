# C++中简单的控制台石头、布、剪刀

> 原文：<https://dev.to/maytd/simple-console-rock-paper-scissors-in-c-g35>

这个程序是一个简单的石头、布、剪刀游戏，玩家必须从三个选项中选择一个来击败电脑。计算机将通过创建一个数字来表示该选项，从而生成一个随机选项。1 将是“石头”，2 将是“布”，3 将是“剪刀”。预期结果应该是这样的:
[![Alt Text](img/c44c538cde4b3b82d16b4868eaa7eb62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V35eYzcp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/76r3dwonmrvr8dq6oxm0.png)

在我们开始之前，让我们将石头、布、剪刀的值设置为如下数字: [![Alt Text](img/fec730fbd81f5867bacbcf5ec02235e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IIOuJA0Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g5e61u1oe73dkfstkazw.png)

现在，让我们做一个选项板。这将允许用户在石头、布或剪刀之间进行选择。打印出可供玩家挑选的选项如下:
[![Alt Text](img/818c2553502297549fdc766f25ff1fff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--itV7NzPI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ogun4jkf5g3t2ae50vp.png)

接下来，我们必须让用户选择并显示他们的选择。我们通过比较用户值和我们设置的常量值来显示他们的数量。
[![Alt Text](img/337cc3868c7f300e74afb47223499d5f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--w4KxUlaQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/awkes1zyt195eo5goh2g.png)

一旦我们有了用户的选择，我们需要得到计算机的选择，看看谁会赢。为此，我们将随机生成一个 1-3 之间的数字，并显示计算机的选择。
[![Alt Text](img/6b97e0b9ac228a961d40f5b8a90bdbb7.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---vf8fsbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6z7fvudupg4zioht4fdd.png)

对于这两种选择，我们已经准备好进行比较，看看谁是赢家！做一个决定所有结果的函数。石头会打败剪刀，剪刀会打败布，布会打败石头。如果计算机或用户赢了，我们应该显示结果。如果两个选择都匹配，不要忘记打领带。
[![Alt Text](img/8b479d0c56f3c8195aa0ebbfc84602e5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HowuU8kr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9oxm4p17a7rql1hx1stm.png)

就是这样！这些是用 C++制作石头、剪子、布游戏的基本步骤。所有用于程序的代码看起来都是这样的:
[![Alt Text](img/a26221d736560849ddfba39cb3564e46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cPKW11TF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ozbxxmhcqe371cf52sfo.png)