# 个人安全、全球定位系统和机器学习:你在逃避危险吗？

> 原文：<https://dev.to/josiahbryan/personal-safety-gps-and-machine-learning-are-you-running-from-danger-43bl>

想象一下，你每分钟都会从你最好的朋友那里收到一条短信，短信里只有他们当前的速度。然后你必须回复他们你认为他们在做什么——他们是在走路、跑步、开车还是坐着不动？

在我的应用程序中，我从“嘿，我有一些 GPS 点正在传输到我的服务器”到“实时机器学习分类触发推送通知”，我花了不到一天的时间编写代码。我是这样做的。

# 走好安全

这正是我在自己制作的应用中要解决的问题。我从用户那里得到一个 GPS 速度读数，我想知道他们是否在走路、跑步等。这款应用名为“WalkSafe ”,并在 Play Store 和 App Store 中免费提供。(尚未发表——仍在审核阶段，因此我有时间在等待审核者批准的时候写博客！)

在我姐姐和她年幼的儿子搬进公寓后，我决定创建 WalkSafe，因为她觉得那里很不安全。对她来说，这是一个很好的举动，但作为一个单身母亲，晚上独自外出，她感到不安全。我家就住在附近，但有时如果有事发生，她可能无法拿出手机打电话。输入“步行安全”的想法

[![](img/d5566a704844d2e7d14c5c96999fd476.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EwEheYol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fyyqml69qsy0wb4st0og.png)

使用 WalkSafe，你可以在遇到危险时设置计时器。如果计时器在你停止计时前停止计时，系统会向你的紧急联系人发送一条短信和语音电话，告知你的位置和你输入的任何信息。当然，如果你安全到达你要去的地方，你只需停止计时器，一切都很好！但是如果你因为任何原因不能停止它，我们的云服务器会监控你的计时器，如果它响了，SOS 会立即发送。这意味着，即使你的手机被摧毁，离线，或没有服务，求救信号仍然会被发送。

当您在 WalkSafe 中设置计时器时，它会开始记录您的 GPS 位置，并在计时器持续期间将其传输到服务器。GPS 不会在之前或之后被储存，只有当你处于危险中的时候。然而，我觉得仅仅在危险中记录 GPS 是不够的。我想可能有某种方法可以使用 GPS 来尝试判断使用该应用程序的人在没有他们交互的情况下是否处于危险中(或安全)。

# 绘制线条

这就是我们如何从一开始就得到这个例子，我们如何解释一串没有其他背景的速度？我们如何决定它是否代表跑步/开车/走路/等等？

当然，坐着不动很容易。小于 0.5 m/s？可能是坐着不动。开车呢？超过 15 米/秒？是啊，可能在开车。但是之后就变得模糊了。你在哪里为步行划定界限？跑步？你如何仅仅根据速度来区分跑步和开车？

要回答这些问题，你可以做两件事中的一件(或者三件，但是我会回到这个话题上。)你可以:

1.  写一堆`if` / `then`语句，考虑到他们最近几次的速度读数，他们在那个速度下已经多久了，他们昨天这个时候做了什么等等。
2.  训练一个简单的神经网络，在你坐着喝茶的时候为你分类数据。

显然，由于这个帖子被标记为#machinelearning，我决定使用神经网络。

在我的例子中，我使用了优秀的 [brain.js](https://brain.js.org) 库，因为我是用 javascript 编写服务器的。我也在 bast 中使用过`brain.js`,我发现它非常容易使用，并且在项目中可以很快上手和实现。

总而言之，从“嘿，我有一些 GPS 点正在传输到我的服务器”到“实时机器学习分类触发推送通知”，我花了不到一天的时间编写代码。我基本上是这样做的。

在客户端，我使用`Cordova`项目来制作 Android/iOS 应用程序，在`React`中编写我的 UI，并利用优秀的`@mauron85/cordova-plugin-background-geolocation`插件在后台将 GPS 传输到我的服务器。

# 服务器端魔术

服务器是奇迹发生的地方。

每个人都知道，要训练一个神经网络，你需要有标签的数据。你输入数据，运行训练，得到一组训练好的权重，然后在以后使用它。很简单，对吧？好吧，请允许我向你介绍一下我是如何做到的，以及一路上有趣的部分。

## 采集数据

我开始只是从我自己的应用程序使用记录大量的 GPS 点。在两天的时间里，我记录了我在走路、跑步、开车、走到我的车前开车、跑到我的车前开车、开车、停车、然后走路以及许多其他场景下的 GPS 点。我在笔记本上记下了我做每一个动作的时间。

## 标注数据

后来，我将时间戳和速度转储到一个 CSV 文件中，并应用了一个简单的速度标签。(例如:`0m/s` = `STILL`、`<2m/s` = `WALKING`、`<10m/s` = `RUNNING`、`>10m/s` = `DRIVING`)然后我打开每个 CSV 文件，将时间戳与我的笔记本进行比较，确保原始标签是正确的。改了很多开>跑或者开>开的时候开的慢之类的东西。当我完成时，我在 CSV 文件中有一组大约 5000 个速度测量值，所有这些测量值都用一组简单的`STILL`、`WALKING`、`RUNNING`或`DRIVING`的活动标签手工标记。

## 格式化数据:N 元图

现在我有了一组顺序的速度测量值，看起来像:

```
[ 0, 1.2, 0.78, 1.9, 2.1, 1.8, 2.8, 3.3, 3.6, 4.1, 3.3, 4.9, 5.7 ] 
```

Enter fullscreen mode Exit fullscreen mode

你能从中看出什么有趣的东西吗？(假设它们是米每秒)如果你仔细观察，你会注意到一个上升，在那里它们开始趋向于 2 米每秒以上一段时间——就在那里我开始运行。在那之前，我在走路。

为了捕捉数据中的顺序性，我决定用一组代表以前 X 值的点来训练我的网络，最终值是我们正在分类的“当前”点。这在概念上类似于语言建模中的 n-gramss，在 n-gram 中，它们将一个文本序列分解成一组有限的项目集。《出埃及记》给定“abcd”和大小为 2 的 n-gram，我们可以生成“ab”、“bc”和“cd”。

因此，我编写了一个简单的`makeNgramsTrainingNN`例程，它获取原始的速度流并将它们打包成速度读数集。这很像在我的数据集上运行一个固定大小的滑动窗口，一次一项，并将窗口内的每组数据记录为一个新的“n 元语法”。所以我的`makeNgramsTrainingNN`例程将接受一个速度对象数组(`speed`和`label`)，并返回一个新的数组，如下所示:

```
[
  { input: { speed0: 0, speed1: 1.2, speed3: 0.78 }, output: { WALKING: 1 } }, 
  { input: { speed0: 1.2, speed1: 0.78, speed3: 1.9 }, output { WALKING: 1 } },
  { input: { speed0: 0.78, speed1: 1.9, speed3: 2.1 }, output { WALKING: 1 } }
] 
```

Enter fullscreen mode Exit fullscreen mode

标签始终是我手动编辑的数据集的标签，用于 n 元语法中的最后一个速度值。

## 训练神经网络

然后，我必须决定如何训练我的网络，以及使用什么类型的网络。经过反复试验，我发现`brain.CrossValidate`在降低错误率方面非常有效。

一旦我把所有的 n-grams 放在一个漂亮的大`ngrams`数组中，我要做的就是训练网络:

```
const trainingOptions = {
    iterations: 35000,
    learningRate: 0.2,
    hiddenLayers: [ngramSize+2],
    log: details => console.log(details),
};

// Use CrossValidation because it seems to give better accuracy
const crossValidate = new brain.CrossValidate(brain.NeuralNetwork, trainingOptions);

// Found it doesn't do us any good to specify kfolds manually
const stats = crossValidate.train(ngrams, trainingOptions);

// Convert the CV to a nerual network for output (below)
const net = crossValidate.toNeuralNetwork(); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我训练好了网络，我就把它保存到一个 json 文件中，这样我就可以实时地用它来分类 GPS:

```
// Stringify the nerual network 
const json = JSON.stringify(net.toJSON());
const outFile = 'gps-speed-classifier.net.json';
fs.writeFileSync(outFile, json); 
```

Enter fullscreen mode Exit fullscreen mode

发现`35000`的迭代次数是一个好数字，并且发现添加一个大小为我的`ngramSize` + 2 的隐藏层是一个好数字，这纯粹是一种尝试和错误。所有的只是测试和再测试，看看什么错误率出来。

值得一提的是，我使用的是 6 的`ngramSize`-这意味着我的神经网络一次看到 6 个速度读数来做出分类决定。我已经配置了 GPS 插件客户端，尝试每 1000 毫秒向我发送 GPS 读数，因此 ngram 大小为 6 意味着大约 6 秒的数据用于训练和分类。需要注意的是，在生产中使用训练好的网络时，我必须使用相同的 ngram 大小。

## 向自己证明这是有效的

为了测试错误率，首先我将所有的训练图按类进行了分类，并测试了每个类的召回率。当我每堂课的回忆率都超过 95%时，我认为培训是成功的。

我在每个训练过的网络上做的最后一个测试是获取一个单一的数据“会话”,就好像它是实时流一样运行，并将预测的标签与手动标记的数据进行比较。一旦我达到了 90%以上的准确率，我就很高兴了。

从“手工标注数据集”到最终拥有一个我满意的训练有素的网络，大约花了 6 个小时左右的测试和反复试验。

# 将训练好的网络集成到 App 中

相比之下，将其集成到应用程序中是一个非常快速的过程——如果是这样的话，可能需要两个小时。我创建了一个名为`GpsActivityClassifier`的“简单”类，它从`gps-speed-classifier.net.json`加载训练好的网络权重。这个类负责用户的*运动状态*的分类和更新

应用程序的 API 进入`GpsActivityClassifier`看似简单:

```
const result = await GpsActivityClassifier.updateUserMotionState(gpsLogEntry); 
```

Enter fullscreen mode Exit fullscreen mode

`gpsLogEntry`是当前 GPS 条目的内部数据库记录。实际上，分类器从日志条目中需要的唯一东西是我们正在分类的`speed`、当前的`timer`和`user`。

在内部，它相当简单，但是代码看起来有点复杂，所以我将在这里分解它。在内部，`updateUserMotionState`看起来是这样的:

1.  获取给定`gpsLogEntry`的时间戳，并加载当前`timer`的先前`ngramSize`条目
2.  将 X 个条目的列表(看起来像`[{speed:0.1,...},{speed:0.5,...}, {speed:1.23,...}, ...]`)转换成一个看起来像`{speed0:0.1, speed1:0.5, speed2:1.23, ...}`的`ngram`对象。转换代码如下所示:

```
const ngram = {};
Array.from(speedValues)
    .slice(0, TRAINED_NGRAM_SIZE)
    .forEach((value, idx) => ngram[`speed${idx}`] = value); 
```

Enter fullscreen mode Exit fullscreen mode

做好`ngram`后，使用预加载的`brain.js` `NeuralNetwork`对象(已经从磁盘加载了权重)对`ngram`做`run`如下:

```
const rawClassification = this.net.run(ngram);
const classification = maxClass(rawClassification); 
```

Enter fullscreen mode Exit fullscreen mode

实用程序`maxClass(...)`只是获取网络最后一层的原始输出，并返回具有最高概率的预测类标签。

# 压力改变

在这一点上，我们有了一个`gpsLogEntry`的预测标签(`predictedState`)。但是这里是我们做“第三件事”的地方，我们在博客前面已经暗示过了。

我们没有将`predictedState`直接应用于用户，并将其称为用户的当前`motionState`，而是将一点硬逻辑应用于状态。

如果分类从一个点快速变化到另一个点，我们不希望用户的`motionState`剧烈振荡，所以我内置了一个简单的“压力”机制，借此预测必须至少在`CLASSIFICATIONS_NEEDED_TO_CHANGE`计数内保持稳定。通过反复试验，我发现`5`是个好数字。

这意味着对于给定的`gpsLogEntry`，分类器可能会返回`RUNNING`。只有在它连续五次返回`RUNNING`GPS 读数后，我们才更新用户的`motionState`。如果分类器在达到 5 次之前进入不同的分类，计数器将重新开始。(例如，如果在第 3 点分类器返回`DRIVING`，我们重置计数器并等待 5 个点，直到我们实际将用户的`motionState`设置为`DRIVING`。)

# 变化是好的(或坏的)

一旦改变`motionStates`的计数器实际达到，我们用新的`motionState`更新数据库中的用户记录，并向我们的`GpsActivityClassifier.updateUserMotionState`方法的调用者返回一个看起来像`{ changed: "DRIVING", confidence: 0.98, previousState: "RUNNING" }`的对象。我认为这是一个“*事件*，因为如果用户的`motionState`确实发生了变化，我们只能得到一个返回值{ changed: *truthy* }。其他时候，如果分类保持不变或者“即将改变”，对象看起来会像`{changed: false, ...}`。

那么，当一个`changed`事件发生时，我们该怎么办呢？

在 WalkSafe 的例子中，我们对这个事件所做的是，当变化发生时，我们运行一点“业务逻辑”。我们采用`stateFrom` ( `previousState`)和`stateTo` ( `changed`)，构建一个简单的转换图(`txMap`)，定义有效/有用的转换，然后做出相应的反应。

为了好玩和开心，下面是我们的`txMap`在 WalkSafe 中的样子:

```
const { WALK, RUN, DRIVE, STILL } = GpsActivityClassifier.CLASSIFIER_STATES,
    OK_30   = 'OK_30',
    OK_60   = 'OK_60',
    SAFE_60 = 'SAFE_60',
    SAFE_5  = 'SAFE_5',
    NOOP    = 'NOOP',
    txMap   = {
        [ WALK + RUN  ]: OK_30,
        [STILL + RUN  ]: OK_30,
        [DRIVE + RUN  ]: OK_60,
        [STILL + DRIVE]: SAFE_60,
        [ WALK + DRIVE]: SAFE_60,
        [  RUN + DRIVE]: SAFE_60,
        [  RUN + WALK ]: SAFE_5,
        [  RUN + STILL]: NOOP,
        [ WALK + STILL]: NOOP,
        [DRIVE + STILL]: NOOP,
        [STILL + WALK ]: NOOP,
        [DRIVE + WALK ]: NOOP,
    }; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们只要在用户的`motionState`随 from 和 to 状态变化时查询`txMap`，并做出相应的反应。为了便于说明，下面是它的样子:

```
const txTest = stateFrom + stateTo,
    txAction = txMap[txTest];

if(!txAction) {
    // Should never encounter, but if we find a tx we don't have defined,
    // we throw which should be caught by Sentry and dashboarded/emailed
    throw new Error(`Undefined transition from ${stateFrom} to state ${stateTo})`);
}

switch(txAction) {
    case OK_30:
    case OK_60: {
        const time = txAction === OK_60 ? 60 : 30;
        return await this._txAreYouInDanger({ time, stateTo, stateFrom, ...props });
    }
    case SAFE_60:
    case SAFE_5: {
        const time = txAction === SAFE_60 ? 60 : 60 * 5;
        return await this._txAreYouSafe({ time, stateTo, stateFrom, ...props });
    }
    default: 
        // NOOP;
        break;
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会详细介绍`_txAreYouSafe`或`_txAreYouInDanger`功能，但它们基本上是在运行的计时器中添加(如果安全)或设置(如果危险)剩余时间，然后通过 Firebase 向用户的设备发送推送通知。

不过，为了给它打上蝴蝶结，这里是发送推送通知的样子，如本文顶部的截图所示:

```
// Triggered possible danger scenario, so reduce time remaining
// to only `time` seconds...
await timer.setSecondsRemaining(time);

// Alert the user to this change ...
user.alert({
    // Channel is Android-specific and MUST EXIST OR 
    // NO NOTIFICATION DELIVERED on Androids. 
    // See list in client/src/utils/NativePushPlugin of valid channels.
    channel: "sos",
    title: "Are you running??",
    body:  `
        If you're not okay, KEEP RUNNING! We'll send an SOS in 
        less than a minute unless you stop the timer or add more time. 
        Don't stop unless it's safe to do so!
    `,

    // onClick is base64-encoded and sent via Firebase 
    // as the action URL for this push notification
    onClick: {
        // This event key is "special":
        // When the user clicks on the notification,
        // our app will emit this event on the ServerStore object...
        // Any other properties in this onClick handler are passed as
        // a data object to the event. This is emitted in PushNotifyService.
        // Obviously, the event does nothing unless some other part of the
        // app is listening for it.
        event:  'gps.areYouInDanger',
        // Extra args for the event:
        timerId: timer.id,
        stateTo, 
        stateFrom,
    },
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 安全行走，但如果需要，可以奔跑，我们有你

所有这些因素的结合为使用 WalkSafe 的人提供了额外的保护。如果他们设置了危险计时器，但在计时器中间开始运行，服务器将识别这种状态变化，减少计时器上剩余的时间，因此如果他们实际上正在逃离危险，它将立即发送 SOS。

这就是我们如何将个人安全、GPS 和机器学习结合在一起，以提高使用简单个人安全 SOS 计时器的人的现实世界安全性！

# Beta 测试员招聘

如果你想测试这个应用程序，给我发消息。或者，如果你有兴趣与我合作的应用程序，我会敞开心扉！如果你有兴趣雇用我做咨询工作，也给我写信吧！你可以打 josiahbryan@gmail.com 的电话找到我。干杯和饼干！