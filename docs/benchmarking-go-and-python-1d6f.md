# 基准测试 Go 和 Python

> 原文：<https://dev.to/fergyfresh/benchmarking-go-and-python-1d6f>

# Gin 和 Flask 之间 GET 请求的简单基准测试

## 序言

您需要安装 Go 和 Python 才能运行。

## 简介

在完成这篇文章的所有代码后，我注意到这两篇文章都与饮酒有关(杜松子酒和烧瓶)。在分析了 web 框架的 go 生态系统(Gin，Martini，Web.go，Beego，Goji，Gorilla)之后，我决定让 Gin 和 Flask 对决。这似乎是我最喜欢的 python web 框架之一，也是众所周知的最快的框架之一。

理想情况下，我会在几个文件中这样做，并可能在 Python 的生产中使用 gunicorn，并以不同的方式构建应用程序，但我只想在 ython 中构建这个示例的最精简版本，以比较 Flask 与 Gin 的纯速度，以及它们在对应用程序的 get 请求中仅返回一个简单的 JSON 对象的最小 docker 构建的大小。

创建一个名为 pygo-benchmark 的目录，并将当前目录更改为该目录。

## 用杜松子酒上网

在 pygo-benchmark 的一个子目录中完成所有这些工作，我称它为 go-stuff，但是您可以随意命名它。

### 写文件

让我们开始建设。我使用 vim-go，这样在第一个`vim file.go`命令时，您就可以获得一个完整的软件包框架。所以当我输入`vim main.go`时，我得到:

```
package main

import "fmt"

func main() {
    fmt.Println("vim-go")
} 
```

因此，您还应该创建一个名为`main.go`的文件，并将上面的代码添加到该文件中。现在我们要安装 gin。为了做到这一点，我们将输入`go get -u github.com/gin-gonic/gin`，这将为我们安装 go 模块。好了，现在我们已经安装了 gin，让我们修改文件，让我们的`main.go`现在使用 gin，并返回一个简单的 json 对象到`localhost:8080`，这个 web 服务器的默认端口。

```
package main

import "github.com/gin-gonic/gin"

func main() {
    router := gin.Default()

    router.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "pong",
        })
    })

    // router runs on :8080 by default
    router.Run()
} 
```

### 构建并运行

用 go 建立一个文件非常简单，`go build main.go`就是你所需要的。现在，在您所在的目录中应该有一个名为 main 的可执行文件。如果您只是在构建文件的同一个目录下运行命令行中的`/main`，它应该会有一些标准的输出，比如:

```
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /                         --> main.main.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080 
```

如果你已经做到了这一步，恭喜你构建并运行了你的第一个 go 应用程序！

## 带 Flask 的 Python web-app

在 pygo-benchmark 的子目录中做所有这些，我称之为 flask-stuff，但是再次做你需要做的事情。

### 写文件

为了使这篇文章更短，我不打算介绍整个设置，我只是想把文件放在这里。用下面的内容创建一个名为`main.py`的文件:

```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def ping():
    return '{"message": "ping"}'

if __name__ == "__main__":
    app.run(host="0.0.0.0") 
```

## 构建并运行 python

因此，python 中没有构建，我们将在我们的基准测试部分得到好处。

我喜欢在 virtualenv 的命令行上做任何事情，但是如果你不介意弄乱你的包，做你想做的事情，为了让这个文件运行起来，我会这样做:

```
virtualenv .venv -p python3.6
source .venv/bin/activate
pip install flask
python main.py 
```

因为这个文件是用`if __name__...`设置的，我们可以从命令行运行这个文件，就像这个`python main.py`，如果我们成功了，我们应该会看到这样的东西:

```
(.venv) ➜  flask-stuff ✗ python main.py
 * Serving Flask app "main" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 313-728-815 
```

## 对标各设置的纯速度。

### 在 python 中使用 requests 和 timeit

现在让我们把标准样板文件拿出来。你知道，在运行基准测试的机器上，结果可能会有所不同，你真的需要有大量的机器才能真正证明 benchmars，yada-yada。

好了，现在我喜欢用 python 中的`timeit`来计时。为了找到更有价值的基准(平均响应时间)，您可以一个接一个地进行多次运行。

因为我们应该有两个 web 服务器在运行，所以 go 服务器在`localhost:8080`运行，python 在`localhost:5000`运行。我们给他们俩计时，好吗？:

```
pip install requests
python
>>>
>>>import timeit
>>>import requests
>>>
>>> # gin / go
>>> timeit.timeit("requests.get('http://localhost:8080')", setup="import requests", number=10000)
21.956489593023434
>>> # flask / python
>>> timeit.timeit("requests.get('http://localhost:5000')", setup="import requests", number=10000)
34.483878459897824 
```

酷毙了。如您所见，Go 选项快了大约 36%!这太不可思议了，难怪现在人们都喜欢 go > python。但是，这也不是最公平的测试，因为我们可以在 python 设置之前放置一个多线程的 WSGI 服务器。现在进入第二轮...

### 使用名为 wrk 的新型基准测试工具

多线程处理所有的事情！感谢一位热心读者发来的一个很好的建议，我还添加了一个多线程基准测试，并添加了 gunicorn 来坐在我们的 Flask 应用程序前面。

为了跟上我的速度，你必须使用这里的说明[下载并制作 wrk 的二进制文件](https://github.com/wg/wrk/wiki/Installing-wrk-on-Linux)，如果你是 Windows/Mac 用户，你会发现边上有更多的选项。我们还需要`pip install gunicorn`,在 flask-stuff 目录中，我们需要输入`gunicorn -w 8 main:app`,它告诉 gunicorn(我们的多线程 WSGI 服务器)使用 8 个线程(因为在我的机器上`nproc --all`的输出是 8 个),它说查看`main.py`文件和`app`函数作为应用程序的入口点。

现在开始用“真正的基准测试工具”——一个叫 Ben 的家伙——对这两者进行基准测试。

```
➜  go-hello wrk -t8 -c32 -d30s http://127.0.0.1:5000 # flask-python
Running 30s test @ http://127.0.0.1:5000
  8 threads and 32 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.58ms    1.70ms  42.16ms   94.75%
    Req/Sec     1.12k   158.54     2.83k    84.48%
  268403 requests in 30.10s, 45.82MB read
Requests/sec:   8917.30
Transfer/sec:      1.52MB
➜  go-hello wrk -t8 -c32 -d30s http://127.0.0.1:8080 # go-gin
Running 30s test @ http://127.0.0.1:8080
  8 threads and 32 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.64ms    4.71ms  60.39ms   85.07%
    Req/Sec     6.86k     0.93k   33.27k    82.09%
  1639477 requests in 30.10s, 220.46MB read
Requests/sec:  54471.39
Transfer/sec:      7.32MB 
```

好吧，也许本是对的，这确实表明了围棋比 python 好得多。然而令人惊讶的是，python 确实有一个更确定的响应时间，因为延迟的标准偏差小于 2 毫秒，而在 go 中几乎是 5 毫秒。

但是再一次，这是证明 go 比 python 更快的另一个基准。平均而言，Req/sec wise go 比 flask 快 6.125 倍。

## 对 Docker 图像尺寸进行基准测试。

那好吧。我只是想说，这篇文章已经太长了，你不会让我分解我迭代的多级 docker 构建，以获得每个构建的最小图像大小。

### Python Dockerfile

让我们将这个文件放在`flask-stuff/Dockerfile`目录中，内容如下:

我们的结构应该是这样的:

*   烧瓶之类的东西
    *   main.py
    *   Dockerfile

```
FROM python:3.6-alpine
COPY . /
WORKDIR /
RUN pip install flask
RUN pip install gunicorn
CMD ["gunicorn"  , "-w", "8", "main:app"] 
```

为了构建它，你需要输入`docker build . -t benchmark/py`。

### Go Dockerfile

让我们将这个文件放在`go-stuff/Dockerfile`目录中，内容如下:

我们的结构应该是这样的:

*   去东西
    *   main.go
    *   Dockerfile

```
FROM golang:latest
ADD . .
RUN go get -u github.com/gin-gonic/gin
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .
RUN ls
RUN pwd

FROM alpine:latest 
RUN apk --no-cache add ca-certificates
COPY --from=0 /go/main .
CMD ["./main"] 
```

为了构建它，你需要输入`docker build . -t benchmark/go`。

### 想必会有什么结果？！？

我有结果了，我不叫雪莉！我知道这是个糟糕的笑话，但我想让你保持清醒。

好了，这是数据:

```
➜  pygo-benchmark docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
benchmark/go        latest              02ef15405471        About an hour ago   21.7MB
benchmark/py        latest              8fd24d5e54f1        About an hour ago   89.6MB
python              3.6-alpine          83d065b0546b        19 hours ago        79MB
golang              latest              901414995ecd        2 weeks ago         816MB
alpine              latest              caf27325b298        3 weeks ago         5.53MB 
```

这太惊人了！`benchmark/go`图像比`benchmark/py`图像小大约 76%。这是因为几个原因，go 实际上只需要一个二进制文件`main`就可以运行，而 python 需要所有的支持库，因为它没有被编译。

如果我们使用 golang 图像作为基础，而不是 alpine 图像作为我们的`benchmark/go`图像的基础，它将是大约 835MB。Go docker 映像如此之小的原因是因为您可以直接在 alpine 映像上运行它们，这大约是 5MB。

# 疑问、评论、担忧

请随意点击这些按钮中的一个，以便向我发出信号，告诉我有什么事情搞砸了。我很乐意解决任何对你不起作用的问题。