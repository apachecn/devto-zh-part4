# Clojure Bits:使用 JSON

> 原文：<https://dev.to/alekcz/clojure-bits-working-with-json-6ok>

Clojure Bits:一口大小、对人友好的 clo jure Bits。今天，我们来聊聊互联网的中坚力量 JSON。我已经用 Clojure 编写了大量的 web 应用程序，到目前为止，我最喜欢的 JSON 库是 Cheshire。使用简单，速度快。非常快。以下是你可以开始使用它的方法。

*(此文首发于[medium.com](https://medium.com/@alekcz/clojure-bits-working-with-json-d93660e7b99f))*

## 安装

要安装 cheshire，将其添加到`project.clj`中的依赖项列表中，如下所示

```
:dependencies  [  ...some  dependencies...  [cheshire  "5.9.0"]  ; installed!  ...more  dependencies...] 
```

当你使用`lein`运行你的程序或启动 REPL 时，它就会下载。

## 使用它

首先将 cheshire 添加到当前名称空间。根据您是在 REPL 中工作还是在文件中工作，有两种方法可以实现这一点。

```
In  a  file  =>  (:require  [cheshire.core  :as  json])  In  the  REPL=>  (require  '[cheshire.core  :as  json]) 
```

## API

### Clojure 映射到 JSON

```
(json/encode  {:message  "build passing"})  ;; "{\"message\":\"build passing\"}" 
```

### JSON 到 clojure 的映射

```
(json/decode  valid-json-object)  ;; {message "build passing"}  ;; notice the map doesn't have keywords 
```

### JSON 到 clojure 用关键字映射

```
(json/decode  valid-json-object  true)  ;; {:message "build passing"}  ;; Yay, much nicer! 
```

柴郡能做的远不止这些。但这是你 99%的时间都需要的。

祝你的身材永远过关。

亚历克斯

## 参考文献

*   [https://github.com/dakrone/cheshire](https://github.com/dakrone/cheshire)
*   无数小时的调试