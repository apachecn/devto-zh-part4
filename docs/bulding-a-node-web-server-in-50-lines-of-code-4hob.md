# ð ç¨ 50 è¡ä»£ç æå»ºäºä¸ä¸ªèç¹ web æå¡å¨

> åæï¼<https://dev.to/lucsan/bulding-a-node-web-server-in-50-lines-of-code-4hob>

SimpServ ç®åæå¡å¨æ¯ä¸ä¸ªï¼å å°é¢ç§¯å°ï¼èç¹ jsï¼web æå¡å¨ï¼å¨ä¸ä¸ª javascript çé¡µé¢ï¼è¿æ¯ä»¤äººå´å¥çã

è¯¥ä»£ç å¯å¨[https://github.com/lucsan/simpserv/blob/master/simpserv.js](https://github.com/lucsan/simpserv/blob/master/simpserv.js)è·å¾

æ¨å¯ä»¥å°æä»¶å¤å¶å°ð°ææçä½ç½®ï¼æèå¦æä½ åæ¬¢ï¼åæ´ä¸ª npm git çäºæð½åéæð´åå­ï¼æ https://github.com/lucsan/simpserv çð¥åºå­ã

å®çç®çæ¯æä¾ä¸ä¸ªð¨å¿«éåð©ç¨äº jsãhtml å css çèæ¬å°å¼åæå¡å¨ã

ä¸ºæ­¤ðæä»¬å°éè¦ä¸äºæ¥èªåºæ¬ nodejs åºçèç¹åã

```
const http = require('http')
const url = require('url')
const fs = require('fs') 
```

å æ­¤ä½ éè¦å¨ä½ çæºå¨ä¸å®è£ ***nodejs*** ãæä¼è®©ä½ äº«åé»ç¼çä¹è¶£ð±æä¹åã

æä»¶ä¸­æç®åçéç½®ï¼

```
const config = {
  port: '8080', // And any port you like.
  root: '', // If you want to server files from a different location to where you started node.
  autoload: true, // Auto loads a window in the default browser.
  index: 'index.html', // You need an index.html in your root.
} 
```

è¿ðå½ä½¿ç¨èç¹æ npm éè¿å½ä»¤è¡è°ç¨ ***start()*** æ¶,âå¼æâå¯å¨ã

##### ðä¾å¦:

`node simpserv.js`ï¼æè(å¦æä½ æ package.json) `npm run serv`

å¯å¨åè½éå¸¸ç®åãæå¡å¨(`http.createServer().listen()`)è¢«åå»ºå¹¶è¢«åç¥ä¸æ³¨å°çå¬æµè§å¨ã

å°`onRequest()`å½æ°è½»è½»å°æ³¨å¥å°`createServer`ä¸­ï¼ä½¿`request.on`äºä»¶å¤çç¨åºå¯ç¨ï¼å®æ¥åå°ç­å¾ä»å®åæ¬¢çæµè§å¨æ¥æ¶è¾å¥(å³:ä¸ä¸ª uri)ã

```
function start () {
  http.createServer(onRequest).listen(config.port)
  console.log('SimpServ running')
  function onRequest(request, response) {
    request.on('error', function(err){ console.log('err ', err) })
    router(request, response)
  }
} 
```

æ¯æ¬¡ ***request.on*** äºä»¶æ¥æ¶å°ä¸ä¸ªæ°ç uri æ¶ï¼å®ä¼æ¥åå°å°å®ä¼ éç»è·¯ç±å¨å½æ°ã

è·¯ç±å¨åè½è¶çº§å¼ºå¤§ï¼å¯ä»¥å¤çææå¶ä»äºæãå¨ä¸ä¸ªæ´å¤§çé¡¹ç®ä¸­ï¼è¿å¯è½ä¼ä½ä¸ºä¸ä¸ªæ¨¡ååå«å¨åï¼æ è®ºå¦ä½ï¼è¿å°±æ¯...

```
const router = (request, response) => {
  let filename = url.parse(request.url).pathname
  filename = filename.replace('/', '')
  if (filename == '') filename = config.index
  let path = (config.root == '' ? filename : `${config.root}/${filename}`)
  let bits = path.split('.')
  let ext = bits[bits.length - 1]
  let ct = 'text/html;'
  if (ext == 'css') ct = 'text/css;'
  if (ext == 'js') ct = 'text/javascript;'

  fs.readFile(path,  (err, page) => {
    if (err) console.log(err)
    response.writeHead(200, { 'Content-Type': `${ct}charset=utf-8` })
    if (page) {
      response.write(page)
    } else {
      response.write('Error')
    }
    response.end()
  })
} 
```

å¨åå è¡ä¸­ï¼æä»¬ä»æ ¹ uri ä¸­æåè·¯å¾çæ¬è´¨ï¼

å³:root = / or ' 'ï¼ ***/*** ä¹åçä»»ä½ä¸è¥¿é½è¢«è®¤ä¸ºæ¯è·¯å¾ã

ä¾å¦:
[https://localhost/my project/](https://localhost/myproject/)=/æèâ

[https://localhost/my project/index . html](https://localhost/myproject/index.html)= index.html

[https://localhost/my project/some/place](https://localhost/myproject/some/place)= some/place

å¦æè·¯å¾ä¸ºç©ºï¼æä»¬å°ä½¿ç¨ index.html ä½ä¸ºé»è®¤æä»¶ï¼ä½æ¯æ¨å¯ä»¥å°å®è®¾ç½®ä¸ºæ¨åæ¬¢çä»»ä½æä»¶ã

æ¥ä¸æ¥ï¼æä»¬æ£æ¥æ©å±ç±»åçè·¯å¾ãå¨è¿ç§æåµä¸ï¼æä»¬åªå³å¿ css å jsï¼è¿ä¸ç¹ä¹ä¸å³å¿æä»¬ï¼ä½æ¯ï¼å¶ä»æ©å±ä¹å¯ä»¥èè(ä¾å¦:php)ã
å°æéç MIME ç±»åæå¥ ***ct***

æå **readfile** è¢«è°ç¨ï¼ä¼ å¥è·¯å¾ï¼å®æå¡äºè¢«å¼ç¨çæä»¶(åºè¯¥å¨ï¼*æç¸å¯¹*ï¼å°è¿è¡æä»¶å¤¹çæ ¹æ*èç¹)ï¼å¦æå®è½æ¾å°å®ï¼å¦åï¼æ¥éãð¤ä¸è®¡ç®ã*

è¿æ®µä»£ç :

```
if (process.platform == 'win32') {
  require('child_process')
    .exec(`start http://localhost:${config.port}`)
} 
```

å¨é»è®¤æµè§å¨ä¸­æå¼ä¸ä¸ªé¡µé¢ï¼æå **/** æ **/index.htm** ï¼å®åªå¨ Windows ä¸å·¥ä½ï¼ä½ä½ å¯ä»¥å¨å¶ä»æä½ç³»ç»ä¸æ¾å°ç­æçãè¿æ¯ä¸ä¸ªå¾å¥½çå°è¯ï¼è½ç¶ä»ææ¯ä¸æ¥è¯´å¹¶ä¸æ¯ç®åæå¡å¨çä¸é¨åã

æ­£å¦ä½ æçå°çï¼ä½ å¯ä»¥å¾å®¹æå°å¼å§ä¿®æ¹è¿äºä»£ç ï¼ä»¥è¾¾å°åç§ç®ç(phpï¼data endpoint)ï¼æèä½¿å¶æ´å¨é¢(åå¤æ)ï¼æèä½¿å¶éåºç¹å®çç®ç(å°±åðillustrated ç°æçä»£ç ä¸æ ·)ã

ä¸é¢æ¯ååº API ç«¯ç¹çä»£ç çæ®µ:

```
if (path.indexOf('/customer') > -1) {
  if (path.indexOf('/name') > -1) filepath = '/customerName.html'
  if (path.indexOf('/address') > -1) filepath = '/customerAddress.html'
} 
```

æ­¤å¤æå¥:

```
if (ext == 'js') ct = 'text/javascript;'
  // --- Insert filepath adapters here --- //
fs.readFile(path,  (err, page) => { ... 
```

ç°å¨ï¼å½æ¨è®¿é® **/customer/name** æ¶ï¼ä¼è¿åæä»¶ customerName.htmlï¼ä½è¿ä¹å¯è½æ¯ä¸äº json æ°æ®ï¼æè**æä»¶è·¯å¾**å¯ä»¥è§£æä¸ºä»å½æ°è¿åç htmlã

```
 filepath = myDataHtml('getSome') 
```

ðâSurfs èµ·æ¥äºï¼ä¼è®¡ä»¬ð