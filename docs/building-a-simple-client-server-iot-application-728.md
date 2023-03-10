# 构建简单的客户机/服务器 IOT 应用程序

> 原文：<https://dev.to/elasticrash/building-a-simple-client-server-iot-application-728>

不久前我遇到了一辆 MXChip。MXChip 类似于 arduino，但它预先构建了许多传感器，专门为云设计。

它的组件包括 Wifi，有机发光二极管显示器，耳机，麦克风，它还具有以下传感器，温度，湿度，运动和压力。

于是我就弄了一辆，拿去试驾了。我构建的第一个应用程序是一个简单的 http 客户机/服务器。

基本上，MXChip 将作为一个客户端，定期向服务器发送传感器读数(用 NodeJS 编写的 API)。

为了简单起见，我将使用 SQLite。所有内容都将存储在具有以下模式的单个表中。

```
create table TimeSeries
(
    id integer
    constraint TimeSeries_pk
    primary key autoincrement,
    temperature numeric,
    humidity numeric,
    date text,
    location text,
    timestamp numeric
); 
```

Enter fullscreen mode Exit fullscreen mode

NodeJS API 只不过是一个支持 POST 和 GET 选项的端点

*   POST:将记录添加到数据库
*   GET:检索两个时间戳之间的记录

在教程中，我倾向于使用尽可能少的依赖。这个只依赖于 sqlite3。

```
import * as http from 'http';
import * as sqlite3 from 'sqlite3';
import * as url from 'url';

const URLSearchParams = url.URLSearchParams;
const hostname = '0.0.0.0';
const sqlite = sqlite3.verbose();
const port = 3000;
var db = new sqlite.Database('./timedb.sqlite');

http.createServer((req: any, res: any) => {
    res.setHeader('Access-Control-Allow-Methods', 'OPTIONS, GET, POST');
    if (req.method === 'OPTIONS') {
        res.writeHead(200);
        res.end();
        return;
    }
    if (req.method === 'POST') {
        let body = '';
        req.on('data', (chunk: any) => {
            body += chunk.toString();
        });
        req.on('end', () => {
            try {
                db.serialize(() => {
                    const data = JSON.parse(body);
                    db.run("INSERT INTO TimeSeries (temperature, humidity, date, location, timestamp) VALUES (?, ? ,?, ?, ?)", [
                        data.temperature,
                        data.humidity,
                        new Date().toUTCString(),
                        data.location,
                        Date.now() / 1000 | 0
                    ]);
                });
            } catch (error) {
                console.log(error);
            }
            console.log(new Date().toUTCString());
            console.log(body);
            res.end('ok');
        });
    } else if (req.method === 'GET') {
        const search_params = new URLSearchParams(req.url.split('?')[1]);
        let from: any = search_params.get('from');
        let to: any = search_params.get('to');
        const now: Date = new Date as unknown as Date;
        if (to === null) {
            to = (now as unknown as number) / 1000 | 0;
        }
        if (from === null) {
            from = now.setHours(now.getHours() - 24) / 1000 | 0;
        }
        try {
            db.serialize(() => {
                db.all("SELECT * FROM TimeSeries WHERE timestamp > ? AND timestamp < ?", [from, to], (err, rows) => {
                    res.end(JSON.stringify(rows));
                });
            });
        } catch (error) {
            console.log(error);
        }
    }
}).listen(port, hostname, () => {
    console.log("server start at port 3000");
}); 
```

Enter fullscreen mode Exit fullscreen mode

说到 arduino，你可以用任何你喜欢的语言编写一个应用程序，只要它能被平台编译。我选择的语言是 C++。

对于那些不熟悉 arduino 开发的人来说，一个基本的文件结构有以下功能

*   设置:初始化和设置初始值的所有内容都在这里
*   循环:连续运行的功能，允许程序适应和响应。

我们的`include`和`global variables`是:

```
#include "AZ3166WiFi.h" #include "Arduino.h" #include "http_client.h" #include "Sensor.h" #include "SystemTickCounter.h" #include "RGB_LED.h"  
static char buffInfo[128]; // buffer for the screen
static RGB_LED rgbLed;  // our led 
static volatile uint64_t msReadEnvData = 0; // stores current tick of executed loop
#define READ_ENV_INTERVAL 120000 // how often loop will run properly static HTS221Sensor *ht_sensor; // sensors
static DevI2C *ext_i2c; // SPI
static bool hasWifi = false; // wifi on/off
static bool begin = false; // avoid race condition 
```

Enter fullscreen mode Exit fullscreen mode

我们的`setup()`将初始化以下内容

*   连续的
*   屏幕
*   温度和湿度传感器
*   无线局域网（wireless fidelity 的缩写）

```
void setup()
{
    Serial.begin(115200);
    Screen.init();
    initSensors();
    initWiFi();
} 
```

Enter fullscreen mode Exit fullscreen mode

初始化传感器，我们需要通过串行外设接口(DevI2C)
与它们通信

```
void initSensors()
{
    ext_i2c = new DevI2C(D14, D15);

    if (ext_i2c == NULL)
    {
        Screen.print(0, "Error \r\n ext_i2c");
    }

    // temperature and humidity
    ht_sensor = new HTS221Sensor(*ext_i2c);
    if (ht_sensor == NULL)
    {
        Screen.print(0, "Error \r\n ht_sensor");
    }

    ht_sensor->init(NULL);
    ht_sensor->reset();
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要连接 wifi。在 MXChip 上设置 wifi 非常简单，因为它会在首次设置设备时永久存储 SSID 和密码。所以我们需要的代码是最少的。

```
void initWiFi()
{
    if (WiFi.begin() == WL_CONNECTED)
    {
        IPAddress ip = WiFi.localIP();
        Screen.print(1, ip.get_address());
        hasWifi = true;
    }
    else
    {
        Screen.print(1, "No Wi-Fi");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们转移到我们的主函数`loop()`

```
void loop()
{
    if (hasWifi)
    {
        // get current tick
        uint64_t ms = SystemTickCounterRead() - msReadEnvData;
        if (!begin)
        {
            if (ms < READ_ENV_INTERVAL)
            {
                return;
            }
        }
        begin = true;

        // get readings
        float temperature = readTemperature();
        float humidity = readHumidity();

        // display the values, because its cool
        displayLines("Leicester", "Temp:" + String(temperature), "Hum: " + String(humidity));

        // update the tick to track loop full execution 
        msReadEnvData = SystemTickCounterRead();    

        // switch on rgb led while posting data (visual feedback)
        rgbLed.setColor(185, 24, 23);

        // POST sensor data
        sendData(temperature, humidity);

        // turn off rgb led
        rgbLed.turnOff();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

读取温度

```
float readTemperature()
{
    ht_sensor->reset();

    float temperature = 0;
    ht_sensor->getTemperature(&temperature);

    return temperature;
} 
```

Enter fullscreen mode Exit fullscreen mode

读取湿度

```
float readHumidity()
{
    ht_sensor->reset();

    float humidity = 0;
    ht_sensor->getHumidity(&humidity);

    return humidity;
} 
```

Enter fullscreen mode Exit fullscreen mode

一个方便的助手功能，一次打印到所有屏幕行(MXChip 有 3 个)

```
void displayLines(String line1, String line2, String line3)
{
    char screenBuff[128];
    line1.toCharArray(screenBuff, 128);
    Screen.print(0, screenBuff);

    line2.toCharArray(screenBuff, 128);
    Screen.print(1, screenBuff);

    line3.toCharArray(screenBuff, 128);
    Screen.print(2, screenBuff);
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要将我们的数据发布到 API(作为 JSON)

```
void sendData(float temp, float humidity)
{
    httpRequest(HTTP_POST, "http://192.168.1.128:3000/", "{\"location\":\"Earth\",\"humidity\":\"" + String(humidity) + "\",\"temperature\":\"" + String(temp) + "\"}");
} 
```

Enter fullscreen mode Exit fullscreen mode

Http_Request/Http_Response 函数

```
const Http_Response *httpRequest(http_method method, String url, String body)
{
    Screen.print(3, "Sending Data");

    char urlBuf[48];
    url.toCharArray(urlBuf, 48);

    HTTPClient *httpClient = new HTTPClient(method, urlBuf);
    httpClient->set_header("Content-Type", "application/json"); // required for posting data in the body

    char bodyBuf[256];
    body.toCharArray(bodyBuf, 256);
    const Http_Response *result = httpClient->send(bodyBuf, strlen(bodyBuf));

    if (result == NULL)
    {
        Screen.print(1, "Failed");
        char errorBuf[10];
        String(httpClient->get_error()).toCharArray(errorBuf, 10);
        Screen.print(1, errorBuf);
        return result;
    }

    Screen.print(3, "Success");

    String(result->body).toCharArray(buffInfo, 128);
    Screen.print(3, buffInfo);

    Serial.print(result->status_code);
    Serial.print(result->status_message);

    delete httpClient;

    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode