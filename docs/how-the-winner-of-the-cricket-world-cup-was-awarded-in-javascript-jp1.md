# 板球世界杯的冠军是如何在 Javascript 中被授予的

> 原文：<https://dev.to/afewminutesofcode/how-the-winner-of-the-cricket-world-cup-was-awarded-in-javascript-jp1>

在这篇文章中，我们将使用最近一次体育赛事中的实际例子来学习如何在 JavaScript 中通过多个字段对数组进行排序。

在最近的板球世界杯中，获胜者是根据以下标准决定的。⠀

*   最多跑垒(如果超级 over)⠀打成平手
*   大多数跑在超级结束(如果一个平局去边界 count)⠀
*   大多数界限。⠀

排序时，我们可以使用语法`array.sort((a,b) => b - a`从最高到最低排序。⠀

所以在下面的代码中，我们检查⠀

*   如果游程和超游程相等-按边界 count⠀排序
*   否则，如果运行相等，则按超级 over⠀中的大多数运行进行排序
*   否则按大多数 runs⠀排序

然后，因为这将对数组进行排序，我们将获取数组[0]中的第一个结果并返回 team 属性，所以当我们注销世界杯决赛的比分时，我们可以看到英格兰队被宣布为比赛的获胜者。⠀

```
const cricketWorldCupFinal = [{
  team: 'England',
  runs: 241,
  wkts: 10,
  supOvrRuns: 15,
  boundaryCount: 26
},
{
  team: 'New Zealand',
  runs: 241,
  wkts: 8,
  supOvrRuns: 15,
  boundaryCount: 17
}]

const calcMatchWinner = scores => {
  return scores.sort((a, b) => {
    if (a.runs === b.runs && a.supOvrRuns === b.supOvrRuns) {
      return b.boundaryCount - a.boundaryCount
    } else if (a.runs === b.runs) {
      return (b.supOvrRuns - a.supOvrRuns)
    } else {
      return (b.runs - a.runs)
    }
  })[0].team
}

console.log(calcMatchWinner(cricketWorldCupFinal))
// output => England 
```

我目前正在进行一个板球 javascript 课程，在那里我演示了如何用 JavaScript 创建一个板球积分表，所以请在下面的一个频道或 Dev 上关注我，以了解它何时发布。⠀

[Instagram](https://instagram.com/afewminutesofcode)
[脸书](https://facebook.com/afewminutesofcode)
[afewminutesofcode.com](https://afewminutesofcode.com/?utm_source=devto&utm_medium=website&utm_campaign=blogpost)
[推特](http://twitter.com/afewminsofcode)
[Pinterest](https://www.pinterest.com.au/afewminutesofcode)

[https://www.instagram.com/p/B0vilpNnOXV/embed/captioned](https://www.instagram.com/p/B0vilpNnOXV/embed/captioned)