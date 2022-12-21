# 现在谁在太空？

> 原文：<https://dev.to/turbobasic/who-is-in-space-now-1gig>

```
$ curl --silent http://api.open-notify.org/astros.json | 
  jq --raw-output .people[].name

Alexey Ovchinin
Nick Hague
Christina Koch
Alexander Skvortsov
Luca Parmitano
Andrew Morgan 
```

Enter fullscreen mode Exit fullscreen mode