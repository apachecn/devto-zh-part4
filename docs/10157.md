# 被动❤️s 的推特机器人

> 原文：<https://dev.to/jacobmparis/a-twitter-bot-for-passive-3s-121g>

将这个粘贴到你的浏览器控制台(或者用 Scriptish 制作一个插件),当你滚动
时，帖子会自动❤️

```
setInterval(() => {
  const heart = document.querySelector(`[data-testid="like"]`);
  if(heart) heart.click();
}, 1408); 
```

Enter fullscreen mode Exit fullscreen mode

或者将其粘贴到地址栏

```
javascript:setInterval((heart=document.querySelector(`[data-testid="like"]`)) => {heart && heart.click();}, 1500); 
```

Enter fullscreen mode Exit fullscreen mode

`disclaimer: Automated likes are against Twitter ToS`

`second disclaimer: don't go to anyone's profile or you'll look like a stalker`