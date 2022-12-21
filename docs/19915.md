# 反应:“我真希望这就是我编写组件的方式。”

> 原文：<https://dev.to/joelnet/react-i-really-wish-this-is-how-i-could-write-components-1k4j>

> ![unknown tweet media content](img/71ed9ee08a7a6fe3a4350cf15dd675e5.png)![Mike Piccolo profile image](img/098edae34b791712ade6d0755ff086ac.png)Mike Piccolo@ mfpiccolo![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我真希望我能这样写组件。04:08AM-05 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1136122559362084864)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1136122559362084864)11[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1136122559362084864)105

## 挑战接受！

下面的代码为`useMatchFetch`。

```
import React from "react";
import { useMatchFetch } from "./effects/useMatchFetch";

export const Example = () => {
  const render = useMatchFetch("https://swapi.co/api/people/1/?format=json");

  return render({
    pending: () => <div>Loading</div>,
    error: err => <div>{err.toString()}</div>,
    data: data => <pre>{JSON.stringify(data, null, 2)}</pre>
  });
}; 
```

## 观看我的直播

想看看我是如何创造这个的吗？在 Twitch 上看我！

[![Twitch Screenshot](img/b81a0584227fef6c8bf9420a424e42e9.png)](https://www.twitch.tv/videos/435160322)

## `useMatchFetch`

我真的很喜欢这个。我想我可能会在一些地方使用它。

```
import { useState, useEffect } from "react";

const render = data => match =>
  data.pending ? match.pending()
  : data.error ? match.error(data.error)
  : data.data  ? match.data(data.data)
  : null // prettier-ignore

export const useMatchFetch = url => {
  const [data, setData] = useState({ pending: true });

  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(data => setData({ data, pending: false }))
      .catch(error => setData({ error, pending: false }));
  }, [url]);

  return render(data);
}; 
```

## 结束

在推特上关注我

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)