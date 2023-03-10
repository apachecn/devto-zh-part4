# 带有 Azure 函数和 NPS 的美女随机图片

> 原文：<https://dev.to/raymondcamden/random-pictures-of-beauty-with-azure-functions-and-the-nps-3224>

我对随机性很着迷，尤其是当它导致有趣的事情或新的发现时。例如，Twitter 账户 [OnePerfectShot](https://twitter.com/OnePerfectShot) 分享了电影史上所有电影的剧照。

> ![unknown tweet media content](img/1942742e697e0a9262a5b6e72a9498bd.png)![One Perfect Shot profile image](img/e83039cb1db4c7c18ff67046dc355f28.png)一个完美的镜头@ oneperfectshot![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)蝙蝠侠(1989)
> 
> 罗杰·普拉特摄影
> 蒂姆·伯顿导演
> 以下是这部电影的诡异之处:[buff.ly/2uqQ3kF](https://t.co/xA3EHdSC5r)下午 16:48-01 2019 年 7 月

再比如[龙囤](https://twitter.com/dragonhoards)。这是一个随机发布微型故事的机器人。它并不总是奏效，但当它奏效时，真他妈的令人着迷。

> ![Dragon Hoards profile image](img/f664dee30aa83370910b974a5a487d37.png)龙囤@龙囤![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)一条黄色的龙住在一片海洋的岸边。她清点了自己的收藏，包括大量的月亮、装满独角兽毛的盒子和太多的馅饼。她是偏执狂。06:40am-01 2019 年 7 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1145582902463389698)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1145582902463389698)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1145582902463389698)

最后，我自己创作的一个作品是 [RandomComicBook](https://twitter.com/randomcomicbook) 。三年前，我[在博客](https://www.raymondcamden.com/2016/02/22/building-a-twitter-bot-to-display-random-comic-book-covers)上写了这个，现在它仍然是我创作的最喜欢的东西之一。这里有一个例子:

> ![unknown tweet media content](img/d962d337b9c2bb2cf94bf1c4c5f19d1a.png)![Random Comic Book profile image](img/2a7dc462173b5d90f371469b32841adf.png)随机漫画@ randomcomicbook![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)《神奇蜘蛛侠(1963) #12》出版 1964 年 5 月
> [【marvel.com/comics/issue/6…](https://t.co/18BRdt7UkS)12:00PM-01 2019 年 7 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1145663388867059713)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1145663388867059713)

有鉴于此，上周我有了一个灵感。我发现国家公园系统有一个 API。实际上是一整套空气污染指数，但有一个特别突出，那就是公园空气污染指数。根据文件，这提供了:

> 公园基本数据包括每个国家公园的位置、联系方式、营业时间和门票/通行证信息。每个公园至少有五张照片。

特别是它提供了每个公园的照片。我认为创建一个机器人来随机选择一个公园和一张照片并通过 Twitter 分享会很棒。我的逻辑非常简单:

API 允许您按州进行搜索，所以第一步只是选择一个随机的州。

API 让你得到一个有限制的公园列表，但在我的测试中，即使是加利福尼亚也只有不到 50 个，所以我认为只要求 100 个就够了。

3)从列表中，过滤出那些带有图像。是的，医生说他们都这样，但我想这不会有什么坏处。

4)从列表中挑选一个，并选择一个随机图像。

5)发微博。

仅此而已。我决定选择 Azure Functions，因为我仍然觉得我需要挖掘更多。 [Netlify](https://netlify.com) 也没有服务器，但由于我没有计划让*为这个项目提供任何* HTML 内容，我认为这不是一个很好的选择。我使用了 [Visual Studio 代码扩展](https://code.visualstudio.com/tutorials/functions-extension/getting-started),它工作得非常好，除了一些(主要)是我的错的小问题。最后，我还将我的部署绑定到一个 GitHub repo。这是[记录良好的](https://docs.microsoft.com/en-us/azure/azure-functions/functions-continuous-deployment)，除了一个小错误，我花了一段时间来修复。我说的“花了我一段时间”是指乞求伯克·霍兰德的帮助，直到他屈服并找到我的问题。(这是文档中缺少的一个关键细节。我为它提交了一个问题，所以它现在可能被修复。如果没有，你可以看到我的评论在底部的更正。)

这个函数大约有 120 行。我会先分享它，而不是一点一点来。

```
const fetch = require('node-fetch');
const Twit = require('twit')

const T = new Twit({
    consumer_key: process.env.TWITTER_CONSUMER_KEY,
    consumer_secret: process.env.TWITTER_CONSUMER_SECRET,
    access_token: process.env.TWITTER_ACCESS_TOKEN,
    access_token_secret: process.env.TWITTER_ACCESS_TOKEN_SECRET,
    timeout_ms: 60 * 1000, // optional HTTP request timeout to apply to all requests.
    strictSSL: true, // optional - requires SSL certificates to be valid.
});

const states = {
    "AL": "Alabama",
    "AK": "Alaska",
// deleted a bunch
    "WI": "Wisconsin",
    "WY": "Wyoming"
}

const NPS_KEY = process.env.NPS_KEY;

// Credit: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random
function getRandomIntInclusive(min, max) {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min + 1)) + min; 
}

module.exports = async function (context, req) {

    let stateAbbrs = Object.keys(states);
    let chosenState = stateAbbrs[getRandomIntInclusive(0, stateAbbrs.length)];

    context.log(`I chose ${chosenState}`);

    let httpResult = await fetch(`https://developer.nps.gov/api/v1/parks?stateCode=${chosenState}&limit=100&fields=images&api_key=${NPS_KEY}`);
    let results = await httpResult.json();
    data = results.data.filter(r => r.images.length);

    // now select a random park
    let selectedPark = data[getRandomIntInclusive(0, data.length - 1)];
    // now select a random image
    let selectedImage = selectedPark.images[getRandomIntInclusive(0, selectedPark.images.length - 1)];

    context.log(JSON.stringify(selectedPark));

    let imageRequest = await fetch(selectedImage.url, { headers: { 'user-agent': 'Chrome' } });
    let image = await imageRequest.buffer();
    image = image.toString('base64');

    let mediaResult = await new Promise((resolve, reject) => {

        T.post('media/upload', { media_data: image }, (err, data, response) => {
            if(err) {
                console.log(err);
                reject(err);
            }
            resolve(data.media_id_string);
        });

    });

    context.log('mediaResult', mediaResult);
    let text = `Picture from ${selectedPark.fullName}. More information at ${selectedPark.url}`;

    let params = { status: text, media_ids: [mediaResult] }

    let tweetResult = await new Promise((resolve, reject) => {
        T.post('statuses/update', params, function (err, data, response) {
            resolve(data);
        })
    });

    context.res = {
        body: `Tweet ID: ${tweetResult.id}`
    };
    context.done();

}; 
```

Enter fullscreen mode Exit fullscreen mode

好吧，发生什么事了？第一个真正的部分涉及到我加载 [Twit](https://www.npmjs.com/package/twit) 库，这是我使用 Twitter API 的首选方式。跳过 JSON 中的 50 个状态(我应该把它转换成一长行)，主函数从选择状态开始。我的数据包括缩写和全名，因为我想我可能最终会两者都用，但这并不成功。我可以以后再优化。

然后，我用我的状态点击 API，将结果过滤成那些带有图像的，并随机选择一个。有了那个公园，我就可以选择我的图像。向 Twitter 发布媒体需要你先上传，这有点麻烦，但是 async/await 让一切变得更好。Twit 使用回调风格的 API，所以我把它包装在一个承诺中，这样我就可以等待它了。一旦我上传了我的媒体，我就可以在推特上引用它。我使用的文字有点简单，我认为还可以改进。我愿意接受任何建议。仅此而已。你可以在[https://twitter.com/npsbot](https://twitter.com/npsbot)看到结果。这里有几个例子。

> ![unknown tweet media content](img/4901adc9d354611347256b4751304ee7.png)![npsbot profile image](img/1baf870dfc262cc79f35b0ec708cc510.png)NPS bot@ NPS bot![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)图片来自奥古斯塔运河国家遗产区。更多信息请点击[【nps.gov/auca/index.htm】](https://t.co/8jO0mzgyBT)2019 年 7 月 01 日下午 18:00[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1145753982054219776)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1145753982054219776)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1145753982054219776)

> ![unknown tweet media content](img/cacd5497e9088528635bb1b761258740.png)![npsbot profile image](img/1baf870dfc262cc79f35b0ec708cc510.png)NPS bot@ NPS bot![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)图片来自堰洲农场国家历史遗迹更多信息请点击[【nps.gov/wefa/index.htm】](https://t.co/GI1GilXcO3)00:00AM-01 2019 年 7 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1145482203503054849)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1145482203503054849)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1145482203503054849)

如果你愿意，你可以在这里查看完整的代码库:[https://github.com/cfjedimaster/npsbot](https://github.com/cfjedimaster/npsbot)。只要 Azure 不向我收费，我就会让这个机器人一直运行。:)

编辑-一个我忘记提到的小提示。我要做的最后一件事是将函数从 HTTP 驱动的函数切换到预定的函数。我以为这很简单。文件`function.json`决定了您的函数可以使用的连接类型(以及其他设置)，但是调度任务的文档总是显示 function.json 文件的子集，而不是全部。我第一次尝试添加正确的值时破坏了函数，因为我发现不能有 HTTP 驱动的函数*和使用时间表的函数*。这看起来很傻，但是您可以通过添加第二个函数来调用第一个函数来解决这个问题。因为我只需要 HTTP 支持来进行测试，所以我删除了它。我的`function.json`在 GitHub repo 里面，所以如果你好奇的话可以看看。

*标题图片由[查尔斯·布莱克](https://unsplash.com/@divewithchuck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash* 上拍摄