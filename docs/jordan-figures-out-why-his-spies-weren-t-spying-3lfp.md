# 乔丹找出了为什么他的间谍不从事间谍活动

> 原文：<https://dev.to/aarmora/jordan-figures-out-why-his-spies-weren-t-spying-3lfp>

[此处示例代码](https://github.com/aarmora/jordan-does-dead-link-checking/tree/test-check)

上一篇文章我检查了链接检查器的所有代码，试图将它产品化。我希望它可以投入生产，包括单元测试。有些测试没有按照我的预期进行。这篇文章讲述了我在深入研究并让它们发挥作用时学到的一些东西。

## 将函数分离成自己的文件或模块

[![](img/1ce29107782981232df68f0a7d7d9b6b.png)](https://i.giphy.com/media/SD5Y8XtrthqsE/giphy.gif)

我遇到的最大问题是我的间谍。刺探的时候，你要先刺探模块，再刺探函数，像`sinon.spy(moduleName, 'function/methodName')`。我最初在同一个文件中有很多函数，这导致了一些问题。

比如我的`checkLinks()`函数调用`domainCheck()`。因为这两个函数在同一个文件中，而我需要一个模块，所以我简单地用`import * as findDeadLinksFunctions from './../findDeadLinks;`来获取一个模块，然后用`sinon.spy(findDeadLinksFunctions, 'domainCheck');`来探测。这个间谍永远不会被传唤。原因是因为它的行为几乎就像是在窥探不同的东西。

解决方案是将这个函数和许多其他函数导出到它们自己的文件中。我把不互相调用的函数放到一个`helpers.ts`文件中，然后像这样窥探:

```
import * as helpers from './../helpers';

...

    it('should call domainChecK()', async () => {
        const originalLinkObject: helpers.ILinkObject = {
            link: 'https://javascriptwebscrapingguy.com/jordan-takes-advantage-of-multithreaded-i-o-in-nodejs/',
            status: null,
            locationOfLink: 'https://javascriptwebscrapingguy.com'
        };
        const originalLinks = [];
        const domain = 'https://javascriptwebscrapingguy.com';
        const desiredIOThreads = 4;

        nock('https://javascriptwebscrapingguy.com').get('/jordan-takes-advantage-of-multithreaded-i-o-in-nodejs/').reply(200, '<button>click me</button>');

        domainCheckSpy = sinon.spy(helpers, 'domainCheck');

        await checkLinkFunction.checkLink(originalLinkObject, originalLinks, domain, desiredIOThreads);

        expect(domainCheckSpy.callCount).to.equal(1);

    }); 
```

应该注意的是，我仍然能够在实际的`checkLink()`函数中直接从`helpers.ts`导入域检查，如下所示。所以只要它在自己的模块中(或者在这种情况下作为模块的文件)，它就能很好地工作。

```
import { domainCheck, ILinkObject, getLinks } from './helpers';
...
    if (newDomain) {
        if (html && domainCheck(linkObject.link, domain, newDomain)) {
            newLinks = await getLinks(html, domain, linkObject.link, false);
        }
    } 
```

## 还原存根 vs 还原间谍

出于某种原因，我必须在一个`afterEach`中恢复我的存根。最初，我会这样做:

```
domainCheckSpy = sinon.spy(helpers, 'domainCheck');
getLinksStub = sinon.stub(helpers, 'getLinks');

// some test stuff

domainCheckSpy.restore();
getLinksStub.restore(); 
```

这对间谍来说很有用。如果我试图用一个存根做这件事，函数将永远不会恢复，无论在哪里使用`getLinks`,它都将返回 undefined，就像这个存根导致的那样。

如果我在一个`afterEach`内完成了，它就没有问题了。我最终在下面这样做了。我有条件，因为不是每个函数都使用 spy 或 stub。

```
describe('checkLink()', () => {
    let domainCheckStub;
    let domainCheckSpy;
    let getLinksSpy;
    let getLinksStub;
    let checkLinkSpy;

    afterEach(() => {
        if (domainCheckStub) {
            domainCheckStub.restore();
        }
        if (domainCheckSpy) {
            domainCheckSpy.restore();
        }
        if (getLinksSpy) {
            getLinksSpy.restore();
        }
        if (getLinksStub) {
            getLinksStub.restore();
        }
        if (checkLinkSpy) {
            checkLinkSpy.restore();
        }
    });
... 
```

## 测试递归函数

`checkLink()`自称。有时候很多。我希望有一种方法能够测试它是否经常调用自己。在我的测试中，我用`import * as checkLinkFunction from "../checkLink";`导入它，并将其命名为`promises.push(checkLink(linkToCheck, links, domain, desiredIOThreads));`。当我期望它调用自己三次，其中两次是递归调用时，它只调用了自己原来的时间。

这篇 stackoverflow 帖子非常有用。我只需要将函数作为它自己的模块导入，并以这种方式递归调用它，然后它就可以完美地工作了。

```
import * as checkLinkFunction from './checkLink';
...

            // Have to call the imported function so tests work: https://stackoverflow.com/a/51604652/2287595
            promises.push(checkLinkFunction.checkLink(linkToCheck, links, domain, desiredIOThreads)); 
```

## 设置测试发现大 bug

[![](img/46965bcfc6c47c55676c3d6ae8cf11d3.png)](https://i.giphy.com/media/IS9LfP9oSLdcY/giphy.gif)

这太棒了。我的代码中有一个我不知道会发生的大错误。代码看起来工作正常，我可能永远也不会发现这个错误。我使用的发现这个错误的测试是`findDeadLinks.spec.ts`中的这个。

```
it('should return the number of bad links (if one 404 and one 200, one bad link)', async () => {
        const returnLinks: helpers.ILinkObject[] = [
            { link: 'https://heyAnotherBuddy.com', status: null, locationOfLink: 'https://javascriptwebscrapingguy.com' },
            { link: 'https://heyBuddy.com', status: null, locationOfLink: 'https://javascriptwebscrapingguy.com' }
        ];
        getLinksStub = sinon.stub(helpers, 'getLinks').returns(Promise.resolve(returnLinks));

        nock(domainToSend).get('/').reply(200);
        nock("https://heyBuddy.com").get('/').reply(200);
        nock("https://heyAnotherBuddy.com").get('/').reply(400);

        const links = await findDeadLinks(domainToSend, desiredIOThreadsToSend);

        expect(links.length).to.equal(1);

    }); 
```

我的数组中有两个链接，我希望它像我在这里展示的那样返回。它返回的链接应该只有一个，因为我们只返回坏链接，只有一个状态为 400 的链接，但它却返回 0 个坏链接。

罪魁祸首在这里:

```
let linkToReplaceIndex = links.findIndex(linkObject => linkObject.link === linkObject.link);
    links[linkToReplaceIndex] = linkObject; 
```

看到问题了吗？我没有。很久没有了。我一直在弄这个，试图弄清楚到底发生了什么。如果你仔细看，你就能发现问题。`linkObject => linkObject.link === linkObject.link`。我在检查它自身，所以它每次都会在索引 0 处返回 true。总是由*替换索引为 0 的链接。*

就我而言，我的第一名是`heyAnotherBuddy.com`，第二名是`heyBuddy.com`。它将通过第一次迭代，并且运行良好。然后在第二次迭代中，它会用`heyBuddy.com`替换`heyAnotherBuddy.com`，其状态为 200。

让我很难找到的最大问题是`heyBuddy.com`的状态是如何更新的。它从未在索引 0 处，但不知何故它的状态得到了更新。我传递给我的`checkLink`函数的链接仍然引用了原始链接数组中的链接。更新它的状态会自动在链接数组中更新它。所以，我只是撕开了`linkToReplaceIndex`的一块，它的一切工作完美。

## 结论

我学到了更多关于测试的知识。我抓到一只大虫子。而且…我有一个非常不纯的函数。`checkLink`肯定会影响其功能之外的事物。我不喜欢这样。这是我需要更多考虑的事情，并找到更好的方法去做。

总的来说，美好的一天。做了很多好事。

帖子[乔丹指出为什么他的间谍没有从事间谍活动](https://javascriptwebscrapingguy.com/jordan-figures-out-why-his-spies-werent-spying/)首先出现在[的 JavaScript 网页抓取家伙](https://javascriptwebscrapingguy.com)上。