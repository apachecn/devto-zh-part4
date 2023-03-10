# 预提交挂钩和替代项中的测试

> 原文：<https://dev.to/zeitschlag/tests-in-pre-commit-hooks-and-alternatives-3b9o>

几个月前，我给自己买了一台花哨的 MacBook Pro。你知道，这是一种有着别致的触摸条、漂亮的视网膜屏幕和默认坏掉的键盘的别致设备。为了让自己感觉更好，因为你永远不知道未来几年会发生什么，我还买了一个 AppleCare Plus 套餐。

一开始，我对这个设置非常满意——速度惊人:一切都准备好了！哇！令人兴奋！然后空格键开始触发两次。几个星期后，左 CMD 键和其他一些键的行为相同。所以上周我把电脑带到了苹果经销商那里，让他们把电脑送给苹果，让他们更换整个键盘。

同时我用了我的旧电脑。这是一款 2014 款 Macbook Air，配有 1.4Ghz CPU 和 4GB 内存。我一直把它作为备用电脑，我真的很失望，几个月后我不得不依赖它。与 2018 年的 Macbook Pro 相比，它相当慢，但嘿，至少它有一个工作键盘。我依赖这个 MBA 项目超过 4 年半，它从未让我失望。

七月，马克斯和我录制了一集德语播客。和我们的[嘉宾张秀坤一起，我们讨论了知识转移](https://codestammtis.ch/2019/07/15/cst032-wissenstransfer-mit-dom/)，在那一集里，张秀坤提到，[可以用 git-hooks](https://medium.com/@pearsontsp/automate-tests-in-ios-with-git-hooks-8d97b66e0f36) 在 iOS 中自动测试。这个星期，我真的尝试了，因为我真的很喜欢这个想法。我将测试设置为在预提交钩子中运行。在每次提交之前，项目被编译，我的所有单元测试都被运行，如果且仅当它们成功，提交被创建。

不幸的是，这并没有给我的后备 MBA 带来很多乐趣，因为编译 Swift 代码和运行测试大约需要一分钟。这意味着，每次创建一个新的提交需要一分钟。这意味着浪费了大量的等待时间。

我有什么选择？我可以把这个从预提交钩子移到预推送钩子，就像前面提到的博客文章中建议的那样。

或者我可以使用类似于 [Windmill](https://www.windmill.io) 或本地 [Jenkins](https://jenkins.io) 的工具，但这并不能解决问题，因为这些工具仍然可以在我的老式但却是金色的 MBA 上本地运行。使用 [Gitlab CI](https://about.gitlab.com/product/continuous-integration/) 也不行，因为它的运行程序仍然在我的机器上本地运行。我没有另一台备用计算机可以用来作为构建服务器。

另一个选择是 bitrise 的免费爱好计划。或者我可以开源我的项目，并从 [Travis CI](https://travis-ci.org) 中获利。或者我可以付钱给他们。或者我可以从 [MacStadium](https://www.macstadium.com) 租一台 Mac，让它运行 Gitlab Runner，或者我自己的 Jenkins，或者 Xcode 机器人。

如此多的可能性可供选择！在我的 MBP 回来之前，我可能会选择最实惠的方式。每次`CMD+S`后按`CMD+U`即可。让我们看看，因为:

> 迫切是一个很难养成的习惯，尤其是对我们这些 iOS 开发者来说。
> 
> — [来源](https://medium.com/@pearsontsp/automate-tests-in-ios-with-git-hooks-8d97b66e0f36)