# 如何用 json 输出在 rest 框架中按月-年制作分组数据列表？

> 原文：<https://dev.to/tipu/how-can-i-make-a-list-of-group-data-by-month-year-in-rest-framework-with-json-output-5cpf>

我想要的 json 数据格式是这样的:

```
[
  {
    monthName: "AUG 2019",
    items: [
      {
        day: "03",
        description: "Baseball",
      },
      {
        day: "08",
        description: " Baseball 2"
      },

    ]
  },
  {
    monthName: "Sep 2019",
    items: [
      {
        day: "03",
        description: "Baseball",
      },
      {
        day: "08",
        description: " Baseball 2"
      },

    ]
  }
] 
```

Enter fullscreen mode Exit fullscreen mode