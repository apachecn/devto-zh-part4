# 正确的马电池订书钉

> 原文：<https://dev.to/oinak/correct-horse-battery-staple-33ap>

有很多地方对您可以输入的密码字符有令人讨厌的限制，甚至更糟，对密码长度有限制。

如果你不能从这篇文章中得到任何东西，至少请注意:*密码字段应该允许任何字符，并且足够长，以便你的用户不会注意到长度限制*。

有人使用密码生成器并粘贴 250 个字符长的字符串。让他们的生活变得轻松，因为他们正在尽自己的一份力量。

但是，唉，如果你不使用密码管理器，但仍然希望有安全的密码，容易记住，但很难猜测，那么互联网上的最佳提示是这样的:

[xkcd:密码强度](https://www.xkcd.com/936/)

[![Password Strength](img/673a3ca303fac1fd12e529edac8a3eee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ZrLCC6g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/password_strength.png)

如果你了解我，我通常会用 ruby 来解决问题，所以，我们开始吧:

## 假设:

*   您在 GNU/Linux 上，或者您可以提供一个包含目标语言单词列表的文本文件
*   您已经安装了 ruby

## 代码:

```
#!/usr/bin/env ruby

# https://www.xkcd.com/936/
module PasswordGenerator
  extend self

  # replace this with your language:
  WORDS = '/usr/share/dict/spanish'

  def run
    puts((1..4).map { safe_words.sample }.join(" "))
  end

  private

  SAFE_SET = /\A[A-Za-z]+\Z/.freeze

  # avoid non-ascii to save trouble
  def safe_words
    @safe ||= words.select{ |w| SAFE_SET.match(w) }
  end

  def words
    @words ||= File.read(WORDS).split("\n")
  end
end

# this executes the run method if the file is run as a command
PasswordGenerator.run if __FILE__ == $PROGRAM_NAME 
```

Enter fullscreen mode Exit fullscreen mode

预期的用途是运行几次，直到某个单词组合让你想起一个你觉得能够记住的图像或故事。

* * *

> 封面图片由来自[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2168233)的[扬·亚历山大](https://pixabay.com/users/JanBaby-3005373/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2168233)拍摄