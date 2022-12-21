# 纱线即插即用和因果报应

> 原文：<https://dev.to/cawabunga/yarn-plug-n-play-and-karma-omi>

在打开 Yarn 上的即插即用特性后，我在运行 javascript 测试时遇到了一个问题。严格来说，因果报应根本跑不了。

控制台上正在说:

```
./node_modules/.bin/karma: No such file or directory 
```

我很快意识到我通过路径直接调用二进制，但是`node_modules`是空的，所以我需要获取新的路径。这不是一个大问题，只是得到一个可执行文件的路径:`yarn bin karma`，对于它的运行简单来说:`yarn run karma`。

然后我运行了 Karma，但没有测试:

```
ERROR [preprocess]: Can not load "webpack", it is not registered!
ERROR [karma-server]: Server start failed on port 9878: Error: No provider for "framework:jasmine"! 
```

下面是一段配置文件:

```
// karma.config.js:
{
   ...
   browsers: ['Chrome'],
   frameworks: ['jasmine'],
   preprocessors: {
       './app/javascript/**/*.test.js': ['webpack']
   },
   ...
} 
```

在网上做了一些研究后，我没有找到任何可行的解决方案。所以我已经开放了 Karma 的源代码和它的依赖注入库。我发现 Karma 通过扫描所有可用的插件(预处理器、测试框架、浏览器启动器等)来动态加载插件。).(如果我更加专心并且正确地阅读文档，就不会有任何问题。)但是启用了 PnP 的 Yarn 有不同的目录结构，所以 Karma 看不到任何插件。

我们只需要在配置中严格指定插件，并将它们作为依赖项添加到您的`package.json`中。

```
// karma.config.js:
{
    ...
    plugins: [
        'karma-jasmine',
        'karma-webpack',
        'karma-chrome-launcher',
    ],
    ...
} 
```

### 结论

通过启用即插即用，在使用库的相对路径时要更加小心。并且不要忘记在您的`package.json`中指定您所有的显式和隐式依赖关系。