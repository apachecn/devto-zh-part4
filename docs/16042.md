# Angular 应用程序中的日期处理(第 1 部分— JSON 和 JavaScript 日期对象研究)

> 原文：<https://dev.to/imben1109/date-handling-in-angular-application-part-1-json-and-javascript-date-object-study-2p54>

[https://medium . com/self-learning/date-handling-in-angular-application-part-1-JSON-and-JavaScript-date-object-s-8f 77a 69734 c 7](https://medium.com/self-learning/date-handling-in-angular-application-part-1-json-and-javascript-date-object-s-8f77a69734c7)

# 简介

在现代 web 应用中，JSON 被广泛用作服务器端和客户端之间的通信格式。但是，JSON 没有指定日期对象的格式。

# 通用协议

ISO 8601(YYYY-MM-DDTHH:MM:ss . sssz)是日期对象交换格式的通用协议。

# JavaScript 日期对象

```
var now = new Date() 
```

以上是在浏览器环境下创建日期对象的方法。

## 取决于时区和区域设置

默认情况下，浏览器将使用时区和区域设置在浏览器中显示日期。即

```
var now = new Date();
console.log(now.toString()); 
```

**结果:**孙俊 2019 年 30 日 23 时 18 分 34 秒 GMT+0800(中国标准时间)

浏览器中日期对象的某些方法依赖于时区和位置。

```
var now = new Date();
now.getHours() 
```

**结果:** 23

## ISO 格式(独立于时区和区域设置)

如上所述，ISO 日期字符串格式是 JSON 格式中一种通用协议格式。

```
var now = new Date()
console.log(now.toISOString()); 
```

**结果:** 2019-06-30T15:55:46.936Z

# JSON 转换

将日期对象转换为 JSON

```
var jsonData = {date1: new Date()};
console.log(JSON.stringify(jsonData)); 
```

**结果:**{ " date 1 ":" 2019-06-30t 16:26:18.460 z " }

## 将 JSON 还原为 JavaScript 对象

```
var jsonData = {date1: new Date()};
var jsonDataStr = JSON.stringify(jsonData)
var revertedJsonData = JSON.parse(jsonDataStr);
console.log(revertedJsonData); 
```

**结果:**{日期 1: "2019-06-30T16:30:19.096Z"}

发现 JSON reversion (JSON.parse)不知道日期的类型。它不能将日期字符串转换为日期对象。

ISO 8601 是日期对象 json 字符串的约定格式。我们可以使用 JSON.parse 中的 reviver 函数来帮助转换。

```
var isoDateFormat = /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(?:\.\d*)?Z$/;
function parseIsoDateStrToDate(key, value){
  if (typeof value === "string" && isoDateFormat.test(value)){
    return new Date(value);
}
  return value
}
var jsonData = {date1: new Date()};
var jsonDataStr = JSON.stringify(jsonData)
var revertedJsonData = JSON.parse(jsonDataStr, parseIsoDateStrToDate);
console.log(revertedJsonData); 
```

**结果:**{日期 1:2019 年 7 月 1 日 01:00:04 GMT+0800(中国标准时间)}

# 汇总 JSON 日期和 JavaScript 日期对象

*   JavaScript 日期对象依赖于时区和区域设置
*   ISO 8601 日期格式是 JSON 日期表示的通用协议
*   JavaScript 不知道 JSON 日期类型

# 引用

*   [http://json.org/](http://json.org/)
*   [http://es5.github.io/#x15.9.2](http://es5.github.io/#x15.9.2)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/JSON/parse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)

**part 2:**[https://dev . to/imben 1109/date-handling-in-angular-application-part-2-angular-http-client-and-ngx-date picker-3fna](https://dev.to/imben1109/date-handling-in-angular-application-part-2-angular-http-client-and-ngx-datepicker-3fna)