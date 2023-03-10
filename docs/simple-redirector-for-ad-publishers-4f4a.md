# 广告发布者的简单重定向器

> 原文：<https://dev.to/akhiljp_dev/simple-redirector-for-ad-publishers-4f4a>

## 前提

零售商发布和推送广告，广告由谈论他们产品的新闻文章和博客组成。

例如，uber eats 可以用福布斯的一篇文章制作一个广告，谈论 Uber Eats 有多棒。

## 问题

当广告内容本身是针对第三方网站时，零售商无法跟踪点击这些广告的用户。

例如，作为 Uber Eats 的所有者，我希望确保我知道点击这些广告的用户何时访问我的网站，这样我就可以为他们提供更好的价格或更好的报价。

## 解

DIY 方法(最便宜的选择):创建一个 CF worker，允许你在重定向的同时放入一个 cookie，这样你就可以在一个特定的用户访问你的网站时跟踪这个 cookie
付费解决方案(有点贵):有可能第三方工具提供了相同的解决方案，但是他们通常有很多其他可能不需要的服务。

### CloudFlare 工作者解决方案

工人解决方案的三个部分

a)在传入的 URL 中查找查询字符串参数。代码片段
归功于 [Emedara](https://community.cloudflare.com/u/emedara)

```
//function to handle the request
async function handleRequest(request) {
  const url = request.url
    // Function to parse query strings
    function getParameterByName(name) {
        name = name.replace(/[\[\]]/g, '\\$&')
        name = name.replace(/\//g, '')
        var regex = new RegExp('[?&]' + name + '(=([^&#]*)|&|#|$)'),
            results = regex.exec(url)
        if (!results) return null
        else if (!results[2]) return ''
        else if (results[2]) {
            results[2] = results[2].replace(/\//g, '')
        }
        return decodeURIComponent(results[2].replace(/\+/g, '  '));
    } 
```

b)找到您在广告 CTA url
中设置的最终目的地 url 和活动 cookie

```
// Using the function getParameterByName to extract finalurl
var finalURL = getParameterByName('finalurl')
console.log(finalURL)

//conditionally add a campaign cookie you wish to add to the url 
var utmcookie = getParameterByName('utm_camp')
var utmcookiedomain = 'campagincookie='+ utmcookie + '; Domain=redirect.akhil.workers.dev; Max-Age=40000'
console.log(utmcookie) 
```

c)使用 set-cookie 参数
发出到最终目的地的重定向

```
//issue the redirect 
return new Response('', {
  status: 302,
  headers: {
    Location: finalURL,
   'Set-Cookie':utmcookiedomain,
  }
 }) 
```

完整代码

```
 addEventListener('fetch', async event => {
  event.respondWith(handleRequest(event.request))
})

//function to handle the request
async function handleRequest(request) {
  const url = request.url
    // Function to parse query strings
    function getParameterByName(name) {
        name = name.replace(/[\[\]]/g, '\\$&')
        name = name.replace(/\//g, '')
        var regex = new RegExp('[?&]' + name + '(=([^&#]*)|&|#|$)'),
            results = regex.exec(url)
        if (!results) return null
        else if (!results[2]) return ''
        else if (results[2]) {
            results[2] = results[2].replace(/\//g, '')
        }
        return decodeURIComponent(results[2].replace(/\+/g, '  '));
    }

    // Using the function getParameterByName to extract finalurl
    var finalURL = getParameterByName('finalurl')
    console.log(finalURL)

//conditionally add a campaign cookie you wish to add to the url 
var utmcookie = getParameterByName('utm_camp')
var utmcookiedomain = 'campagincookie='+ utmcookie + '; Domain=*.akhil.workers.dev; Max-Age=40000'
console.log(utmcookie)
//issue the redirect 
  return new Response('', {
            status: 302,
            headers: {
            Location: finalURL,
            'Set-Cookie':utmcookiedomain,
      }
        })

} 
```

### 最终解决

`https://redirect.akhil.workers.dev?finalurl=<<Enter Final URL for the arctile>>&utm_camp=<<Enter the cookie you want to set>>`

示例链接:[https://redirect . akhil . workers . dev？final URL = https % 3A % 2F %2Ftimesofindia . India times . com % 2f 趋势跟踪% 2f 创业-启动-努力-细分-情绪-转向-电动% 2f itecshow % 2f 70416317 . CMS&UTM _ camp = akhil cookie](https://redirect.akhil.workers.dev?finalurl=https%3A%2F%2Ftimesofindia.indiatimes.com%2Ftrend-tracking%2Fev-startups-crank-up-efforts-as-segments-mood-turns-electric%2Farticleshow%2F70416317.cms&utm_camp=akhilcookie)