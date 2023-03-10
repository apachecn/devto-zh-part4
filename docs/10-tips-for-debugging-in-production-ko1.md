# 生产调试的 10 个技巧

> 原文：<https://dev.to/molly/10-tips-for-debugging-in-production-ko1>

作为开发人员，调试是最难的事情之一。它推动你在一个全新的层面上理解你的创作。当你试图在本地或测试中追踪一个 bug 时，你友好的 Ruby 调试工具如 pry 和 byebug 是非常好的。但是当 bug 在生产时会发生什么呢？如果 bug 只在数千个并发作业运行时出现，那该怎么办？你如何处理这些场景的调试？

最近，我和我的团队遇到了一个 Sidekiq 工作人员的问题，我们花了 4 天多才解决。当我们调试这个问题时，我们不懈地试图在我们的本地环境中复制它，但是没有成功。这是一个关于 bug 搜索如何展开的故事，以及我们用来帮助我们在生产中安全调试问题的策略。

# 这个🐛

我们使用 [Sidekiq Enterprise](https://sidekiq.org/products/enterprise.html) 来处理我们所有的后台工作。我们使用的 Sidekiq Enterprise 的主要特性之一是[滚动重启](https://github.com/mperham/sidekiq/wiki/Ent-Rolling-Restarts)特性。这允许我们在 Sidekiq 上有长时间运行的作业，我们不必担心当一个部署出去并重新加载工人时会被杀死。reload 命令发出一个 USR2 信号，告诉工人一旦完成他们的工作就关机。

我们使用这项功能已经快一年了，直到上周才出现任何问题。上周，我们部署了一个新任务，它本身存在一些问题，并引发了许多错误。当作业中断并引发所有这些错误时，当我们尝试重新加载 Sidekiq 线程时，我们开始看到它们被卡住。在终端中，当我们在部署后查看 Sidekiq 流程时，我们看到了以下情况:

[![](img/0aef37d5104f1f572ad9a1d8d123cce5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S8j48l3f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ogql8ub2gy9tfzflizcx.png)

这些线程没有在任何事情上工作，但是它们仍然没有死亡。考虑到我们一天要部署很多次，我们无法承受这些停滞不前的线程。

我们立即尝试在本地重现问题。对于开发，我们在笔记本电脑上使用一个流浪的虚拟机。他们配备了整个应用程序、工作人员等。我们甚至可以将我们的流浪者踢入生产模式，这将像生产服务器一样运行。(稍后我会解释*几乎*😉)

我们首先试图在开发模式下重现这个问题，但是没有成功。然后我决定让我的流浪者进入生产模式，看看这是否可行。还是没有骰子。此时，我们唯一的选择是在生产中调试。我已经能感觉到网上对我评判的目光。

[![](img/3f09b582f5cd7bf6496fc22db45ebd50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XQrPaWU2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ib9xz5nlro45azszj7e.gif)

在产品中调试是理想的吗，不。但是有时这是你必须做的来解决问题。关键是要以安全的方式进行，确保您的用户不会受到影响。在整个调试过程中，我将与您分享我在生产中调试时使用的工具和策略，这些工具和策略已被证明非常有价值。

## 1。限制调试的范围

首先，当您在生产环境中调试时，您希望限制测试和调试的范围。在我们的案例中，我们有多个生产环境，其中一些环境的使用率比其他环境低。我们做的第一件事是确认我们可以在一个较小的生产环境中重现该问题。

如果您只有一个生产环境，请考虑将您的测试范围扩大到一台服务器甚至一个用户。您对将要测试的内容或位置了解得越多，对用户造成干扰的风险就越小。

有了测试环境，我们开始深入研究这个问题。鉴于我们之前从未见过这种行为，我们最初认为新工作有问题。在对阻塞的进程运行一些进程跟踪并搜索结果后，我们发现一个潜在的问题是新的作业代码不是线程安全的。Sidekiq 使用多线程来处理作业，如果它们不是线程安全的，就会导致线程阻塞。我们仔细检查了一遍，确保新任务是线程安全的。然后，我们检查以确保新工作发出的所有外部请求都没有被阻塞。我们确保新的作业代码是原始的，但是当它出错时，我们仍然会看到阻塞的线程。

由于工作中没有确凿的证据，我们决定看看这是否真的是新的工作准则。这引出了我的第二条建议。

## 2。通过消除可能的原因来定位问题

当我们使用 Sidekiq 处理作业时，会发生很多事情。为了准确地找出问题所在，我们必须开始消除可能有问题的代码。在开发环境中或者当您正在进行测试时，这可以像注释掉代码一样简单。在制作过程中，你必须变得更有创造力，这也引出了我的下一条建议:

## 3。不要害怕编写和提交用于调试目的的代码

为了查看新的作业代码是否有错，我们创建了一个名为 StandardErrorWorker 的虚拟 Sidekiq 作业，它除了引发一个错误之外什么也不做。

```
class StandardErrorWorker
  include Sidekiq::Worker

  def perform
    raise StandardError
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果这个简单的作业导致了相同的 Sidekiq 阻塞线程，那么我们可以排除新作业代码出现问题的可能性。

我知道你们中的一些人现在很震惊。犯临时代码，亵渎！但有时你必须做你必须做的事。我认为出于调试目的编写临时代码是完全可以接受的。关键是你要确保做完后清理干净。你可以通过添加一个待办事项，使用类似于 [todo_or_die gem](https://github.com/searls/todo_or_die) 的工具，或者简单地写一个便条或标签来提醒自己在完成后删除它，来确保你做到这一点。把它想象成露营⛺️

[![](img/add488a029d3251ef669b13f71b1fc60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yaZPYte3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jofvxng3m2gck5nstxr3.gif)

不留痕迹！

随着新工作的部署，我们在较少使用的生产环境和 low 上启动了我们的测试场景，结果发现线程被卡住了！这对我们来说是一个巨大的转折点，因为它向我们表明，问题不在于我们的新工作，而在于其他方面。

在我继续之前，我想指出一些事情。对这份新工作的调查花了几天时间。我觉得人们做这些报道，一眨眼的功夫就从一个场景跳到下一个场景，这让我觉得想明白这件事是小菜一碟。新闻快讯，现实生活中不是这样的！在现实生活中，你必须用头撞墙至少一天才能得出这种结论。

[![](img/9251ca226b6997b08f8e8a5c65f1bf8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H1kcw8KR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9p9fki0doymkx6i3h9ft.gif)

我们花了几天时间尝试其他东西，然后才想到这可能不是新的工作代码。当这个问题开始的时候，新的任务就被删除了，这怎么可能和它的代码无关呢？！甚至在我们设置虚拟工人的时候，我们都非常确定它不会有问题。最后，我们对结果感到非常惊讶，这给了我第四条建议:

## 4。测试每一个场景，不管多么不可能

即使有你高度怀疑的场景，也要测试它！最坏的情况是它会证实你已经知道的事情。最好的情况，它会告诉你你完全错了，让你走上新的道路。对我们来说，是后者。

好了，现在继续。一旦我们确定这不是新工作，我就开始仔细研究 Sidekiq。我最初的想法是，我们失败得太快了，可能是时间问题。也许在关闭路径中某些东西被跳过或错过了。是时候正式发布调试日志了。

在我们的生产代码中，我将它添加到我们的 sidekiq.rb 初始化文件中，以便在我们测试的小型生产环境中打开 sidekiq 调试日志记录。

```
if ENV['env_name'] == "small_prod"
  Sidekiq::Logging.logger.level = Logger::DEBUG
end 
```

Enter fullscreen mode Exit fullscreen mode

在打开日志记录的情况下，我启动了我们的错误作业，发出了一些重新加载命令，并让一些工人卡住了。我从日志中找到的东西是不确定的。有时 Sidekiq 关闭流被正确触发，但是线程仍然被卡住。其他时候，关闭流程看起来甚至从未启动过。这是一个死胡同，并没有真正的帮助。

此时，我向 Sidekiq 的创始人 Mike Perham 寻求帮助。这让我想到了另一条建议，无论您是在生产环境中测试还是在本地测试，这条建议都适用。

## 5。不要害怕向你的同事或社区寻求帮助。

我承认，我可能总是等得太久才寻求帮助，因为我想确保我没有错过任何明显的事情。他在他的 wiki 中向我展示了这个有用的[停滞流程故障排除](https://github.com/mperham/sidekiq/wiki/Problems-and-Troubleshooting#frozen-processes)部分。不打算撒谎，我觉得自己有点像个白痴，因为在我之前的谷歌恶作剧中完全错过了这一点。但是我没有细说，而是钻研了维基的建议。

我在我们的服务器上连接了 [GDB](https://www.gnu.org/software/gdb/) ,然后去镇上看看卡住的进程。不幸的是，这是一个死胡同。转储线程数据没有带来任何有价值的东西，我仍然看不到阻塞的线程在做什么。当我陷入这种困境时，我总是会回到日志，试图找到我错过的东西，这是我的下一条建议。

## 6。读，重读，再读日志。

[![](img/4b4ca830144db7242df97f9636e347e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRn0HU6P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30baa1psblwbiym63eir.gif)

当您在本地或测试环境中调试时，您可以很容易地设置断点来检查调试过程中发生了什么。因为你不能在你的产品代码中设置断点，你必须依靠日志来显示发生了什么。我回去梳理了日志，一些东西立即打动了我，我们的蜜獾错误。

[Honeybadger](https://www.honeybadger.io/) 是我们用来报告应用程序错误的服务。我们的应用程序中包含了 [Honeybadger gem](https://github.com/honeybadger-io/honeybadger-ruby) 来帮助我们将这些应用程序错误发送给 Honeybadger 服务。我在日志中注意到，我们不仅成功地向 Honeybadger 发送了错误，而且还有许多错误失败了。那些失败的公司正在被节流。我开始怀疑 Honeybadger 代码中的节流错误是否会导致流程停滞。

是时候应用更多的问题定位并尝试消除另一个可能的问题组件了。测试蜜獾是不是问题很容易。我关闭了 StandardErrorWorker 的 Honeybadger 报告，并再次复制了失败场景，只是这一次，一切都完美无缺！当数千个任务失败并重试时，我能够重新加载 Sidekiq 5 次，没有一个线程接近停滞。我现在知道问题出在蜜獾身上，但我不知道为什么或者蜜獾身上有什么问题。

我立刻跳进蜜獾[源码](https://github.com/honeybadger-io/honeybadger-ruby/blob/master/lib/honeybadger/worker.rb#L200)查看。在那里，我可以看到运行中的节流，并决定尝试关闭它，看看是否可以修复我们的问题。通常，当我处于这种情况时，我不会试图完全理解发生了什么，而是首先想出一个测试来验证我看到的代码是否是问题所在。再一次，问题本地化赢得胜利！

如果我确认我看到的代码是问题所在，那么我会更深入地理解为什么它会以这种方式工作。为了关闭节流，我让 hacky 和 monkey 为我找到的蜜獾方法打了补丁，这让我想到了我的下一条建议:

## 7。猴子打补丁是你调试时的朋友

当你怀疑某个宝石可能有问题时，不要害怕用它来测试你的理论。如果事实证明这确实是个问题，那么你可以花些时间对宝石本身进行公关。

首先，我修补了 [handle_response](https://github.com/honeybadger-io/honeybadger-ruby/blob/bd96aa9500987dc462128d76820e96184504cb77/lib/honeybadger/worker.rb#L200) 方法，使其不增加任何限制。

```
module Honeybadger
  class Worker
    def handle_response(msg, response)
      debug { sprintf('worker response code=%s message=%s', response.code, response.message.to_s.dump) }

      case response.code
      when 429, 503
        warn { sprintf('Error report failed: project is sending too many errors. id=%s code=%s throttle=1.25 interval=%s', msg.id, response.code, throttle_interval) }
        # add_throttle(1.25)
        warn { sprintf('monkey patch code working') }
      when 402
        ...
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我修补了 [work](https://github.com/honeybadger-io/honeybadger-ruby/blob/bd96aa9500987dc462128d76820e96184504cb77/lib/honeybadger/worker.rb#L165) 方法，也删除了那里的节流。

```
module Honeybadger
  class Worker
    def work(msg)
      send_now(msg)
      # sleep(throttle_interval)
    rescue StandardError => e
      error {
        msg = "Error in worker thread class=%s message=%s\n\t%s"
        sprintf(msg, e.class, e.message.dump, Array(e.backtrace).join("\n\t"))
      }
      sleep(1)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

有了补丁后，我进行了与以前相同的测试。我真的真的以为会是这样....但事实并非如此😩。工人们仍然被困住了。我又回到了 Honeybadger 源代码，继续到处戳。这时我注意到了[队列对象](https://github.com/honeybadger-io/honeybadger-ruby/blob/bd96aa9500987dc462128d76820e96184504cb77/lib/honeybadger/worker.rb#L18)。这激起了我的兴趣，因为队列意味着将项目排队等待处理，如果你有很多项目，可能会减慢速度。

我首先注意到队列有一个 max_size，并想知道它是什么。原来是一个[配置设置](https://github.com/honeybadger-io/honeybadger-ruby/blob/7eea24a47d44aed663e315be970e501b7cf092fc/lib/honeybadger/config/defaults.rb#L86)，默认为 1000。

[![](img/29349c3e3fcf46a9f9eb0859840abe2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S5sD_-ks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k1yqrkxy8zpb3e4sv5xr.gif)

哇，这似乎是一个问题，当搭配节流。查看配置设置时，我注意到了 [`send_data_at_exit`](https://github.com/honeybadger-io/honeybadger-ruby/blob/7eea24a47d44aed663e315be970e501b7cf092fc/lib/honeybadger/config/defaults.rb#L81) 设置。我以前从未听说过，所以我读了那是什么。它默认为真，这是它的描述:

> 在允许程序退出之前，完成发送排队异常。

[![](img/1899a4522559801fb2d06449604ee24c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tsg41-pL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tlyfc77k0nz34ffx8k0z.gif)

答对了。Honeybadger 不允许一个程序退出，直到它发送完所有的入队数据。是时候测试另一个理论了！我将生产环境中的配置变量`send_data_at_exit`设置为 false。这意味着当我告诉线程退出时，它们会忽略在 Honeybadger 队列中建立的任何东西，并立即退出。我启动了失败场景，重新加载了工人，零个线程被卡住了！

我的反应是👇
[![](img/ff5f17b74248d8440e2b0cacb9b04cb5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Fw3ELuM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsk0ndmv8jxekv4i5dpa.gif)

我的团队知道我已经为此奋斗了好几天，当我大喊大叫的时候，他们都看着我，他们说的第一句话是，“妈的，你解决了它，不是吗？”这是世界上最好的感觉！

回顾整个过程，当你调试像这样的复杂问题时，我有更多的想法和建议与你分享。

## 8。不要忘记你的宝石和插件可能会导致问题。

99.99%有效的宝石很容易被遗忘。我们从未与蜜獾有过纠纷，所以我们从未想到这可能是个问题。也很容易认为是你写了错误的代码，因为写 gem 的人是完美的，对吗？不对！宝石和插件也有 bug！它甚至可能不是一个 bug，可能是 gem 没有针对您的用例进行优化。

### 修罗

我知道你们中的一些人非常想知道我们是如何解决这个问题的。我们有两个选择来解决这个卡住线程的问题。我们可以减小队列的大小，或者将`send_data_at_exit`设置为 false。我们决定采用第二种选择。我们的 Sidekiq 故障也记录在一个日志 Elasticsearch 集群中，所以即使故障没有一直传到 Honeybadger，我们仍然会有它们的记录。此外，当我们经历大量失败时，它们通常都是同一类型的，所以在可见性方面，Honeybadger 中缺少一些不太可能成为问题。

随着我们的代码得到修复，应用程序中的一切都运行顺利，终于到了可以高枕无忧的时候了，对吗？

[![](img/017c536f72746d496f85e8c3d2e4c468.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7lgoreUZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dhz7zgkp45r46sc7p9zx.gif)

代码修好了不代表我们就完了。在一次产品调试冒险之后，您还应该做两件事情。首先是:

## 9。使错误在本地可再现

在生产中测试的主要原因是因为您不能在本地重新创建它。为了避免在生产中再次调试，请寻找可以改进或更新您的本地环境的方法，以便更好地模拟生产，并使您遇到的各种错误可以重现。

前面我提到过，我们在本地使用流浪虚拟机，我们可以将它们投入生产模式。生产模式来自于另一次我们在生产中被迫调试的问题。从那以后，它在帮助我们解决生产问题方面发挥了不可估量的作用。在这种情况下，它不起作用的原因是没有启用 Honeybadger。我们已经改变了这一点，所以我们在生产模式下的流浪者更好地模拟了我们的实际生产环境。

最后但同样重要的是:

## 10。分享你的故事！

如果你经历了漫长的调试历程，并从中获得了一些有趣的见解，那么请分享吧！你可能最终会拯救那些遇到同样问题的人很多时间。

至少，如果你的问题与一个 gem 或插件有关，联系维护者或打开一个 Github 问题让他们知道。他们肯定想知道是否有 bug，他们可能还想知道是否有方法可以改进他们的文档。在我的调试之旅之后，我和 Mike(Sidekiq 的创始人)和 Josh(Honeybadger 的联合创始人)聊了聊他们可以改进文档和 wiki 的方法，以帮助其他人避免我的失误。他们非常友好，采纳了我的一些建议。

下一次当您遇到生产 bug 时，使用这些建议来帮助您安全成功地调试它！现在开始调试吧！

[![](img/cfdc248cf5c642fe0f84db56230e1a66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IezpU-cG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r26nyd8wddo4d8l4jkqa.jpg)