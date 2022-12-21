# IP 地理定位和 IP 位置查找 API

> 原文：<https://dev.to/ipwhoisio/ip-geolocation-and-ip-location-lookup-api-oj0>

IP 地理定位服务对测试和小型应用程序和网站是免费的。

**开发**
由于位于不同大洲的强大且优化的服务器和高效的数据库，在世界大部分地区实现高达 90 毫秒的真实响应。

**PHP 库**
T3】转到 Github 项目

**PHP 函数**

```
$ip = "CLIENT_IP_ADDRESS";
$location = get_ipwhois($ip);
$decodedLocation = json_decode($location, true);

echo "<pre>";
print_r($decodedLocation);
echo "</pre>";

function get_ipwhois($ip, $format = "json", $lang = "en") {
    $url = "http://free.ipwhois.io/".$format."/".$ip."?lang=".$lang;
    $cURL = curl_init();

    curl_setopt($cURL, CURLOPT_URL, $url);
    curl_setopt($cURL, CURLOPT_HTTPGET, true);
    curl_setopt($cURL, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($cURL, CURLOPT_HTTPHEADER, array(
        'Content-Type: application/json',
        'Accept: application/json'
    ));
    return curl_exec($cURL);
} 
```

Enter fullscreen mode Exit fullscreen mode