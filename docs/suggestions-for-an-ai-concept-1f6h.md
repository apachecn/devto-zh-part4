# 对人工智能概念的建议

> 原文：<https://dev.to/fultonbrowne/suggestions-for-an-ai-concept-1f6h>

这是一个概念，我有一个更快更简单的声音模型。它使用 tesorflow 和 java，我希望在我正在开发的应用程序 [ara](https://github.com/FultonBrowne/Ara-android) 中实现这一点。

在我的 ara 语音识别的基本版本中有这样的代码:

```
 switch (labelIndex - 2) {
    case 0:
        resulttxt = "yes";

        break;
    case 1:
        resulttxt = "no";
        break;
    case 2:
        resulttxt = "up";
        break;
    case 3:
        resulttxt = "down";
        break;
    case 4:
        resulttxt = "left";
        break;
    case 5:
        resulttxt = "right";
        break;
    case 6:
        resulttxt = "on";
        break;
    case 7:
        resulttxt = "off";
        break;
    case 8:
        resulttxt = "stop";
        break;
    case 9:
        resulttxt = "go";
        break;
                                } 
```

Enter fullscreen mode Exit fullscreen mode

我的想法是采用这个基本概念，但不是完整的单词，而是寻找声音和单词。这可能是新的识别码:

```
 switch (labelIndex - 2) {
    case 0:
        resulttxt = resulttxt + "LongA";

        break;
    case 1:
        resulttxt = resulttxt + "ShortA";
        break;
    case 2:
        resulttxt = resulttxt +"ShortB";
        break;
    case 3:
        resulttxt = resulttxt +"LongB"; // Such as the words be and bee.
        break;
    case 4:
        resulttxt = resulttxt +"ShortC";
        break;
        // .......................................................
                                } 
```

Enter fullscreen mode Exit fullscreen mode

请给你的投入，我不是人工智能专家，所以如果它哑告诉我。