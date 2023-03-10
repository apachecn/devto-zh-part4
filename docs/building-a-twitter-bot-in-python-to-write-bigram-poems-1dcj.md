# 用 Python 构建一个 Twitter 机器人来写 bigram 诗

> 原文：<https://dev.to/tomweinandy/building-a-twitter-bot-in-python-to-write-bigram-poems-1dcj>

我描述了我如何以及为什么用 Python 构建了一个 Twitter 机器人，将 tweets 转换成美丽的表情。我还界定了什么是重婚诗，并提出为什么它应该是一个文学创作。所有代码都可以在我的 Github 页面上找到。
液体错误:内部

### **灵感**

在研究生院的第三年，我开始研究文本分析以及如何将它应用到社交媒体的自然语言处理(NLP)中。我提交了一份研究提案，希望利用 Twitter 上的实时信息应用一些数据挖掘和 NLP 技术。在演示过程中，我举了一个例子，说明如何将一条 tweet 中的文本转换成一组 bigram 字符串(稍后会详细介绍)。下面是幻灯片:
[![](img/5a63bdb7ff792c12ecf48fb472f88d3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yJnEk8I2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mdr9zees3uv2f49hmkux.jpg)

我大声朗读了这份名单，并对它富有诗意的声音进行了评论。我说，如果我不成为一名经济学家，那么我可能会成为一名 slam 诗人，在那里我会把推特当成某种 bigram 诗来读。我的同学被这个路过的笑话逗笑了。

但是我后来想，“为什么不呢？”。

### **为什么**

该项目的目的是创造一个新的 Twitter 机器人，将公共 tweets 重写为 bigram 诗歌。它让我提高了我的编码技能，熟悉了 Twitter API。它甚至可以通过教别人自然语言处理技术来起到教育作用，但很可能不是。然而，我可以宣称，在这篇不起眼的博客文章之前，bigram poems 并不存在。

通过这个项目，我的贡献是提出二元模型的使用不仅仅是文本分析的手段，而是一种诗歌表达的形式。我构建了一个函数，它将实用的句子改写成简洁的诗歌。这表明普通语言有能力听起来优雅。或者这只是一个可爱的小项目。

### **什么是重婚诗？**

好问题！二元组诗是任何转换成一系列连续词对的短语。

语境对于理解词义非常重要。如果一篇在线评论描述了一家“食物不好吃”的餐馆，我们人类很容易理解其中的负面含义。如果文本分析只考虑单个单词的频率，那么计算机可能会将单词“good”解释为积极的情绪，并认为该短语也是积极的。解决这个问题的一个常用方法是将短语分解成 n 个单词，或者 n 个连续单词组成的组。二元模型就是其中 n=2 的一个例子。前一个短语的一个二元结构是这样的:

> “食物”，“食物是”，“不是”，“不好”

计算机更容易分析这些，并适当地将单词对“不好”标记为负面情绪。

### **The bigram_poem()函数**

构建发送二元组诗的 Twitter 机器人的第一步是编写一个自动将短语转换为二元组诗的函数。下面的 Python 代码描述了这个过程:

```
import nltk                                                           # natural language tool kit is a must for NLP in Python 
def bigram_poem(phrase):                                              # define new function bigram_poem   
    rejects = '¿.?!,[]|"“”();…{}«•*+@~><'                             # define punctuation to be removed
    phrase_reject = phrase.translate({ord(c): None for c in rejects}) # remove defined punctuation
    phrase_split = phrase_reject.split(' ')                           # split phrase by whitespace
    phrase_clear = list(filter(None, phrase_split))                   # strip any extra whitespace
    phrase_shortened = phrase_clear[0:8]                              # only use the first 8 terms
    phrase_bigram = list(nltk.bigrams(phrase_shortened))              # convert to bigram list
    tw = ''                                                           # create new string
    for b in phrase_bigram:                                           # loop through bigrams
        tw += ' ' + b[0] + ' ' + b[1] + ' \n'                         # add looped bigrams to string w line break
    return(tw) 
```

Enter fullscreen mode Exit fullscreen mode

对于没有编程经验的人，我将详细说明上面的几个步骤。首先，我舍弃了阻碍诗歌流畅的标点符号。接下来，我将这个短语截短为前八个单词，以确保期望的输出简短明了。最后，我将短语按单词拆分，并转换成一个二元模型列表。

我使用电视节目《发展受阻》中的一系列简短引用来测试这个函数。然后，我从列表中随机选择一段引文，并将其转换成一首 bigram 诗。下面是三个例子。

```
import random

AD = ['I hear the jury’s still out on science.', 'I’m a monster!', 'Baby you got a stew going.', 'Do these effectively hide my thunder?', 'Army had a half day.', 'Say goodbye to these!', 'This party’s going to be Off. The. Hook.', 'There are dozens of us. Dozens!', 'And that’s why you always leave a note.', 'I’m afraid I just blue myself.', 'There is always money in the banana stand.', 'I’ve made a huge mistake.', 'Dead dove. Do not eat.', 'Here’s some money. Go see a Star War.', 'It’s hot ham water!', 'But where does the lighter fluid come from?', 'Get rid of the Seaward.', 'You’re just a chicken.', 'It’s an illusion Michael!', 'For British eyes only', 'Family love Michael.', 'Watch out for hop-ons.', 'They don’t allow you to have bees here.', 'Has anyone in this family seen a chicken?', 'Solid as a rock!', 'Did nothing cancel?', 'I know you’re the big marriage expert.', 'She calls it a mayonegg.', 'It’s vodka. It goes bad once it’s opened.', 'Don’t call it that.', 'On the Next Arrested Development...', 'Luz, that coat costs more than your house!', 'I just want my kids back.', 'I have Pop Pop in the attic.', 'The soup of the day is Bread.', 'My heart is straining through my shirt', 'Maybe, I’ll put it in her brownie.', 'I like hot sailors.', 'I understand more than you’ll never know.', 'Who’d like a banger in the mouth?', 'And that’s why you don’t yell.', 'I don’t care for GOB.', 'No touching! No touching!', 'Glasses off, hair up.', 'And I think I maced a crane.', 'You’re my third least favorite child.', 'Something that says leather daddy?', 'I enjoy scholarly pursuits.', 'You’re a crook, Captain Hook...', 'And this is not a Volvo.', 'Rita corny, Michael.', 'We’re having a fire sale.', 'Tea for dong!', 'They said it was a bob.']

print(bigram_poem(random.choice(AD)))               # Convert random quote to bigram poem 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/d85193571ad451e25a082cc3452345e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YSsYnVf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/urm28va7p08e29eipfvn.png)

### **Twitter API**

我用账号[@ bigram poeties](https://twitter.com/BigramPoetry)建立了一个 Twitter 账户，在平台上申请[开发者身份](https://developer.twitter.com/en/apply-for-access.html)并被接受。我根据网站的[自动化规则](https://help.twitter.com/en/rules-and-policies/twitter-automation)构建 Twitter 机器人，这些规则解释道:

> “只要你遵守所有其他规则，你可以出于娱乐、信息或新奇目的发布自动推文。作为提醒，发布重复、垃圾或其他禁止内容的帐户可能会被暂停。”

为了遵守这些规则，我在账户简介中清楚地解释了项目的意图，并标记了我的个人账户，以便于交流。为了防止机器人成为垃圾邮件，我限制每 15 分钟只能发送一条推文。

我现在准备好连接到 Twitter API，并在这里使用 [Twython 包装器](https://twython.readthedocs.io/en/latest/) :
来这样做

```
import json
from twython import Twython  

with open("twitter_credentials.json", "r") as file:                # load credentials from json file (not included for security reasons)
    creds = json.load(file)

twitter = Twython(creds['CONSUMER_KEY'], creds['CONSUMER_SECRET'],  
                    creds['ACCESS_TOKEN'], creds['ACCESS_SECRET']) # add credentials to access API 
```

Enter fullscreen mode Exit fullscreen mode

注意:我不共享私有访问信息，而是将它保存在一个单独的 json 文件中。关于如何安全保存 API 凭证，请参见此处的说明。

### **决定原文**

我首先考虑的原始材料是使用来自演员、音乐家、政治家、企业家和运动员的精选名单的推文。这有拥有大量追随者的优势，但也有不经常发推特的劣势，以及名人的短暂性，他们可能很快失去相关性。我也考虑过关注热门话题，但这违反了 Twitter 自动化规则。

当时的解决方案是跟随一个特定的标签，该标签将针对那些会对这个项目感兴趣的用户。我的第一选择#NLP 是一个糟糕的候选词，因为它与其他缩写词混淆了，并且在 Twitter 上不经常使用。相反，我选择使用包含“#machinelearning”的推文作为我的语料库。

记住了源文本并建立了 API 连接，我现在可以实时收集想要的 tweets 并应用额外的格式，如下所示:

```
import datetime
from twython import TwythonStreamer  

class MyStreamer(TwythonStreamer):                              # create a class that inherits TwythonStreamer
    def on_success(self, data):                                 # receive data when successful
                                                                # Conditions: 1) restrict tweets to 3 or more words, 
        tweet = data['text']                                    #    2) exclude retweets, 3) exclude links, 
        bigram_len = bigram_poem(tweet).count('\n')             #    4) English only, 5) exclude myself
        if 'text' in data and bigram_len > 2 \
        and 'RT' not in tweet \
        and 'http' not in bigram_poem(tweet) \
        and data['lang'] == 'en' \
        and data['user']['screen_name'] != 'BigramPoetry':
            tweet = tweet.replace('\n',' ').replace('VIDEO','') # remove edge cases with bad formatting
            tweet = tweet.replace(' -',' ').replace(' –',' ').replace(' ‘','')
            tweet = tweet.replace('- ',' ').replace(' /',' ').replace('— ',' ')
            tweet = tweet.replace(': ',' ').replace(':)','').replace('&amp','and')
            poem = 'A Bigram Poem inspired by ' + data['user']['screen_name']  # title line
            poem += ':' + '\n' + bigram_poem(tweet)                       # use bigram_poem function
            poem += '   -' + data['user']['name']                         # signature line
            twitter.update_status(status=poem)                            # tweet out on @BigramPoetry account
            print(poem)                                                   # print result, timestamp
            print(datetime.datetime.now(), '\n')
            self.disconnect()                                   # stop stream (so old tweets don't dam up) 
    def on_error(self, status_code, data):                      # when problem with the API
        print(status_code, data)
        print(datetime.datetime.now())
        self.disconnect() 
```

Enter fullscreen mode Exit fullscreen mode

我避免使用外语推文和转发，以确保我使用的是目标受众可以理解的原创内容。经过一系列的测试，我个别地纠正了一些边缘情况，即诗歌的格式被一些非常规的字符或间距所阻碍。最后，我将每条推文的格式设置为标题和签名行。

### **推送推文出去**

一旦传入的推文被转换成一首 bigram 诗，并按照需要进行格式化，它们就为 twittersphere 做好了准备。我用这个推文:

```
import time
import sys

stream = MyStreamer(creds['CONSUMER_KEY'], creds['CONSUMER_SECRET'],      # credentials for API streaming
                    creds['ACCESS_TOKEN'], creds['ACCESS_SECRET'])
while True:                                                               # continuously runs the function
            try:                                                                  # attempt to execute the below function
                status_stream = stream.statuses.filter(track='#machinelearning')  # start the stream to search for tweet
                time.sleep(900)                                                   # waits 15 minutes (900 seconds) before starting over
            except:                                                               # when above function fails, print error
                print("Unexpected error:", sys.exc_info()[0])
                time.sleep(900) 
```

Enter fullscreen mode Exit fullscreen mode

之前请注意流媒体功能在发出合适的推文后如何断开连接，以防止传入的推文阻塞。这里的 *while True* 语句和时间延迟重新启动流，每 15 分钟发布一条推文，以确保只使用符合定义标准的最新推文。 *try* 和 *except* 语句共同确保当错误发生时流不会被永久中断。

在测试我的原始代码时，这个账户被 Twitter 标记为垃圾邮件过多。该应用程序被钳制，并失去了 API 编写权限，直到它被修复。这里有一条相关的推文:

> ![Allan Enemark profile image](img/0e410035c2f79d66729606cf3a55e2fa.png)艾伦·恩马克@ exactly Allan![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)这个很好。[twitter.com/jalammar/statu…](https://t.co/zd3fnHMgnN)直观地了解 python 生态系统中的数据分析、机器学习和科学计算。我们还看了表格、图像和文本是如何用数字表示的。https://t.co/fTnw1XfYy1 https://t.co/WMLQA23fni[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1143948046562025472)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1143948046562025472)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1143948046562025472)

> ![Bigram Poetry profile image](img/584ed838065041a798e3ff3c0b1ecea6.png)双头诗@双头诗![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)一首双头诗灵感来自 [@exactlyAllan](https://twitter.com/exactlyAllan) :
> 这是
> 非常
> 非常好
> -Allan ene mark18:24PM-26 Jun 2019[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1143948068896825344)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1143948068896825344)

It turned out the issue was in the unsolicited @mention of the user as prohibited in the automation rules. I remove the '@' from the title line and for good measure also include it in the list of rejected characters within the bigram function. This means that any tweet including an *@screen_name* after going through the function will be reduced to just their *screen_name*.

### **托管 bot**

现在的最后一步是允许 Twitter 机器人持续运行，而不依赖于我的笔记本电脑。经过两天的在线培训，我能够理解亚马逊网络服务(AWS)的基础知识。我用 [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) 发布了一个应用程序，它建立了一个服务器实例来运行代码，并分配了云存储来记录代码部署(分别是 EC2 和 S3，仅供参考)。

我对结果非常满意，下面显示的是推文发布前后的非随机样本。有些诗写出来有点奇怪，但总的来说，我对其优雅的构图印象深刻。说真的，大声读出来，听听他们的诗！
液体错误:内部

> ![Bigram Poetry profile image](img/584ed838065041a798e3ff3c0b1ecea6.png)

This Twitter bot that automatically writes Bigram Poems may not be what the world needed, but it is certainly what the world deserves.

### **感恩与资源**

我想首先感谢我的朋友塞思·约斯特作为这个项目的顾问，并解决了 AWS 的一些问题。我也感谢 Emily Cain 的博客文章 T1 和 Jared 的 T2 文章 T3，这两篇文章都概述了这个过程的各个方面并提供了样本代码。

对于那些希望更多地探索 Python 中的 Twitter API 的人，我推荐[这个指南](http://2017.compciv.org/guide/topics/python-nonstandard-libraries/twython-guide/twitter-twython-api-basics.html)，我承认我希望在项目完成之前就发现它。最后，查看 [Botwiki](https://botwiki.org) 的文章列表、样本机器人以及如何构建自己的机器人的指南。