# 使用过滤器删除 docker 图像范围

> 原文：<https://dev.to/tiemen/deleting-ranges-of-docker-images-using-filters-ljm>

# 使用过滤器清除陈旧的 Docker 图像范围

如果`docker images`的输出跨越太多行，你想整理一下，但是你觉得 [`docker system prune`](https://docs.docker.com/engine/reference/commandline/system_prune/) 就像用 C4 开金枪鱼罐头，说不定拿`docker images`的`-f`旗兜一圈！

`docker images -f`利用[滤镜选项](https://docs.docker.com/engine/reference/commandliimg/#filtering)，它可以让你指定一些额外的搜索选项来准确选择你感兴趣的图像！

# 过滤选项

目前，Docker 支持一系列过滤器选项，但在这篇文章中，我们只关注`before`和`since`过滤器——要全面了解可用的过滤器选项，请访问 [Docker 关于过滤的文档](https://docs.docker.com/engine/reference/commandliimg/#filtering)

**-f 自= {图像 id}**
过滤器选项自取一个图像 id 并选择所有比给定图像更新的图像。

**-f before={image id}**
很像自，这个标签选择所有比给定图像 id 旧的图像。

要选择一系列 Docker 图像，只需将它们粘在一起即可！

# 举例

说够了；给我看看钱！所以——想象一下`docker images`的输出如下:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hippo-fe            latest              ec8ec381807d        55 minutes ago      455MB
hippo-backend_api   latest              88a08f954c9f        4 days ago          47.4MB
<none>              <none>              66ee4e399b4a        4 days ago          423MB
<none>              <none>              098006369e17        4 days ago          449MB
<none>              <none>              7010975558a2        4 days ago          449MB
<none>              <none>              75ecd4cee8ef        4 days ago          47.4MB
<none>              <none>              d3942b2482a2        4 days ago          449MB
<none>              <none>              88676747d621        4 days ago          449MB
<none>              <none>              eb522755627b        4 days ago          445MB
<none>              <none>              d62380cf625f        5 days ago          86.3MB
<none>              <none>              21bce955c85b        5 days ago          474MB
spacevim/spacevim   latest              a6b4bb27c4e9        2 weeks ago         1.36GB
<none>              <none>              abf507ccc201        2 weeks ago         106MB
<none>              <none>              79a409b99ca5        2 weeks ago         530MB
<none>              <none>              5fd5b5859b37        3 weeks ago         106MB
<none>              <none>              d93260d7abc3        3 weeks ago         530MB
node                10.16.3-alpine      b95baba1cfdb        3 weeks ago         76.4MB
<none>              <none>              2c62a47471d1        4 weeks ago         106MB
<none>              <none>              3de57027cc18        4 weeks ago         523MB
elixir              1.9.1-alpine        33a0cf122cf7        5 weeks ago         87.6MB
<none>              <none>              e5196ef97445        6 weeks ago         104MB
elixir              1.8.1-alpine        447a8dff23a8        5 months ago        91.1MB
alpine              3.9                 5cb3aa00f899        6 months ago        5.53MB 
```

这是一个很长的列表！假设我们不再对图像 id 为`88a08f954c9f`标记为`hippo-backend_api`的图像下方的图像和图像 id 为`a6b4bb27c4e9`的图像上方的图像感兴趣，我们将如何使用上述过滤器呢？

```
$ docker images -f before=88a08f954c9f 
```

会给我们所有在之前*创建的图像 *hippo-backend_api* 一个。下一步将是选择所有的 Docker 图像，直到历史中给定的图像:* 

```
$ docker images -f before=88a08f954c9f -f since=a6b4bb27c4e9 
```

给出给定范围内的所有图像:

```
<none>              <none>              66ee4e399b4a        4 days ago          423MB
<none>              <none>              098006369e17        4 days ago          449MB
<none>              <none>              7010975558a2        4 days ago          449MB
<none>              <none>              75ecd4cee8ef        4 days ago          47.4MB
<none>              <none>              d3942b2482a2        4 days ago          449MB
<none>              <none>              88676747d621        4 days ago          449MB
<none>              <none>              eb522755627b        4 days ago          445MB
<none>              <none>              d62380cf625f        5 days ago          86.3MB
<none>              <none>              21bce955c85b        5 days ago          474MB 
```

正如你所看到的，我们现在只给出了选定范围内的图像！

soo——我们现在要做的就是在`-q`标志上做标记，以获得一个图像 ID 列表，并通过管道将其发送到`xargs`,以便在`docker rmi -f`命令中使用它们作为参数...

```
$ docker images -f before=a6b4bb27c4e9 -f since=88a08f954c9f -q | xargs docker rmi -f 
```

...瞧啊。如果我们再次运行 docker images 命令，我们会看到给定的范围已经被删除:

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hippo-fe            latest              ec8ec381807d        2 hours ago         455MB
hippo-backend_api   latest              88a08f954c9f        4 days ago          47.4MB
spacevim/spacevim   latest              a6b4bb27c4e9        2 weeks ago         1.36GB
<none>              <none>              abf507ccc201        2 weeks ago         106MB
<none>              <none>              79a409b99ca5        2 weeks ago         530MB
<none>              <none>              5fd5b5859b37        3 weeks ago         106MB
<none>              <none>              d93260d7abc3        3 weeks ago         530MB
node                10.16.3-alpine      b95baba1cfdb        3 weeks ago         76.4MB
<none>              <none>              2c62a47471d1        4 weeks ago         106MB
<none>              <none>              3de57027cc18        4 weeks ago         523MB
elixir              1.9.1-alpine        33a0cf122cf7        5 weeks ago         87.6MB
<none>              <none>              e5196ef97445        6 weeks ago         104MB
elixir              1.8.1-alpine        447a8dff23a8        5 months ago        91.1MB
alpine              3.9                 5cb3aa00f899        6 months ago        5.53MB 
```

🙌