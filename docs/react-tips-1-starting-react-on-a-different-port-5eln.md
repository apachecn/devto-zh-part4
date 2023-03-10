# React 提示:1–在不同的端口上启动 React

> 原文：<https://dev.to/pcmichaels/react-tips-1-starting-react-on-a-different-port-5eln>

我想到了一个在 React 上开始一系列帖子的想法，这些帖子提供了关于你可以做的事情、你可能会遇到的错误以及未来的我可能会发现有用的任何其他事情的小提示。

我首先创建了一个新的 react 应用程序。我的意图是在 GitHub 上有一个[参考知识库。](https://github.com/pcmichaels/react-demos)

第一部分涉及在不同的端口上启动 react。

# 为什么？

有几个原因可以让您选择这样做，但其中之一是在运行时避开这个问题:

```
npm start
Something is already running on port 3000 
```

# 怎么

在您的项目中查找一个名为 package.json 的文件。在那里，您应该会看到类似这样的内容:

```
{ 
    "name": "react-demos",
    "version": "0.1.0",
    "private": true,
    "dependencies": {
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "react-scripts": "3.0.1"
},
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
}, 
```

在开始部分，您可以添加端口:

```
{ 
    "name": "react-demos",
    "version": "0.1.0",
    "private": true,
    "dependencies": {
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "react-scripts": "3.0.1"
},
"scripts": {
    "start": "set PORT=3005 && react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
}, 
```

默认端口是 3000，因此如果您不指定端口，它将总是尝试在 3000 上启动。

# 如果我不想换端口/为什么 react 不会死呢？

那么，您可以在 bash 中尝试以下命令:

```
taskkill -F -IM node.exe 
```

它应该会杀死你所有的反应过程。

# 参考

[https://github.com/facebook/create-react-app/issues/1083](https://github.com/facebook/create-react-app/issues/1083)

[https://stack overflow . com/questions/45544145/how-stop-after-running-react-scripts-start](https://stackoverflow.com/questions/45544145/how-stop-after-running-react-scripts-start)

[本帖最初发表于此](https://www.pmichaels.net/2019/07/20/react-tips-1-starting-react-on-a-different-port/)