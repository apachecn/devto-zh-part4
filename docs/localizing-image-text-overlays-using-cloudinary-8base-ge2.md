# 使用 Cloudinary + 8base 本地化图像文本覆盖

> 原文：<https://dev.to/sebastian_scholl/localizing-image-text-overlays-using-cloudinary-8base-ge2>

想象你是一名社交媒体实习生。请容忍我！你是一名社交媒体实习生，你的经理没有让你参加战略会议，而是给了你一份与形象相关的任务清单。它包括:

1.  裁剪*这个*。
2.  使变亮。
3.  叠加“新到！”在 Twitter 标题图片上。
4.  调整脸书共享卡的大小。
5.  给我拿杯咖啡。
6.  翻译“新来的！”俄语、德语和斯瓦希里语。

...你明白了

现在，你是一个聪明的饼干。你绝不会想把一天浪费在电脑上，不得不手动操作所有这些图像。所以，你开始寻找更好的方法。

## ⏸的故事结束了，暂且不提

这种情况不是虚构的。是真的！每天，无论是在工作中还是在个人项目中，都会有数百万张图片被创建、编辑、更新、托管、删除、丢失等等。有助于管理混乱或简化流程的服务会非常有帮助。

前几天有个朋友跟我分享了 [Cloudinary 的 URL API](https://cloudinary.com/documentation/solution_overview?query=URL%20API&c_query=Account%20and%20API%20setup%20%E2%80%BA%20URLs%20and%20endpoints#urls_and_endpoints) 。很快，我就把它视为公司和个人所遭受的众多与形象相关的低效问题的答案。很快，我将提到一个*为什么*和一个*如何*。

### 为什么

从一个单一的图像，几十个调整版本可能需要得到创建(褪色，文字覆盖，黑白等。).每个版本都需要时间来创建、更新和组织。

### 如何

Cloudinary 的 URL API 接受一个动态参数，该参数在检索时将转换应用于图像。把它想象成点播 Photoshop！

就个人而言，这让我很兴奋。值得注意的是，*文本覆盖了*转换。在花了一点时间玩它之后，我想看看它是否可以扩展到包含图像文本的本地化(翻译)。

这次练习产生了一个有效的演示。你可以在这里玩它[，或者继续阅读，了解它的工作原理！](http://cloudycam.8base.demo.s3-website-us-east-1.amazonaws.com/)

[![Cloudinary URL API Anatomy](img/0131167b59cc13151d7a1941ba089bce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1F9vFkSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1nnhvf3ruoy51906doj4.png)

首先，让我们快速看一下 URL API 的结构。在`upload/`段和`/horse.jpg`段之间有一大段参数。这些是请求图像时执行的图像转换列表。相当酷！对吗？文档就在这里，如果你想深入了解的话。

```
https://res.cloudinary.com/demo/image/upload/c_crop,g_face,ar_16:9,w_1200,h_600/e_auto_contrast/b_rgb:00000099,e_gradient_fade,y_-0.4/co_white,fl_relative,l_text:Times_100_bold_italic:I%20am%20a%20unicorn!,w_0.95/co_black,e_shadow,x_2,y_1/fl_layer_apply,g_south_west,x_20,y_25/dpr_auto,q_auto,f_auto/horse.jpg 
```

现在，你在下面看到的图像是使用上面的链接渲染的。此外，-这是至关重要的一部分-如果你改变转换，一个全新的形象得到回报！

[![Cool unicorn](img/780835bfd1747cbb2af3ee1cf125c18a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dtBBEguH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sj9fusjqry3q6wbcizg1.png)

在检查 URL 时,`l_text:Times_100_bold_italic:I%20am%20a%20unicorn!`很容易看到。虽然我们不能添加自定义的转换标签(即在 Cloudinary 端)，但是我们有能力将转换应用到 URL。这意味着，在本地化我们的图像覆盖的情况下，我们可以在请求图像之前强制 URL。

一个无服务器的 GraphQL 解析器函数可以被部署到一个 [8base 工作空间](https://8base.com)来完成这个任务。它可以处理 URL 的解析和翻译。有许多方法可以部署无服务器功能。然而， [8base](https://8base.com) 让它变得超级简单直接。

作为一个简单的说明，让我们设计如下行为的函数。

1.  如果文本前面有一个`local_[2-char-lang-code]`标签，翻译文本，并更新 URL。
2.  如果文本前面没有本地代码，则返回原始 URL。

## 谈够了，让我们开始吧

##### 1。创建一个新的 8base 项目

如果你有一个现有的项目，你可以随时添加一个新的功能。

```
# Install the CLI globally
npm install -g 8base-cli

# Initialize a new project with a GraphQL resolver called "localizer."
8base init my-project --functions="resolver:localizer" 
```

这些命令创建了一个新项目，其中包含了调用 GraphQL 解析器函数所需的所有文件和代码。在翻译我们的 Cloudinary URL 之前，我们需要做一些修改😉

##### 2。更新解析器的`graphql.schema`

在`src/resolvers/localizer/schema.graphql`打开文件。我们需要定义我们的查询操作和响应。在这种情况下，我们将在收到`cloudinaryUrl`后返回一个带有更新后的`url`的对象。用下面的代码片段更新文件。

```
type LocalizeResult {
  url: String!
}

extend type Query {
  localize(cloudinaryUrl: String!): LocalizeResult
} 
```

##### 3。更新`invoke-local`的模拟

更新`src/resolvers/localizer/mocks/request.json`,以便可以在本地用数据调用该函数。生成的模拟文件与传递给生产中的函数的模式相同。

```
{  "data":  {  "cloudinaryUrl":  "https://res.cloudinary.com/cdemo/image/upload/c_crop,g_face,ar_16:9,w_1200,h_600/e_auto_contrast/b_rgb:00000099,e_gradient_fade,y_-0.4/co_white,fl_relative,l_text:Times_100_bold_italic:local_es:Breaking%20news:%208base%20solves%20all%20your%20image%20related%20needs!,w_0.95/co_black,e_shadow,x_2,y_1/fl_layer_apply,g_south_west,x_20,y_25/dpr_auto,q_auto,f_auto/dosh1/img-0.jpg"  },  "headers":  {  "x-header-1":  "header value"  },  "body":  "{\"cloudinaryUrl\":\"https://res.cloudinary.com/cdemo/image/upload/c_crop,g_face,ar_16:9,w_1200,h_600/e_auto_contrast/b_rgb:00000099,e_gradient_fade,y_-0.4/co_white,fl_relative,l_text:Times_100_bold_italic:local_es:Breaking%20news:%208base%20solves%20all%20your%20image%20related%20needs!,w_0.95/co_black,e_shadow,x_2,y_1/fl_layer_apply,g_south_west,x_20,y_25/dpr_auto,q_auto,f_auto/dosh1/img-0.jpg\"}"  } 
```

##### 4。该功能

我们需要一个翻译引擎。我选择了 AWS 翻译，它每月提供 200 万个免费字符。让我们将所需的库和配置添加到项目中。

```
# Install AWS SDK
npm install --save aws-sdk 
```

更新`src/resolvers/localizer/handler.ts`

```
const AWS  = require('aws-sdk');

AWS.config.update({
    region: 'us-east-1', 
    credentials: {
      accessKeyId: process.env.AWS_IAM_SECRET_KEY, 
      secretAccessKey: process.env.AWS_IAM_ACCESS_KEY 
    }
});

const translate = new AWS.Translate({ apiVersion: '2017-07-01' });

/* Other code ... */ 
```

在本地开发时，您需要将 AWS 凭证设置为环境变量或静态值。您在上面看到的例子是当该功能被部署到 8base 时的工作情况。这里是关于访问 [8base 环境变量](https://docs.8base.com/development-tools/dev-env/runtime_environment#environment-variables)的文档。

因为我们使用的是 TypeScript，所以函数响应需要一个`type`。这个类型**必须**匹配添加到`graphql.schema`文件的结构和名称。对于我们的场景，将以下内容添加到函数体中。

```
type LocalizeResult = {
  data: {
    url: string
  }
}; 
```

函数体是不言自明的。与其在这里描述*在那里展示*然后在那里展示*，请阅读行内评论以澄清正在发生的事情。* 

```
export default async (event: any, ctx: any) : Promise<LocalizeResult> => {
  /**
   * Regex Statement for matching our custom local_tag and preceeding text
   */
  const REG_EX = /(local_[a-z]{2})\:(.*?)([,\/])/g
  /**
   * Pull the given cloudinary url from our function arguments 
   */
  let url = event.data.cloudinaryUrl
  /**
   * Execute our Regex statement returning a match object
   */
  const matchObj = REG_EX.exec(url);
  /**
   * If a local tag is matched, we're in business! If not,
   * we're simply returning the passed url.
   */
  if (matchObj) {
    /**
     * Pull out the matched local and text values from
     * the matchObj array.
     */
    let local = matchObj[1], text  = matchObj[2];

    try {
      /**
       * Make the request to AWS Translate after decoding the given text
       * and slicing the last two characters from the local tag (e.g. local_es)
       */
      let request = translate.translateText({
        TargetLanguageCode: local.slice(-2),
        SourceLanguageCode: 'auto',
        Text: decodeURI(text)
      }).promise();

      let data = await request;
      /**
       * The ACTUAL cloudinary url will break if it has our custom tag. Plus, we
       * need to update the text with the translation! So, let's replace the previously
       * matched locale and text with our tranlsated text, that needs to be escaped.
       */
      url = url.replace(`${local}:${text}`, data.TranslatedText.replace(/[.,%\`\s]/g,'%20'))
    } 
    catch (err) {
      console.log(err, err.stack);
    }    
  } 
  /**
   * Return the final result.
   */
  return {
    data: {
      url
    }
  }
}; 
```

##### 5。运行它！

搞定了。让我们通过本地调用我们的函数来证明这一点。返回的 URL 的文本部分被翻译成区域设置指定的语言！复制链接，并把它扔进浏览器看看它的神奇之处。

```
8base invoke-local localize -p src/resolvers/localize/mocks/request.json
invoking...

Result:
{
  "data": {
    "localize": {
      "url": "https://res.cloudinary.com/demo/image/upload/c_crop,g_face,ar_16:9,w_1200,h_600/e_auto_contrast/b_rgb:00000099,e_gradient_fade,y_-0.4/co_white,fl_relative,l_text:Times_100_bold_italic:¡Soy%20un%20unicornio%20genial!,w_0.95/co_black,e_shadow,x_2,y_1/fl_layer_apply,g_south_west,x_20,y_25/dpr_auto,q_auto,f_auto/horse.jpg"
    }
  }
} 
```

[![Alt Text](img/25e232876fe30a3c5a69483368352ac0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tJW2pQdF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z21h9bknxszlihrwn6uk.png)

## 🏁包裹

抱歉，我们要回去讲故事了。还记得你还是社交媒体实习生的时候吗？那么，您最终找到并使用了 Cloudinary 进行所有动态图像转换，并使用 8base 来简化无服务器 GraphQL 函数的快速无服务器部署。

因有机会成为“本月最佳员工”而兴奋不已，你走近老板，告诉他这个大消息，并说:

> “我能够使用 URL API 对我们的图像应用动态 URL 转换，并扩展其功能以支持文本覆盖的实时翻译！”

你的经理看着你的手，似乎很困惑，回答道:

> “你忘了我的咖啡？”

*[Cloudinary](https://cloudinary.com) 和 [8base](https://8base.com) 都比这篇文章做得更多。我强烈建议你去看看！**