# 使用自动气象站 Lambda 和 Twilio 发送天气预报

> 原文：<https://dev.to/andrewevans0102/sending-a-weather-forecast-with-an-aws-lambda-and-twilio-23np>

[![Alt Text](img/aa903d186c81a8032710564b4b1c42a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4WqDwI2y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y2wj5i2m33ouyh749wbc.jpg)

最近，我需要获取每日天气预报，但我想通过短信而不是应用程序或电子邮件获取。

在研究了一些技术后，我意识到我可以用一点 JavaScript 构建自己的每日提醒。

这篇文章将讲述我是如何通过 [AWS Lambda](https://aws.amazon.com/lambda/) 和 [Twilio](https://www.twilio.com/) 发布每日天气预报的。我的项目是用 JavaScript 编写的，使用了 AWS node SDK。

我的项目的开源版本可以在这里的 GitHub 上找到。

## 整体流程

在我们过于关注细节之前，我想强调一下整体流程。

我构建的项目有以下流程:
[![flow chart](img/324ed556128fd04a8d7661029a5d3c8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TD3jBiwY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kme4zq6bqtjozdi9nq5y.png)

1.  Lambda 由一个 [CloudWatch](https://aws.amazon.com/cloudwatch/) 调度的 cron 作业触发
2.  Lambda 为当前条件调用 [OpenWeatherMap API](https://openweathermap.org/api)
3.  Lambda 调用 [NOAA API](https://www.weather.gov/documentation/services-web-api) 获取预测数据
4.  创建一条包含所有内容的消息
5.  文本消息通过 Twilio 的 API 发送

## 创建 AWS Lambda 和 ClaudiaJS

该项目的第一步是创建一个 AWS Lambda。Lambda 是 AWS 无服务器平台，近年来变得非常流行。Lambdas 的整个前提是让代码在云中运行。开发人员可以部署他们的代码，它将在必要时运行。最终，减轻开发人员维护托管基础设施的需求。要了解更多信息，我建议点击查看 Lambdas 上的 AWS 文档。

为了创建我使用的 Lambda，我使用了 [ClaudiaJS CLI](https://claudiajs.com/documentation.html) 。ClaudiaJS 使创建 Lambdas 变得非常简单，因为它节省了您在 AWS 控制台中工作的时间和精力。CLI 使您能够使用带有参数的预构建脚本来构建和部署基础架构。一旦你构建了一个 Lambda，ClaudiaJS 还允许你更新它并在本地测试它。ClaudiaJS 有很多很棒的教程，我推荐你[从这里的教程开始](https://claudiajs.com/tutorials/hello-world-lambda.html)。

我还写了一个单独的帖子，更详细地讨论了使用 ClaudiaJS 构建无服务器 API 的细节。

遵循 ClaudiaJS 指南，我使用了基本的`claudia create`和`claudia update`命令。为了自己完成这项工作，您首先需要安装 ClaudiaJS CLI 和 AWS CLI。

*   点击[进入 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 。
*   点击[进入 ClaudiaJS CLI](https://claudiajs.com/tutorials/installing.html)

一旦你有了初始设置，做一个[我的项目](https://github.com/andrewevans0102/weather-text)的`git clone`。请参考我的项目的自述文件，了解如何设置环境变量。

一旦将环境变量添加到终端的配置文件中，就可以使用项目的 npm 脚本来完成剩下的工作。

在终端中，导航到您克隆项目的位置并运行`npm run create-lambda`。

如果您想对 lambda 进行更新，您也可以使用我的 npm 脚本和`npm run update-lambda`，这将把任何代码更改直接应用到 AWS 中的 Lambda。

一旦你的 Lambda 构建完成，你也可以在我的项目中使用 npm 脚本用 ClaudiaJS 测试它。在终端运行`npm run local-test`来测试你的 Lambda。

## 获取天气预报

为了得到天气预报，我同时使用了 [OpenWeatherMap API](https://openweathermap.org/api) 和[美国国家海洋和大气局(NOAA)API](https://www.weather.gov/documentation/services-web-api)。NOAA APIs 是免费的，但是你需要用 OpenWeatherMap API 创建一个(免费)帐户来使用它们的端点。点击查看 [OpenWeatherMap API 网站，获取密钥。](https://openweathermap.org/api)

在我的 Lambda 中，我从 OpenWeatherMap API 和 NOAA 中检索了天气预报，结果如下:

```
const APIKey = process.env.OPEN_WEATHER_MAP_API_KEY;
const latitude = process.env.LATITUDE;
const longitude = process.env.LONGITUDE;
const units = "imperial";

// OpenWeatherMapAPI
const openWeatherMapAPIURL = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&units=${units}&appid=${APIKey}`;
const currentWeather = await axios.get(openWeatherMapAPIURL).catch(error => {
  console.log(error);
  return;
});

// NOAA Metadata
const NOAAMetadata = await axios
  .get(`https://api.weather.gov/points/${latitude},${longitude}`)
  .catch(error => {
    console.log(error);
    return;
  });

// NOAA Weekly
const NOAAWeeklyForecast = await axios
  .get(NOAAMetadata.data.properties.forecast)
  .catch(error => {
    console.log(error);
    return;
  });

// NOAA Hourly
const NOAAHourlyForecast = await axios
  .get(NOAAMetadata.data.properties.forecastHourly)
  .catch(error => {
    console.log(error);
    return;
  }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意到了，我在这里使用 [axios](https://www.npmjs.com/package/axios) 来进行 HTTP 调用。这使得每次通话都变得非常简单和容易。

我使用我想要天气预报的地点的纬度和经度坐标来打电话。我首先调用 OpenWeatherMap API 来获取当前的天气状况。然后，我给 NOAA 的`metadata`、`weekly forecast`和`hourly`端点打电话，获取天气预报。

一旦我成功检索到天气信息，就只需要解析响应了，如下所示:

```
const hoursToday = retrieveHours(NOAAHourlyForecast.data.properties.periods);

let highTemp = 0;
hoursToday.forEach(period => {
  if (parseInt(period.temperature) > highTemp) {
    highTemp = period.temperature;
  }
});

let lowTemp = highTemp;
hoursToday.forEach(period => {
  if (parseInt(period.temperature) < lowTemp) {
    lowTemp = period.temperature;
  }
});

const sunrise = formatTime(currentWeather.data.sys.sunrise);
const sunset = formatTime(currentWeather.data.sys.sunset);
const message =
  "WEATHER TEXT:\n" +
  "\n" +
  "Good Morning!  ☀️ 💦 🌤 ⛈ \n" +
  "Here's the lowdown for today...\n" +
  "\n" +
  // to show degree symbol on OSX hold shift + option + 8
  `temp: ${currentWeather.data.main.temp.toFixed(0)}°\n` +
  `high: ${highTemp.toString()}°\n` +
  `low: ${lowTemp.toString()}°\n` +
  `wind: ${currentWeather.data.wind.speed.toFixed(0)} MPH\n` +
  `sunrise: ${sunrise} AM\n` +
  `sunset: ${sunset} PM\n` +
  "\n" +
  `forecast: ${NOAAWeeklyForecast.data.properties.periods[0].detailedForecast}\n` +
  "\n" +
  "Have a good day! 🎉🎉 🎉 🎉"; 
```

Enter fullscreen mode Exit fullscreen mode

注意，我还为`sunrise`和`sunset`值使用了一个辅助函数。这些只是将日期表示格式化为由 OpenWeatherMap API 返回的数字。我知道有更好的方法来处理这个问题，但是我的小函数实现了我想要的。注意，OpenWeatherMap API 根据 UTC 返回时间，因此您需要根据您的时区在消息中适应这一点。

我还创建了一个助手函数，从特定于今天的每小时预报中检索小时周期。我原本打算将 OpenWeatherMapAPI 用于高温和低温，但是发现我必须订阅他们的付费服务。不幸的是，NOAA 的当前状况 API 也是不可靠的，因为一些气象站没有定期更新。因此，为了解决这个问题，我使用了(可靠的)NOAA 每小时预报终点，并分析了数据，以确定当天的预期高点和低点。

## 发送实际消息

现在我已经获得了天气预报，我需要把它作为短信发送出去。

我应该注意到我花了相当多的时间玩 AWS SNS 服务。我最初的想法是利用 SNS 主题向一个电话号码(和电子邮件)发送消息。我发现的问题是，AWS 对可以发布到电话号码的 SNS 主题消息的数量有限制。这是为了安全，并避免人们滥用短信功能。因为有一个极限，我想要一些我可以(潜在地)有无限能力使用的东西。所以我看着[朱莉奥](https://www.twilio.com/)。

Twilio 提供了一个很好的服务，通过他们的 API 支持语音和文本服务。开始使用它们非常简单。你只需创建一个免费试用账户，他们会给你一个号码开始使用。有了这个号码，您可以发送文本和语音信息。Twilio 有很棒的文档，我推荐[点击这里查看网站](https://www.twilio.com/docs/voice/voip-sdk/get-started)。

当你第一次安装 Twilio 时，你有一个试用帐户。如果您升级您的帐户，您可以有一个“现收现付”的访问权限。您也可以保留您在试用期获得的号码。这两个选项都相对便宜且易于设置。

对于这个项目的目的，我只需要使用短信功能。所以我最终只是做了以下事情:

```
let response = "lambda completed with ";

await client.messages
  .create({
    body: message,
    from: process.env.TWILIO_FROM,
    to: process.env.TWILIO_TO
  })
  .then(success => {
    console.log(success.sid);
    response = response + "success";
  })
  .catch(error => {
    console.log(error);
    response = response + " error";
  });

return response; 
```

Enter fullscreen mode Exit fullscreen mode

我包含了`response`对象，只是为了能够在测试 lambda 时捕获一些要返回的内容。

## 呼唤λ

在 AWS 中有很多方法可以触发 Lambda。我选择创建一个每天触发一次的 [CloudWatch](https://aws.amazon.com/cloudwatch/) 规则。使用 AWS 控制台很容易做到这一点。有其他方法可以做到这一点，但我选择在这里这样做，因为我有一个非常基本的用例。要使用 AWS CloudWatch [设置 cron 作业，请点击这里查看站点](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions)。

## 关闭思绪

因此，我的最终产品非常好，使我能够以短信的形式发送天气预报。在不到 200 行代码中，我就能把几种技术连接起来，提供我的天气预报。我强烈推荐查看 AWS Lambdas 和 Twilio 服务。我在这个项目中学到了一些，希望这篇博文(和我的项目)对你有所帮助。

欢迎发表评论并在 Twitter 上关注我，地址是@AndrewEvans0102 ！

(封面图片来源为 [pixabay](https://pixabay.com/photos/dawn-sun-mountain-landscape-sky-190055/) )