# 在 Apache NiFi 中生成 JSON 数据

> 原文：<https://dev.to/tspannhw/generating-json-data-in-apache-nifi-4apo>

#### JSON 物联网生成器的例子

{

"ip": "${ip()} "，
"unique_id": "${UUID()} "，

"thread": "${thread()} "，

" hostname ":" $ { hostname()} "，

" sensor _ 9 ":$ { random():mod(100)}，

" sensor _ id ":$ { random():mod(30)}，

"sensor_3

#### 资源

*   [https://www . datainmotion . dev/2019/04/integration-testing-for-Apache-nifi . html](https://www.datainmotion.dev/2019/04/integration-testing-for-apache-nifi.html)
*   [https://medium . com/hashmapinc/its-here-an-Apache-nifi-simulator-for-generating-random-and-realistic-time-series-data-d 7 e 463 aa 5e 78](https://medium.com/hashmapinc/its-here-an-apache-nifi-simulator-for-generating-random-and-realistic-time-series-data-d7e463aa5e78)
*   [https://github.com/TouK/plumber](https://github.com/TouK/plumber)