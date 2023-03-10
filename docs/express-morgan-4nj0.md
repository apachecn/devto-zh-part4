# 联合 Express 的 morgan 和其他记录器

> 原文：<https://dev.to/shiena/express-morgan-4nj0>

# 前言

Node.js 的 Web 框架 Express 使用`express-generator`可以制作 APP 演示的雏形。
这个雏形含有 HTTP 请求记录器的 morgan，但是只能定制信息格式。
于是我试着和其他的记录器合作了一下。 在这里试着使用 winston。

# Connecting method

在 GitHub 的页面上，按如下所示用`stream`属性输出到文件中。

[expressjs/morgan #将日志写到一个文件](https://github.com/expressjs/morgan#write-logs-to-a-file)

```
// create a write stream (in append mode)
var accessLogStream = fs.createWriteStream(__dirname + '/access.log', {flags: 'a'})

// setup the logger
app.use(morgan('combined', {stream: accessLogStream})) 
```

追踪这个`stream`属性是如何使用的，如下所示调用了`write`函数。

[摩根/ index.js#L130](https://github.com/expressjs/morgan/blob/871726c3f0941fe80494ba510decd735f1659206/index.js#L130)

```
stream.write(line + '\n') 
```

也就是说，您应该可以通过以下方式与其他记录器协作:

```
app.use(require('morgan')({
    stream: {
        write: function(message) {
            // ここでmessageをログ出力すればよい！
        }
    }})
); 
```

# 与 winston 合作

与 winston 的自定义记录器协作时为以下代码。

```
// winstonでカスタムロガー作成
var winston = require('winston');
winston.emitErrs = true;

var logger = new winston.Logger({
    level: 'info',
    transports: [
        new (winston.transports.Console)({
            level: 'silly',
            handleExceptions: true,
            json: false,
            colorize: true
        }),
        new (winston.transports.File)({
            handleExceptions: true,
            json: false,
            colorize: false,
            filename: './logs/all-logs.log'
        })
    ]
});

// 連携用のwrite 関数
logger.stream = {
    write: function (message) {
        logger.info(message);
    }
};

// morganと連携
app.use(require('morgan')({stream: logger.stream}));

// アプリログはlogger.info()などで出力する 
```

# Reference

[stackoverflow # Node.js -记录/使用摩根和温斯顿](http://stackoverflow.com/questions/27906551/node-js-logging-use-morgan-and-winston)