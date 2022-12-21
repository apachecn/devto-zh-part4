# 如何在子 div 中禁用模糊

> 原文：<https://dev.to/demiragovic/how-to-disable-blur-in-child-div-20d8>

我有一个背景图像是模糊的 div，我已经创建了一些内容的子 div。子 div 中的内容很模糊，我不知道如何禁用它。

html 文件:

```
<div class="home-div" id="img_container">
    <div class="row container slider">
        <div class="col-4 disable-blur">
            <p>some text</p>
        </div>
    </div>
</div> 
```

css 文件:

```
#img_container {
    width: 100%;
    height: 70%;
    overflow: hidden;
    background: url('/assets/mbtownpanorama.jpg');
    background-size: auto;
    background-position: left;
    animation: back-and-forth 90s infinite;
    -webkit-filter: blur(5px); /* Safari 6.0 - 9.0 */
    filter: blur(5px);
  }

  @keyframes back-and-forth {
    0% {
      background-position: left;
    }
    50% {
      background-position: right;
    }
    100% {
      background-position: left;
    }
  }

  .disable-blur{
    filter: blur(0)!important;
    -webkit-filter: blur(0)!important; /* Safari 6.0 - 9.0 */
  } 
```