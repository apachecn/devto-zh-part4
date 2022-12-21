# 使用 Javascript 的 setInterval()和 setTimeout()函数

> 原文：<https://dev.to/vadims4/using-javascript-s-setinterval-and-settimeout-function-1k82>

# 效用

在 web 开发的世界中，我们经常需要适应和克服我们面临的挑战。很多时候，我们不得不忍受试图解决超出我们理解范围的事情。当事情不以我们希望的方式结束时，有时会变得非常令人沮丧。作为新的开发人员，搞清楚普通 JS 的来龙去脉后，我们会发现自己正努力想出一个解决方案。这是我最近在一个项目中的经历。该项目需要一个提醒功能。可以将当前日期/时间与指定日期/时间进行比较的东西。重要的是我采取了正确的方法来解决这个问题。思考它将如何运作让我开始研究。这是我遇到 setInterval()函数和 setTimeout()函数的地方。这些功能有许多应用。您可以通过 setInterval()和 setTimeout()函数不断地改变您的背景并利用所有这些很酷的特性。我将分享这些函数的使用方法，以及我如何使用 setInterval()函数来满足我的项目中对提醒功能的需求！

[![Reminder](img/8d3d7aa9d8bd4c22a672517ddcc153e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCXas5c_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcSbv_jF8YzhozeHgtgstbu3otb-xKKcFiAV9HGQBEBKAprlPtlV)

# setInterval()和 setTimeout()

这些功能非常相似，但又非常不同。对于 Javascript 中的定时事件，两者都非常有用。

*   setInterval()是一种基于时间间隔的代码执行方法，它具有在时间间隔到达时重复运行指定脚本的能力。另外，默认情况下，setInterval()循环。所以不需要用 setInterval()循环。这个函数一直运行到调用函数 clearInterval()为止，在这个过程中，它中断了循环。

```
function myFunction() {
  let interval = setInterval(mySecondFunction, 3000, param1, param2)
}

function mySecondFunction(param1, param2) {
  return "calls this function every 3 seconds"
} 
```

*   setTimeout()是一个基于时间的代码执行方法，当到达时间间隔时，它将只执行一次脚本。除非在循环内部调用，否则这个函数不会重复。如果放在循环中，setTimeout()将一直运行，直到调用 clearTimeout()为止。

```
function myFunction() {
  let interval = setTimeout(mySecondFunction, 3000, param1, param2)
}

function mySecondFunction(param1, param2) {
  return "Hello!"
} 
```

# 在我的项目中实现 setInterval()

在使用我的提醒功能时，我必须想办法创建一个可以比较当前时间和指定时间的功能。困难的是，

1.  我需要创建一个函数来检查时间，看它是否与选择的时间相匹配。
2.  我需要一种方法来一直调用这个函数，这样它就可以一直检查时间是否匹配。

我解决问题的方法是设置一个 setInterval()函数，它每秒调用一次函数。该功能将检查当前日期/时间，并查看它是否与某人为提醒设置的日期/时间相匹配。虽然这不是很有效，但是如果只使用普通的 JS，这可能是唯一的方法。接下来，如果设置的日期/时间与实际的日期/时间匹配，我使用 clearInterval()停止时间间隔，并能够为我的提醒功能呈现一个弹出窗口和一个音频方面。

```
Myinterval = setInterval(setReminder, 1000, ev, newCard);

if(actualTime == alarmTime && currentActualDate == selectedDate) {
    audio.play();
    endReminder(newCard);
  } 
```

# 结论

尽管 Javascript 有时非常令人沮丧，但有许多方法非常有用。如果希望经常调用某个函数，或者在一定时间后需要调用某个函数，那么我在这篇博客中介绍的函数就特别有用。不管怎样，setInterval()和 setTimeout()这样的函数使得 Javascript 成为一种特别的语言。对 Javascript 的工作原理了解得越多，用它编码就越有趣！