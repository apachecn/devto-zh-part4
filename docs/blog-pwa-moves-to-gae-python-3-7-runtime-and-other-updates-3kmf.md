# 博客-pwa 转向 GAE Python 3.7 运行时和其他更新

> 原文：<https://dev.to/justinribeiro/blog-pwa-moves-to-gae-python-3-7-runtime-and-other-updates-3kmf>

到目前为止，他已经是我相当长一段时间的忠实网站管理员了。我们已经看到前端发生了几次转变，从聚合物 2 到聚合物 3，最后是今天的 LitElement。没有得到太多关注的是为它提供动力的后端，谷歌应用引擎。

很久以来，我一直是 Python 2.7 on App Engine 的忠实粉丝。稳定明智的 GAE 从来没有打嗝，从来没有给过我轻微的头痛。也就是说，众所周知，Python 2.7 今年即将寿终正寝，我在做 2 到 3 的转换时有点落后。唉，再也没有了！

截至 [PR #64](https://github.com/justinribeiro/blog-pwa/pull/64) ，我已经将基础库和项目转换为使用 GAE 的 Python 3.7 运行时。这包括对旧的 http2push 库的一些额外工作，我对它进行了删减和修改，以处理几个月前我注意到的一个问题:在 Chrome 和 Firefox 中，预加载的资源在 fetch 层被复制。

注意，这实际上不是浏览器的问题。唉，前段时间修改 lib 失败，忘了给头定义加`crossorigin`。这个问题现在已经解决了，导致了更快的加载时间体验。

```
# Justin says: This is specific to the content path json file; it is # used by fetch() on the client, but we push the resource early so it # doesn't have to wait. Said resource must use as=fetch and have the # crossorigin set otherwise it'll dup in the browser json\_target = '%s%s' % (host, str(self.name)) preload\_links.append( '; rel=preload; as=fetch; crossorigin=anonymous;' % json\_target) for url, v in urls.items(): # Construct absolute URLs per spec. url = '%s%s' % (host, str(url)) t = str(v.get('type', '')) if len(t): # if you don't crossorigin these, they will not work preload\_links.append( '; rel=preload; as=%s; crossorigin=anonymous;' % (url, t)) else: preload\_links.append('; rel=preload' % url) 
```

说到这里，`app.yaml`已经被砍掉，以满足 Python 3.7 运行时的新定义。如果您没有看到这种变化，可能会感到震惊，但在很大程度上，这是非常相似的，也更容易处理。

最后，我对`index.html`中的一些加载库做了一些修改，特别是 web components polyfill loader 不再每次都加载。除非你现在用的是很旧的东西，否则在大多数情况下都不需要，所以现在它会在加载前检查。

我怀疑我会在今年接近尾声的时候做一些补充。我想修改汇总配置(老实说，它有点单薄)，并添加一些我已经在笔记本上使用了一段时间的附加功能。与此同时，一个小的 Python 3.7 谷歌应用引擎更新的快乐时光。