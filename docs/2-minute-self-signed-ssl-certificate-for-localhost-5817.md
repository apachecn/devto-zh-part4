# 本地主机的 2 分钟自签名 SSL 证书

> 原文：<https://dev.to/kauresss/2-minute-self-signed-ssl-certificate-for-localhost-5817>

这个周末，我的任务是使用 passport.js 的 facebook 策略构建一个登录系统。肯定会很容易...

问题是，脸书开发现在强制“HTTPS ”,所以你需要一个自签名的 SSL 证书本地主机:3000 或任何你正在使用的端口。

相当容易....(直到你进入生产模式，并意识到为什么你的宠物比你过得更快乐)😂🐶😹⌨️

在 Express v.4 中，bin 目录是您定义启动脚本的地方。“www”文件包含将 express 应用程序作为 web 服务器启动的代码。因此,‘app’作为一个处理程序传递给你的 web 服务器。如果使用带有“$ npm install express-generator -g”的快速生成器，则会为您创建一个带有 bin 目录和“www”文件的模板。

在您的终端:

```
//Note: you will be presented with lots of options. Be sure to fill the Common Name option as localhost

 $ openssl req -nodes -new -x509 -keyout server.key -out server.cert

//convert cert and key to PEM Files

 $ openssl x509 -in server.cert -out cert.pem -outform PEM
 $cat server.cert server.key > key.pem 
```

Enter fullscreen mode Exit fullscreen mode

复制项目目录中的 cert.pem 和 key.pem。然后在你的 bin 目录下的‘www’中，你将需要引用你的证书

```
/**
 * Module dependencies.
 */

const app = require('../app');
const debug = require('debug')('node-passport-social:server');
const http = require('https');
const fs = require("fs");

/**
 * Get port from environment and store in Express.
 */

const port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTPS server.
 */

const privateKey = fs.readFileSync('./key.pem');
const certificate = fs.readFileSync('./cert.pem');

const server = http.createServer({
  key: privateKey,
  cert: certificate
}, app);

/**
 * Listen on provided port, on all network interfaces.
 */

server.listen(port);
server.on('error', onError);
server.on('listening', onListening);

/**
 * Normalize a port into a number, string, or false.
 */

function normalizePort(val) {
  var port = parseInt(val, 10);

  if (isNaN(port)) {
    // named pipe
    return val;
  }

  if (port >= 0) {
    // port number
    return port;
  }

  return false;
}

/**
 * Event listener for HTTP server "error" event.
 */

function onError(error) {
  if (error.syscall !== 'listen') {
    throw error;
  }

 const bind = typeof port === 'string'
    ? 'Pipe ' + port
    : 'Port ' + port;

  // handle specific listen errors with friendly messages
  switch (error.code) {
    case 'EACCES':
      console.error(bind + ' requires elevated privileges');
      process.exit(1);
      break;
    case 'EADDRINUSE':
      console.error(bind + ' is already in use');
      process.exit(1);
      break;
    default:
      throw error;
  }
}

/**
 * Event listener for HTTP server "listening" event.
 */

function onListening() {
  const addr = server.address();
 const bind = typeof addr === 'string'
    ? 'pipe ' + addr
    : 'port ' + addr.port;
  debug('Listening on ' + bind);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在浏览器中打开 https:localhost:3000