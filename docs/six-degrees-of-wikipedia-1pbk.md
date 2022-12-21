# 维基百科六度

> 原文：<https://dev.to/alebian/six-degrees-of-wikipedia-1pbk>

几年前，我读过一篇关于脸书用户分离程度的有趣文章。不管文章中的结果如何，我认为找到其他事物之间的分离是令人兴奋的，比如维基百科的文章。

对于这个问题，我们将接收 2 篇有效的维基百科文章，并返回一个连接这两篇文章的最小文章列表。如果两个文章中的一个链接到另一个，则这两个文章是连接的(不需要反过来)。

让我们把问题分成几个部分，分别解决:

*   从维基百科获取一篇文章。
*   获取一篇文章到其他文章的所有链接。
*   找出两篇文章之间的最短路径。

对于前两个，我们需要使用 HTTP 获取一篇文章，然后找到所有引用其他文章的`a`标签。检查 HTML 我们可以看到，每篇文章都有我们想跳过的链接(如主页和外部链接):

```
module Services
  class Wikipedia
    BASE_URL = 'https://en.wikipedia.org'.freeze

    class << self
      def article_links(article)
        article = get_article(article)

        article.css('a').each_with_object([]) do |link, array|
          href = link['href']
          array << href if internal?(href) && !array.include?(href)
          array
        end
      end

      private

      def get_article(article)
        uri = URI.parse("#{BASE_URL}#{article}")
        Nokogiri::HTML(uri.read)
      end

      def internal?(link)
        link =~ /^\/wiki\/*/ && !link.include?(':') && link != '/wiki/Main_Page'
      end
    end
  end
end 
```

我使用了 [Nokogiri](https://nokogiri.org/) gem 来解析 HTML 并轻松搜索`a`标签。

玩代码的时候，我发现如果有某种缓存就好了，这样我们就不会丢失从维基百科获取的数据。我用 Redis 做这个:

```
module Repositories
  class Redis
    REDIS_URL = 'redis://localhost:6379'.freeze

    def initialize
      @connection = ::Redis.new(url: REDIS_URL)
    end

    def get_links(path)
      return Oj.load(@connection.get(path)) if @connection.exists(path)

      links = Services::Wikipedia.article_links(path)
      @connection.set(path, links.to_json)

      links
    end
  end
end 
```

任务的最后一部分是实际的算法。在这种情况下，我们可以将文章视为图的节点，将链接视为顶点，形成一个有向图。由于访问每个节点的成本相同(一个 HTTP 请求)，我们可以假设这是一个未加权的图，或者是一个加权的图，每条边的成本都相同。

为了构建这个图，我将使用一个名为 [RGL](https://github.com/monora/rgl) 的 gem，它也给了我们一个搜索算法，所以我们不必实现它。因为我们没有图表，所以我们必须聪明地创建它。使用 DFS(深度优先搜索)遍历链接可能会导致我们创建一个带有不必要数量节点的图，所以我认为最好使用 BFS(广度优先搜索)来构建它:

```
module Crawlers
  class Graph < Base
    def call
      graph = RGL::DirectedAdjacencyGraph.new

      queue = []
      queue.push(@from_path)

      while (current = queue.shift) != nil
        links = @repository.get_links(current)

        links.each do |link|
          unless graph.has_vertex?(link)
            graph.add_edge(current, link)
            queue.push(link)
          end
        end

        if links.include?(@to_path)
          return graph.dijkstra_shortest_path(EdgeWeightHack.new, @from_path, @to_path)
        end
      end
    end

    class EdgeWeightHack
      # The dijkstra_shortest_path expects a hash
      def 
        1
      end
    end
  end
end 
```

正如您所看到的，我使用了一个队列来以 BFS 的方式添加节点，如果图中已经有了链接，就跳过它们。我发现 RGL gem 没有针对未加权图的搜索算法，但它有针对加权图的 Dijkstra 算法。它期望一个带有边值的散列，但是由于每个边都有相同的值，并且我不想使用不必要的额外内存，我利用 Ruby 的 duck typing 编写了一个名为`EdgeWeightHack`的类。

现在让我们来看看这是怎么回事！让我们来看看`Chuck Norris`和`Computer programming` :
有多远

```
################################################################
Chuck_Norris is 3 degrees separated from Computer_programming.
################################################################
/wiki/Chuck_Norris
/wiki/Republican_Party_(United_States)
/wiki/Internal_Revenue_Service
/wiki/Computer_programming
################################################################ 
```

算法不得不搜索 1113 个链接才能找到答案，而我们(程序员)和查克只差 3 度！不错！

在某样东西工作之后，我总是试着看看我是否能改进它。我觉得制作一个 BFS 来添加节点，然后搜索路径是双重工作，因为在这种情况下，BFS 将找到一个最小路径！

由于我不想搜索，所以我放在图中的每个节点都必须知道我用来到达它的路径，所以我们来构建这个自定义图:

```
module Crawlers
  class Custom < Base
    def call
      queue = []
      queue.push(Node.new(@from_path))

      answer = []

      while (current = queue.shift) != nil
        return current.complete_articles_path if current.article == @to_path

        links = @repository.get_links(current.article)

        if links.include?(@to_path)
          return (answer = current.complete_articles_path << @to_path)
        end

        links.each do |article|
          unless current.previous_articles.include?(article)
            queue.push(Node.new(article, current.complete_articles_path))
          end
        end
      end

      answer
    end

    class Node
      attr_reader :article, :previous_articles

      def initialize(article, previous_articles = [])
        @article = article
        @previous_articles = previous_articles
      end

      def complete_articles_path
        @previous_articles + [@article]
      end
    end
  end
end 
```

现在是时候对两个解决方案进行基准测试，看看哪一个是最好的:

```
Benchmarking 100 times

       user     system      total        real
Custom 63.772801   9.894999  73.667800 (249.561173)
Graph 197.834715  10.884686 208.719401 (378.234052)

Memory usage of Custom ---------------------------
        Total allocated: 155287889 bytes (2223856 objects)
        Total retained: 546 bytes (2 objects)
Memory usage of Graph ----------------------------
        Total allocated: 259432021 bytes (2015009 objects)
        Total retained: 97485111 bytes (517055 objects) 
```

哇！这实际上在速度和内存上都是一个非常大的改进！

## 结论

尽管解决这个问题的结果不是很有用，但我从中获得了很多乐趣。我能够以更“现实”的方式使用我所学的算法(与书本上的练习相比)。

我希望你和我一样喜欢这个！你可以在我的 github 账号上查看完整实现[！](https://github.com/alebian/six-degrees-of-wikipedia)

[![Buy Me A Coffee](img/5a370298d6333d837f8a2300ee8ce599.png)](https://www.buymeacoffee.com/alebian)