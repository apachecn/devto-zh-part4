# 通过足球类比了解自我

> 原文：<https://dev.to/danvyle/understanding-self-via-football-analogy-2f0e>

### 你是否迷失了自我？

[![mirror](img/3c1e7fcde58171d030b19fca0e44600d.png)](https://i.giphy.com/media/60rUZAj7OHOiCsbPJY/giphy.gif)

如果你正在寻找自我，请退出这个博客。如果你是编程新手，不太清楚如何在面向对象的 Ruby 中使用 self，我也许能帮上忙。

在编程中，你用得越多，自我就成了你的第二天性，但是当你第一次学习它的时候，可能会有些困惑。

我经常问，“我用它喵吗？”“喵怎么样？”对如何以及何时使用它知之甚少。当我第一次开始时，我只是插入并祈祷它有效。

[![cat](img/13a2c291283bdd2c6135b448c386bd34.png)](https://i.giphy.com/media/581Zvttgt7Witjgc0Y/giphy.gif)

我是这样理解它的:

### ..体育用语。

#### 我从团队运动中吸取了很多经验:

*   努力工作和纪律
*   失败了又重新站起来
*   协力
*   ...以及现在如何使用 self。

假设你有一个足球游戏的对象类。

```
class FootballGame

  attr_accessor :hometeam, :awayteam, :date

  @@all = []

  def initialize(hometeam, awayteam, date)
    @hometeam = hometeam
    @awayteam = awayteam
    @date = date
    @away_team_score = 0
    @home_team_score = 0
    @@all << self
  end 
```

### 类

这个例子中的类是你的足球游戏类。很笼统。你总是可以指望任何足球比赛有 4 节，两个队，掷硬币，啦啦队，一群可怕的裁判支持你的主队，等等，等等。

### 实例

这个例子中的实例是曾经玩过或将要玩过的每一个游戏。很具体。就像一个游戏里，马肖恩·林奇碾死了几个红衣主教来记录地震活动。如果你愿意，可以称之为野兽地震。

[![beastmode](img/5a07bcbfa2893f390d72e104a50f980e.png)](https://i.giphy.com/media/UetVmMI3HkMr6/giphy.gif)

我们将使用这个游戏来创建一个足球游戏的新实例。可以用上面代码中列出的三个属性进行初始化:hometeam、awayteam、date。

```
az_sea_01032015 = FootballGame.new("Cardinals", "Seahawks", "01/03/2015") 
```

太棒了。我们已经创建了一个新实例，命名为 az_sea_01032015。亚利桑那是主队，西雅图是客场队，他们在 2015 年 01 月 03 日比赛。您的命名约定应该是您认可的，并且任何其他程序员都应该能够理解的。

来看看我们拟定的一些方法。

```
 def home_td
    @home_team_score += 6
    "We scored, yay!"
  end

  def away_td
    @away_team_score += 6
    "They scored, boo."
  end

  def home_fg
    @home_team_score += 1
    "And the field goal was good."
  end

  def away_fg
    @away_team_score += 1
    "And the field goal was good."
  end 
```

这些被认为是实例方法。我们怎么知道？

*   单数@符号通常表示实例变量。
*   它们是特定于实例的(海鹰队对红雀队的比赛)。
*   您创建的信息将保留在该实例中。

要调用这些方法，我们只需使用创建实例时设置的变量。

```
az_sea_01032015.home_td
  => "We scored, yay!"
az_sea_01032015.home_fg
  => "And the field goal was good."
 #=> @home_team_score = 7

#Although the Cardinals may have scored first...SPOILER alert, Seahawks won! 
```

我们再来看一些方法。

```
def self.all
  @@all
end

def self.intro
  "Game on! - Carrie Underwood"
end

def self.coin_toss
  ["heads", "tails"].sample
end 
```

这些被认为是类方法。我们怎么知道？

*   方法名称中的句点。
*   两个@@符号。
*   这些方法可以在类上调用。它适用于可以创建的每个实例。
*   为什么要用自我。这里吗？我不会说，“丹-维(我的名字是丹-维，如果你之前没听清楚的话)要去公园。”我就告诉你，"我要去公园。"我已经在你面前了，就像你已经在你的足球课上一样。

调用你的类方法:

```
 FootballGame.all
  => [#<FootballGame:0x00007fccdf954d50
    @away_team_score=6,
    @awayteam="Seahawks",
    @date="10/10/18",
    @home_team_score=12,
    @hometeam="Packers">,
   #<FootballGame:0x00007fcce0148200
    @away_team_score=0,
    @awayteam="Seahawks",
    @date="01/03/2015",
    @home_team_score=7,
    @hometeam="Cardinals">] 
```

这里我们称之为 FootballGame.all(在我们的代码中，我们将其称为 self.all 方法)。它列出了我们在 FootballGame 类中创建的每个游戏(实例)。很好。

```
FootballGame.intro
=> "Game on! - Carrie Underwood"
FootballGame.coin_toss
=> "tails" 
```

这些方法是为了加深你对类的理解。这些是通用方法，可以在每个游戏中调用，而不仅仅是一个。

希望这有所帮助！我将以某个 QB 结束他所有采访的方式结束:“加油，老鹰队。”祝你在编码之旅中一切顺利！

[![schmidt](img/2ff5ec0d359bf18f365da9a02e62b3cb.png)](https://i.giphy.com/media/piXrzDejeWIM/giphy.gif)