# 乔丹在网站上搜寻关键词

> 原文：<https://dev.to/aarmora/jordan-scrapes-websites-for-keywords-550h>

## Axios

[![](img/62deb417ba1e1b1c6d3d6c6c96434188.png)](https://i.giphy.com/media/l0MYwrMA90bl6CfyU/giphy.gif)

好吧，好吧。 [Axios](https://github.com/axios/axios) 还不错。在使用[请求](https://github.com/request/request)和[请求-承诺](https://github.com/request/request-promise)时，我一直很固执。老实说，他们很棒。我已经习惯了它所能做的，而且它一直都很稳定。

然而，我一直从事的[链接检查](https://javascriptwebscrapingguy.com/category/link-checking/)工作使得从被抓取的网站获得准确的回复变得非常重要。当我浏览数千页时，我得到了很多假阴性。当使用 request-promise 时，页面会返回 403 或者只给我一个`ECONNREFUSED`,但是当我在浏览器中检查页面时，它们会正常工作。

我正在写另一篇关于这个问题的更详细的文章，但是现在我可以肯定地说，Axios 完成的成功请求比请求-承诺要多得多。我将进一步挖掘，因为我必须想象同样的工作正在幕后进行，也许我只是在请求-承诺中有某种配置错误。

## 三个功能的故事

[![](img/1882e4727592050a01baaf80461e7867.png)](https://i.giphy.com/media/26AHIbtfGWCi2Q2C4/giphy.gif)

### 异步函数 getLinks

```
export async function getLinks(html: any, originalDomain: string, links: any[]) {
    const $ = cheerio.load(html);

    $('a').each((index, element) => {
        let link = $(element).attr('href');
        if (link && (!link.includes('javascript:') && !link.includes('tel:') && !link.includes('mailto:'))) {
            // Sometimes the first character of the link isn't the domain and has a slash. Let's clean it up
            if (link.charAt(0) === '/') {
                // This is within our original domain, so we are good
                link = link.slice(1)
            }
            // our original domain isn't in this link, skip it
            else if (!link.includes(originalDomain)) {
                return true;
            }

            let linkToPush = link.includes('http') ? link : `${originalDomain}/${link}`;
            linkToPush = linkToPush.split('?')[0];

            // We're going to skip #comment and #respond since it's not really a link
            if (!linkToPush.includes('#comment') && !linkToPush.includes('#respond') 
                && !linkToPush.includes('.PNG')
                && !linkToPush.includes('.png') 
                && !linkToPush.includes('.jpg')
                && !linkToPush.includes('.jpeg')
                && links.indexOf(linkToPush) === -1) {
                links.push(linkToPush);
            }
        }
    });

    return links;

} 
```

这个函数与来自[链接检查器](https://github.com/aarmora/jordan-does-dead-link-checking/blob/06dc3c56dd1e0dd288e6abca9481333e87480cf8/src/helpers.ts#L18)的函数非常相似。这个想法是，它接受任何 html 并寻找新的链接，以便浏览整个域。

在链接检查器中，我检查了在目标域中找到的每个链接的状态，不管它是否指向另一个域。在这个项目中，我想针对特定的领域，所以没有做任何指向另一个领域的链接。

我没有对包含常见图像标签的 URL 进行任何检查，比如`.png`或`.jpg`。它们不会包含任何有用的关键字，所以我节省了时间，跳过了它们。

### 功能检查关键字

```
async function checkKeywords(html: string, keywords: string[], pagesWithKeywords: string[], currentUrl: string) {
    if (new RegExp(keywords.join("|")).test(html)) {
        console.log('found a potential here', currentUrl);
        pagesWithKeywords.push(currentUrl);
    }
} 
```

超级简单。我接受一组关键字和 html。我只是做了一个简单的正则表达式测试，如果在页面上找到了任何一个，我就把 currentUrl 放入一个数组中。

可能值得注意的是，这根本不是很好的函数式编程。这些功能绝对不纯。我不喜欢这一点，也许我会在未来对此进行更多的调整。

### 异步函数 getEmailAddresses

```
export async function getEmailAddresses(html: any, emails: string[] = []) {
    const regex = /([a-zA-Z0-9._-]+@[a-zA-Z0-9._-]+\.[a-zA-Z0-9._-]+)/gi;

    const emailsToTest: string[] = html.match(regex);
    if (emailsToTest) {
        for (let i = 0; i + 1 < emailsToTest.length; i++) {
            const testTerms = ['.jpg', '.jpeg', '.png', '.svg', '.img', '.gif', '@example', '@email'];
            if (!testTerms.some(term => emailsToTest[i].toLowerCase().includes(term)) && emails.indexOf(emailsToTest[i]) === -1) {
                emails.push(emailsToTest[i]);
            }
        }
    }
    return Promise.resolve();
} 
```

想法同上。我有一个普通电子邮件地址格式的正则表达式，我测试了它的 html。我还做了检查，试图确保我没有重复的电子邮件地址。

### 异步函数 getEverything

```
async function getEverything(html: any, originalDomain: string, currentUrl: string, keywords: string[], emails: string[], pagesWithKeywords: string[]) {
    console.log('checking:', currentUrl);
    checkKeywords(html, keywords, pagesWithKeywords, currentUrl);
    await getEmailAddresses(html, emails);

    if (pagesWithKeywords.length > 0) {
        return Promise.resolve();
    }
    else {
        let newLinks: any[] = [];
        const newDomain = new URL(currentUrl).origin;
        if (domainCheck(currentUrl, originalDomain, newDomain)) {
            newLinks = await getLinks(html, originalDomain, newLinks)
        }
        // Let's cap how deep we go to 100 additional checks
        for (let i = 0; i < 100; i++) {
            if (pagesWithKeywords.length > 0) {
                return Promise.resolve();
            }

            if (newLinks[i]) {
                console.log('checking new link:', newLinks[i]);
                try {
                    // TODO: Can't this be done recursively?
                    const response = await axios(newLinks[i]);
                    checkKeywords(response.data, keywords, pagesWithKeywords, currentUrl);
                    await getEmailAddresses(html, emails);
                }
                catch (e) {
                    console.log('could not get new link', newLinks[i] );
                }
            }
        }
    }

    return Promise.reject('No potential found.');
} 
```

这个函数将所有这些联系在一起。这个函数有几个值得注意的地方。第一个是检查，如果我已经找到了一个带有关键字的页面，让我们检查完这个域。我只需要看到如果域包含关键字一次，然后我知道他们是一个可行的线索。

第二，虽然我确实从`getLinks`那里得到了一些新链接，但我把我检查的链接数量限制在任意 100 个。我想我可能会做出这样的假设，如果我在 100 页中没有找到我要找的关键词，它可能就没有。它也是一个时间保护器。活跃的网站可以很容易地拥有成千上万的页面，我不想花时间去浏览所有这些。

## 见不得人的部分

[![](img/b7c9930c1ef5a1b19bc060cf69c9ec5a.png)](https://i.giphy.com/media/l2QZWIUgY9aVlaDza/giphy.gif)

这是我很快完成的一个剧本，它肯定需要更多的润色。我最不喜欢的是…为什么我不递归地调用`getEverything`。我真的需要启动这一切的父函数来管理它被调用的次数。或者`getEverything`可能是父函数，但这意味着我需要另一个函数来保存其余的函数。

敬请关注。我可能会尝试改进这一点。

帖子[乔丹抓取网站关键词](https://javascriptwebscrapingguy.com/jordan-scrapes-websites-for-keywords/)最早出现在 [JavaScript 网页抓取家伙](https://javascriptwebscrapingguy.com)上。