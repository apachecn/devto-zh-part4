# 如何用普通的 JavaScript 在浏览器中编写链接检查器

> 原文：<https://dev.to/eviltester/how-to-write-a-link-checker-in-the-browser-with-vanilla-javascript-4n69>

*TLDR；从代码片段或控制台运行一个简单的链接检查器有一些次要的优势，比如跳转到链接并显示 CSP 和 CORB 错误*

* * *

我一直在尝试更多的 JavaScript，并且更多地在控制台上工作。

在 LTG 聚会的安全研讨会上，看到 Santhosh Tuppad 在控制台上使用大量 JavaScript 真是太棒了。我认为他鼓励每个人创建一个本地解压缩的特设 chrome 扩展是一个好主意。

我创建了一个教程，展示如何在 YouTube 上创建一个 Chrome 扩展

*   [https://www.youtube.com/watch?v=Olz4wo-ILwI](https://www.youtube.com/watch?v=Olz4wo-ILwI)

我在 github 上有几个扩展的例子

*   一个是我和 https://github.com/eviltester/usefuljssnippetextension 一起建造的
*   我用它来做自己的临时实验[https://github.com/eviltester/javascriptbotshowcase](https://github.com/eviltester/javascriptbotshowcase)

使用 Chrome 扩展可以让你访问更多的 API，避免一些跨站点脚本的限制。

## 外部链接检查器

我使用链接检查器，比如:

*   [总验证器](https://www.totalvalidator.com/)

从外部抓取我的网站的错误。

外部爬虫对于发现页面的状态是重要的，例如 404、200

## 构建链接检查器

作为一个快速实验，我想看看我能用 JavaScript 构建多少链接检查器，并从片段中运行它。

我已经上传了所有的代码作为主旨:

*   [https://gist . github . com/evil tester/8 a 27 ca 23 f 7475d 6 b 47 fc 99 fc 11 ad 3198](https://gist.github.com/eviltester/8a27ca23f7475d6b47fc99fc11ad3198)

## 查找所有链接

本质上，我所做的是:

*   找到所有链接
*   迭代它们

看起来像这样:

```
var links = document.querySelectorAll("a"); 
var linkReport = [];
links.forEach(function(link){
    var reportLine = {url: link.getAttribute('href'), status:0, message : "", element : link};
    linkReport.push(reportLine);
    // do stuff to the reportLine and link here
});
console.table(linkReport); 
```

您可以从控制台运行它，或者将其添加为代码片段。

当它完成时，它使用 [console.table](https://developer.mozilla.org/en-US/docs/Web/API/Console/table) 功能来输出所有对象。

对象是在以下行中创建的:

```
var reportLine = {url: link.getAttribute('href'), status:0, message : "", element : link}; 
```

在这种形式下，它实际上什么也不做，但是...

...如果表格中的任何内容引起了您的注意，比如说表格中的 link 70，那么您可以在控制台中...

将其滚动到视图中:

```
linkReport[70].element.scrollIntoView() 
```

并在屏幕上突出显示:

```
linkReport[70].element.style.backgroundColor = "red" 
```

所以这种简单的形式可能会有用。

## 检查链接

我想通过提出 HEAD 请求来检查链接。

我知道这并不适用于所有的链接，因为浏览器会因为跨站点脚本问题而阻止一些请求。

像[检查我的链接](https://chrome.google.com/webstore/detail/check-my-links/ojkcdipcgfaekbeaelaapakgnjflfglf)这样的扩展将使用 Chrome APIs 来避免 XSS 问题。

但是我不顾一切地继续下去，看看是否会发生什么有趣的事情。

我最初使用 XMLHttpRequests:

```
 var http = new XMLHttpRequest();
        http.open('HEAD', reportLine.url);

        http.onreadystatechange = (function(line,xhttp) {
            return function(){
                if (xhttp.readyState == xhttp.DONE) {
                    line.status = xhttp.status;
                    line.message = xhttp.responseText + xhttp.statusText;
                    linksChecked++;
                    console.table(xhttp);
                }
            }
        })(reportLine, http);
        http.send(); 
```

该控制台记录工作时的 http 请求。

因为这是基于回调的，如果我在循环后输出表，它不会有所有的请求状态，所以我维护了一个链接计数检查`linksChecked++;`并在循环后添加了一个轮询机制:

```
var finishReport = setInterval(
                        function(){
                              if(linksChecked>=linkReport.length){
                                  console.table(linkReport);
                                  clearInterval(finishReport);
                                  }
                               }
                        , 3000); 
```

这样，只有当检查的链路数量与阵列中的链路数量匹配时，才会显示最终的 console.table 报告。

## 使用 XMLHttpRequest 的简单链接检查器

给我一个简单的链接检查器，就像这样:

```
var links = document.querySelectorAll("a");
var linkReport = [];
var linksChecked=0;
links.forEach(function(link){
    var http = new XMLHttpRequest();
    var reportLine = {url: link.getAttribute('href'), status:0, message : "", element : link};

        http.open('HEAD', reportLine.url);
        linkReport.push(reportLine);

        http.onreadystatechange = (function(line,xhttp) {
            return function(){
                if (xhttp.readyState == xhttp.DONE) {
                    line.status = xhttp.status;
                    linksChecked++;
                    line.message = xhttp.responseText + xhttp.statusText;
                    console.table(xhttp);
                }
            }
        })(reportLine, http);
        http.send();
});
var finishReport = setInterval(
                        function(){
                              if(linksChecked>=linkReport.length){
                                  console.table(linkReport);
                                  clearInterval(finishReport);
                                  }
                               }
                        , 3000); 
```

同样，我可以滚动到链接并使其可见。

我在检查我的链接时遇到的一个问题是，当链接失败时，有时很难在屏幕上找到它。这样我可以在控制台中使用 JavaScript 跳转到它。

## 使用 Fetch

我想用 Fetch 试试，看看输出有什么不同:

```
 fetch(reportLine.url, {
      method: 'HEAD'
    })
    .then(function(response) {
        linksChecked++;
        reportLine.status=response.status;
        reportLine.message= response.statusText + " | " +
                            response.type + " | " + 
                            (response.message || "") + " | " +
                            (response.redirected ? "redirected | " : "") +
                            JSON.stringify(response.headers) ;
        console.table(response);
        }
    )
    .catch(function(error){
        reportLine.message = error;
        console.table(error);
        linksChecked++;
    }); 
```

这更容易使用，响应包含更多有用的信息，所以我粗略地将我感兴趣的响应字段连接到报告行的 message 属性中。

## 错误

当链接检查器运行时，它在控制台中显示所有 CSP 错误:

> `VM14:1 Refused to connect to 'https://help.github.com/'
> because it violates the document's Content Security Policy.`

以及所有 CORB 的错误:

> `Cross-Origin Read Blocking (CORB) blocked cross-origin response
> https://gist.githubusercontent.com/eviltester with MIME type
> text/plain. See https://www.chromestatus.com/feature/5629709824032768
> for more details.`

这是一个有益的副作用。表格报告显示我的状态为 0，但是我可以在控制台中查看其他错误。

这是一个有用的副作用，因为我在外部链接检查器中看不到这些警告，但能够检查各种 XSS 策略是否到位，或者是否已经针对某些服务器适当地故意放宽，这一点很重要。

我不认为我有任何其他工具可以轻松地为我提供这些信息。

## 我可以检查这些的状态吗？

为了尝试添加更多的信息，我想看看能不能在初始日志中检查任何抛出错误的状态。

所以我用了一个在 Santhosh 工作室学到的快速破解方法。

图像标签通常用于 XSS 向另一个站点传递信息，但我想看看它是否能给我任何状态信息。

```
function imgreport(links){    
    links.forEach(function(link){
            if(link.status==0){
                // trigger error messages with status 
                // to the console for status of 0
                var img = new Image();
                img.src = link.url;
            }
        }
    );
} 
```

上面的函数创建了一个新的图像，并将 url 设置为一个无法获取的链接。

这会提供更多信息吗？

确实如此。

通过捡回来的东西，我了解到:

> 从起点'[https://www.eviltester.com](https://www.eviltester.com)'到'[https://twitter.com/eviltester](https://twitter.com/eviltester)'
> 的“获取访问”已被 CORS 策略
> 阻止:请求的资源上不存在“访问控制允许起点”
> 标头。
> 如果不透明响应满足您的需求，
> 将请求的模式设置为‘no-cors ’,以在禁用 CORS 的情况下获取
> 资源。

对于与我所学图片相同的 URL:

> `GET https://twitter.com/eviltester 403`

## fetch 还能做什么？

我看了一下 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) 文档，发现它可以为我跟踪重定向:

```
 fetch(reportLine.url, {
      method: 'HEAD',
      mode: 'cors',
      redirect: 'follow'
    }) 
```

所以我在报告中添加了它被重定向到的 url

```
 if(response.redirected){

            reportLine.redirectedTo = response.url;

        } 
```

## 
  
我的最终代号

我的 linkchecker 的最终代码使用了“fetch”版本，因为它有更多可操作的有用信息。

```
var links = document.querySelectorAll("a");

var linkReport = [];

var linksChecked=0;

links.forEach(function(link){

```
var reportLine = {url: link.getAttribute('href'), status:0, redirectedTo: "", message : "", element : link};
linkReport.push(reportLine);

console.log("HEAD " + reportLine.url);

fetch(reportLine.url, {
  method: 'HEAD',
  mode: 'cors',
  //mode: 'no-cors',
  redirect: 'follow'
})
.then(function(response) {
    linksChecked++;
    reportLine.status=response.status;
    reportLine.message= response.statusText + " | " + 
                        response.type + " | " + 
                        (response.message || "") + " | " +                            
                        JSON.stringify(response.headers) ;
    if(response.redirected){
        reportLine.redirectedTo = response.url;
    }
    console.table(response);
    }
)
.catch(function(error){
    reportLine.message = error;
    console.table(error);
    linksChecked++;
}); 
```

});

function img report(links){

links . foreach(function(link){
if(link . status = = 0){
//触发状态为
 //的错误消息到控制台状态为 0
var img = new Image()；
img . src = link . URL；
}
}
)；
 }

var finish report = setInterval(
function(){ if(links checked>= link report . length){
console . table(link report)；
img report(link report)；
clear interval(finish report)；
 }} 
，3000)； 
```

## 
  
不是日常链接检查器

我发现这是一个有用的练习。

链接检查器报告对我很有用，因为它确实揭示了页面上的问题，这些问题在 Chrome 中由图标暗示，但在获取错误消息中非常明显。

使用 console.table 允许我在控制台中对“报告”进行排序，以使调查有用，并且我学到了更多关于 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch) 的知识

从这个[要点](https://gist.github.com/eviltester/8a27ca23f7475d6b47fc99fc11ad3198)开始，所有的代码都很容易复制和粘贴来进行实验

* * *

如果你想学习更多的 JavaScript，那么:

*   我在 [TestAutomationU](https://testautomationu.applitools.com/automating-in-the-browser-using-javascript/) 上有一门关于浏览器自动化的课程
*   我在这个在线课程中讲述了[技术网络测试](https://www.eviltester.com/page/onlinetraining/techwebtesting101/)
*   我在这个 [Patreon 微课程](https://www.eviltester.com/page/patreon/#microcourses)中介绍了游戏黑客