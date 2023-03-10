# 我造了一个机器人，试图从我的网络供应商那里要回钱

> 原文：<https://dev.to/healeycodes/i-built-a-bot-to-try-and-get-money-back-from-my-internet-provider-33ip>

在我与 ISP 的合同中，他们给了我一个预期的速度范围，但也是保证的最低速度。如果他们不能保持在这个最低限额以上，我就有资格每年享受几次折扣。

我不想坐在垃圾邮件 speedtest.net 检查我的速度越来越低。我想知道是否有下降。下沉是在同一时间吗？有什么规律吗？我需要数据和一些数据可视化，以了解我是否/何时有资格享受任何折扣，以及是否值得追求。

### 类似这样的东西

[![Bandwidth graph](img/0c224e5c2bdb58ba8800a2981932643c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yxMDP26X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t8llz1s3y4unbf6i4bg.png)

[![Raspberry Pi](img/be3913bc43aa020601bf37ea12ad16ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aKc_3oBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b2aa9v17yfdvdr9p9lt0.jpeg)

在我值得信赖的覆盆子馅饼上奔跑。通过路由器的 USB 电缆供电，并通过以太网连接，因此结果(除其他网络使用外)应该是可靠的。

<small>查看我当前的带宽结果 [**live**](https://bandwidth-checker.glitch.me) 或者在 [**GitHub**](https://github.com/healeycodes/bandwidth-checker) 上阅读源代码。</small>

### 过度设计

我开始这个项目时太复杂了。我使用名为 Selenium 的网络浏览器自动化框架访问了网飞的 fast.com 带宽测试来检查我的速度。它使用了一个无头的 Chromium 实例。

```
# wait for test to finish i.e. when 'your speed message' is shown WebDriverWait(driver, 120).until(
    expected_conditions.presence_of_element_located(
        (By.CSS_SELECTOR, FAST_COMPLETE_CSS))
) 
```

Enter fullscreen mode Exit fullscreen mode

这不仅非常脆弱，容易受到 CSS 修改(必须硬编码，见上)的影响，还意味着这个项目的任何用户都需要为 Raspbian(Raspberry Pi 的默认 Linux 发行版)安装 headless Chromium。在解决了这个问题并让它运行之后，我意识到这是过度设计了。

使用这种方法仍然是项目中的一个选项(`browsertest.py`)，但是由于浏览器开销，它在 Raspberry Pis 上给出的结果不太准确。注意:这个项目可以在任何平台上工作，但是主要针对 Pis。

我在游戏后期发现 speedtest.net 有一个很棒的 CLI 库，我应该从一开始就使用它。下面是我如何在`clitest.py`中获得下载速度的。

```
import speedtest

# .. 
def get_speed():
    """
    Use Speedtest CLI to test bandwidth speed.
        :return: Download speed in Mbps
    """
    s = speedtest.Speedtest()
    s.download()
    results_dict = s.results.dict()
    return results_dict['download'] / 1048576  # convert bits to megabits 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本通过 crontab 运行，并指向服务器。类似于`python clitest.py 'https://server-location/save' 'password'`的命令每半小时运行一次。Windows 的替代品是任务调度程序，但我认为它比较笨重。

### 后端

我认为能够从任何地方检查我的速度会很棒，所以我创建了一个 [Glitch 项目](https://bandwidth-checker.glitch.me/)来存储、接收和托管结果。这是一个 Express/Node 项目，有两个 API 路径。

结果和密码一起被发送到`/save`，密码是通过 Glitch 上的环境变量设置的。

可以从 JSON 中的`/read`读取一片结果。这是它的快速路线。

```
// get bandwidth test results for graphing here
app.get("/read", function(request, response) {
  const data = db.get("results").value();
  const prepared = data.map(s => {
    return { x: s.date, y: Number(s.speed).toFixed(3) };
  });
  const trimmed = prepared.slice(Math.max(prepared.length - 48, 1));
  response.send(trimmed); // send a slice of results
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于存储，我想要不需要任何设置的东西。是一个小型的本地 JSON 数据库，它非常完美，因为只有一个进程在读或写，写事件大约每半小时发生一次。`lowdb`如果“数据库”文件不存在，则创建该文件。

### 数据可视化

Chart.js 是 JavaScript 中的图形库，使用 Canvas API。它包括电池，默认情况下看起来很好(但也许我只是习惯了这种风格！).大概五十行，包括 API 调用。

```
fetch('/read')
    .then(response => response.json())
    .then(json => renderGraph(json));
const safeDate = time => new Date(parseInt(time)).toUTCString();
const renderGraph = (speedData) => {
    var ctx = document.getElementById('myChart').getContext('2d');
    var myChart = new Chart(ctx, {
    type: 'scatter',
    data: {
        datasets: [{
            data: speedData,
            backgroundColor: () => 'rgba(255, 99, 132, 0.2)',
            borderColor: () => 'rgba(255, 99, 132, 1)',
            borderWidth: 1,
            pointRadius: 5,
        }]
    },
    options: {
        scales: {
            xAxes: [{
                type: 'linear',
                position: 'bottom',
                ticks: {
                    userCallback: (label, index, labels) => safeDate(label)
                },
                scaleLabel: {
                    display: true,
                    labelString: 'Date'
                }
            }],
            yAxes: [{
                scaleLabel: {
                    display: true,
                    labelString: 'Mbps'
                },
                ticks: {
                    beginAtZero: true
                }
            }],
        },
        title: {
            display: true,
            text: 'Bandwidth Test Results'
        },
        legend: {
            display: false,
        },
        tooltips: {
            callbacks: {
                label: function(tooltipItem, data) {
                return `${tooltipItem.value} Mbps @ ${safeDate(tooltipItem.label)}`;
                }
            }
        }
    }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现使用 Chart.js 很容易，就像 JavaScript 一样，如果你想把一些东西放在一起，它是非常高效的。文档很棒，而且它是一个足够大的库，普通搜索会找到有用的 StackOverflow 答案。

### 何去何从

到目前为止，我的速度一直徘徊在保证的最小值附近，通常在当地时间晚上 8 点左右下降。目前，我没有理由抱怨！一个惊喜。

因为我现在有了我的速度的最新记录，所以诊断任何未来的带宽问题将会很简单。希望这个项目的一些代码可以帮助你诊断一天中任何简单的带宽问题。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。