# 可观察的网络工作者，对现实用例的深入探究

> 原文：<https://dev.to/zakhenry/observable-web-workers-a-deep-dive-into-a-realistic-use-case-4042>

在[上一篇文章](https://dev.to/zakhenry/observable-webworkers-with-angular-8-4k6)中，我们看了一下库 [`observable-webworker`](https://github.com/cloudnc/observable-webworker) ，它让我们使用熟悉的可观察模式来构建和管理 Web Workers 以及线程之间的通信。在本文中，我们将开发一个应用程序，首先不使用 web worker，然后使用 web worker 来重构它，以展示 web worker 的强大功能和有用性。如果您还没有阅读前一篇文章，我建议您先阅读，因为它介绍了启动和运行的所有先决条件，以及什么是 web workers 的背景知识，在这里我将跳过这些内容。

这篇文章是对高级开发人员的深入探讨，所以请喝一杯饮料，安顿下来。在本文中，我假设您对 Typescript 和 RxJS 有一定的了解，所以如果您是初学者，恐怕这不适合您。这里有一个目录，如果你愿意的话，你可以分阶段回到这篇文章。

*   [简介](#brief)
*   [数据流计划](#data-flow-plan)
*   [算法](#algorithm)
*   [应用](#application)
    *   [通用功能](#common-functionality)
    *   [服务](#service)
    *   [组件](#component)
    *   [主线程测试](#main-thread-test)
    *   [工人](#worker)
    *   [工人线程测试](#worker-thread-test)
*   [性能](#performance)
*   [总结](#wrap-up)

## 简短

我们将要构建的应用程序有几个要求，我将把它们作为验收标准列出来，供我们以后参考。

> 作为一名用户，我希望能够搜索从古腾堡项目中获取的各种小说文本。我的搜索结果应该包含匹配段落上下文中突出显示的我的搜索短语。当我打错字或拼错单词时，我仍然会得到预期的结果。

为了好玩，我将在 [BDD 小黄瓜语法](https://cucumber.io/docs/guides/10-minute-tutorial/)
中详述标准

```
Feature: Find phrases in books by searching for them

    Background:
        Given I'm a user on the home page
        And I have selected the book "Alice in Wonderland"
        And I see a free text field in which I can type my search phrase

    # basic search
    Scenario
        When I type "we’re all mad here"
        Then I should see the first search result containing the text "‘Oh, you can’t help that,’ said the Cat: ‘we’re all mad here. I’m mad. You’re mad.’"

    # multiple result search
    Scenario
        When I type "mad"
        Then I should see search results with "mad" highlighted at least "10" times

    # search with typos
    Scenario
        When I type "were all madd her"
        Then I should see the first search result containing the text "‘Oh, you can’t help that,’ said the Cat: ‘we’re all mad here. I’m mad. You’re mad.’" 
```

除了这些用户旅程要求，我们还将有以下性能要求:

*   用户界面必须随时保持响应
*   用户每击一次键就能实时获得搜索结果
*   用户可以更换图书，搜索立即开始返回他们先前搜索的结果

在需求中需要特别注意的困难部分是处理打字错误和拼写错误的能力。这增加了问题的复杂性，因为它不像寻找一个段落的子串那样简单；相反，我们需要对候选子字符串进行评分，以找到最佳匹配。

## 数据流计划

首先，我们将创建一个简单的 typescript 文件来证明可观察的流程，然后将它构建到应用程序中。

```
// playground/observable-flow.ts

import { from, Observable, of } from 'rxjs';
import { scan, shareReplay, switchMap } from 'rxjs/operators';

/**
 * First off, we create a quick enum of the books we will use as a demo. Later
 * we will update the urls to be correct
 */
enum BookChoice {
  ALICE_IN_WONDERLAND = 'http://some-url-to-alice-in-wonderland-text',
  SHERLOCK_HOLMES = 'http://some-url-to-sherlock-holmes-text',
}

/**
 * This observable will be something like a regular dropdown, emitted whenever
 * the user changes their selection.
 */
const userBookSelection$ = of(BookChoice.ALICE_IN_WONDERLAND);

/**
 * This observable represents the stream of search terms the user will enter
 * into a text box. We `shareReplay(1)` so that subsequent subscribers will get
 * the latest value
 */
const userSearchTerm$ = of(`we’re all mad here`).pipe(shareReplay(1));

/**
 * This function will be responsible for fetching the content of the book given
 * the enum. We're cheating a little by making the enum value the url to fetch.
 * For now we will just pretend the url is the content of the book.
 * @todo implement
 */
function getBookText(bookChoice: BookChoice): Observable<string> {
  return of(bookChoice);
}

/**
 * This function will be responsible for taking the search term and returning
 * the stream of paragraphs found, as soon as they are found.
 *
 * For the purposes of quick testing, we've hardcoded as if two search results
 * were found
 *
 * We will also likely extend this return type in future to handle highlighting
 * the search phrase, but for now just the paragraph is sufficient for testing
 * @todo implement
 */
function getSearchResults(
  searchTerm: string,
  bookText: string,
): Observable<string> {
  return from([
    searchTerm + ' (this will be the first result)',
    searchTerm + ' (this will be the second result)',
  ]);
}

/**
 * Here we take the user selected book stream and pipe it via switchMap to fetch
 * the content of the book. We use switchMap() because we want to cancel the
 * download of the book if the user switches to a different book to search
 * before the book has finished downloading.
 *
 * Next we again switchMap() the result of the book content to the user search
 * term observable so that if the user has changed books, once it is loaded we
 * will cancel the processing of the current search term.
 *
 * Next we pass that stream of search terms to getSearchResults() which will
 * itself be returning a stream of search results for that search string.
 *
 * Finally, we use a scan() operator to collate the stream of search results
 * into an array so that we can present all results to the user, not just the
 * most recent one
 */
const searchResults$ = userBookSelection$.pipe(
  switchMap(selection => getBookText(selection)),
  switchMap(bookText => {
    return userSearchTerm$.pipe(
      switchMap(searchTerm =>
        getSearchResults(searchTerm, bookText).pipe(
          scan((searchResults: string[], searchResult) => {
            return [...searchResults, searchResult];
          }, []),
        ),
      ),
    );
  }),
);

/**
 * Last but not least, to check our logic, we subscribe to the observable and
 * bind the console so we see output.
 */
searchResults$.subscribe(console.log); 
```

好的，我们已经有了一个概念，我们想要构建什么，那么当我们运行它的时候，我们得到了什么？我喜欢使用`ts-node`进行这种快速测试，所以只需运行`npx ts-node --skip-project playground/observable-flow.ts`(提示:`--skip-project`位只是因为我当前所在的工作目录中有一个 tsconfig.json 文件，该文件与只运行普通 nodejs 脚本不兼容。)

我们对上述文件的输出如下:

```
[ 'we’re all mad here (this will be the first result)' ]
[
  'we’re all mad here (this will be the first result)',
  'we’re all mad here (this will be the second result)'
] 
```

好的，这是意料之中的——我们首先在数组中得到一个结果，然后在数组中得到两个结果。成功？嗯，有点，但我们真的想把一些现实主义到这个脚本中，这样我们就可以看到我们的可观测量有正确的行为。

因此，我们现在将编辑刚刚编写的脚本，添加一些真实的延迟和更多的日志记录，看看到底发生了什么:

```
// playground/observable-flow-test.ts

import { from, Observable, of } from 'rxjs';
import {
  concatMap,
  delay,
  map,
  pairwise,
  scan,
  shareReplay,
  startWith,
  switchMap,
  timestamp,
} from 'rxjs/operators';

enum BookChoice {
  ALICE_IN_WONDERLAND = 'http://alice.text',
  SHERLOCK_HOLMES = 'http://sherlock.text',
}

/**
 * This is a nice little custom operator that spaces out observables by a certain amount, this is super handy for
 * emulating user events (humans are slooow!)
 */
function separateEmissions<T>(delayTime: number) {
  return (obs$: Observable<T>): Observable<T> => {
    return obs$.pipe(
      concatMap((v, i) => (i === 0 ? of(v) : of(v).pipe(delay(delayTime)))),
    );
  };
}

/**
 * For the book selection, we've piped to separateEmissions() with 4000ms
 * defined, this means when subscribed the observable will immediately emit
 * Alice in Wonderland content, then 4 seconds later emit Sherlock Holmes content.
 */
const userBookSelection$ = from([
  BookChoice.ALICE_IN_WONDERLAND,
  BookChoice.SHERLOCK_HOLMES,
]).pipe(separateEmissions(4000));

/**
 * Slightly different strategy for this one - we're
 * 1\. Piping delayed user book selection to vary the search phrase depending on
 * which book is selected
 * 2\. creating a streams of individual characters
 * 3\. spacing out the emissions by 100ms (this is the inter-keystroke time)
 * 4\. using scan to combine the previous characters
 * The result is a pretty good simulation of the user typing the phrase at 10
 * keys per second
 */
const userSearchTerm$ = userBookSelection$.pipe(
  delay(200),
  switchMap(book => {
    const searchPhrase =
      book === BookChoice.ALICE_IN_WONDERLAND
        ? `we’re all mad here`
        : `nothing more deceptive than an obvious fact`;

    return from(searchPhrase).pipe(
      separateEmissions(100),
      scan((out, char) => out + char, ''),
    );
  }),
  shareReplay(1),
);

/**
 * Here, we're guessing it will take about 200ms to download the book. We've
 * also put in a console.log so we can make sure we're not going to try download
 * the book on every keystroke!
 * @param bookChoice
 */
function getBookText(bookChoice: BookChoice): Observable<string> {
  console.log(`getBookText called (${bookChoice})`);
  return of(bookChoice).pipe(delay(200));
}

/**
 * With this function we're saying that the search takes (20 milliseconds * the
 * length of the search string)
 * This is actually totally unrealistic, but the linear variability will help
 * when understanding the logs
 */
function getSearchResults(
  searchTerm: string,
  bookText: string,
): Observable<string> {
  return from([' (first search result)', ' (second search result)']).pipe(
    map(result => `${bookText} : ${searchTerm} : ${result}`),
    delay(20 * searchTerm.length),
    separateEmissions(200),
  );
}

/**
 * This is unchanged from before
 */
const searchResults$ = userBookSelection$.pipe(
  switchMap(selection => getBookText(selection)),
  switchMap(bookText => {
    return userSearchTerm$.pipe(
      switchMap(searchTerm =>
        getSearchResults(searchTerm, bookText).pipe(
          scan((searchResults: string[], searchResult) => {
            return [...searchResults, searchResult];
          }, []),
        ),
      ),
    );
  }),
);

/**
 * Lastly we'd doing a few tricks to make the output express what happened
 * better.
 * The combination of timestamp and pairwise gives us a stream of when the
 * emission happened and bundles it with the previous one so we can compare
 * times to get a time taken value. The startWith(null) just gives us the
 * startup time as a baseline.
 * Lastly we use our old friend map() to output the data in a nice format for
 * the logger.
 */
searchResults$
  .pipe(
    startWith(null),
    timestamp(),
    pairwise(),
    map(([before, tsResult], i) => {
      const timeSinceLast = (tsResult.timestamp - before.timestamp) / 1000;
      return `${i} : Search Result: [${tsResult.value.join(
        ', ',
      )}] (+${timeSinceLast} seconds)`;
    }),
  )
  .subscribe(console.log); 
```

```
getBookText called (http://alice.text)
0 : Search Result: [http://alice.text : w :  (first search result)] (+0.431 seconds)
1 : Search Result: [http://alice.text : we :  (first search result)] (+0.123 seconds)
2 : Search Result: [http://alice.text : we’ :  (first search result)] (+0.128 seconds)
3 : Search Result: [http://alice.text : we’r :  (first search result)] (+0.121 seconds)
4 : Search Result: [http://alice.text : we’re :  (first search result)] (+0.121 seconds)
5 : Search Result: [http://alice.text : we’re all mad here :  (first search result)] (+1.6 seconds)
6 : Search Result: [http://alice.text : we’re all mad here :  (first search result), http://alice.text : we’re all mad here :  (second search result)] (+0.204 seconds)
getBookText called (http://sherlock.text)
7 : Search Result: [http://sherlock.text : n :  (first search result)] (+1.704 seconds)
8 : Search Result: [http://sherlock.text : no :  (first search result)] (+0.125 seconds)
9 : Search Result: [http://sherlock.text : not :  (first search result)] (+0.124 seconds)
10 : Search Result: [http://sherlock.text : noth :  (first search result)] (+0.125 seconds)
11 : Search Result: [http://sherlock.text : nothi :  (first search result)] (+0.122 seconds)
12 : Search Result: [http://sherlock.text : nothing more deceptive than an obvious fact :  (first search result)] (+4.68 seconds)
13 : Search Result: [http://sherlock.text : nothing more deceptive than an obvious fact :  (first search result), http://sherlock.text : nothing more deceptive than an obvious fact :  (second search result)] (+0.201 seconds) 
```

好吧，让我们深入研究一下。

我们可以直接看到，我们立即拿到了《爱丽丝梦游仙境》这本书，再也不会——这太完美了。

接下来，当短语开始被输入时，我们首先得到一个结果，然后另一个被附加到结果中，很好，很好。

稍后(以`5`开始的那一行)我们可以看到，搜索结果速度变慢意味着我们得到结果的频率降低了，而且它们是针对比下一个字符更长的搜索短语——这是我们所期望的，因为这意味着`switchMap`正在取消订阅搜索处理器功能，因为有不同的数据要处理。这是一个比较`switchMap()`、`mergeMap()`和`exhaustMap()`不同行为的好机会:

*   如果我们选择了`mergeMap()`，我们将会看到每一次击键的每一个搜索结果，但是很可能所有的结果都是相互重叠的，并且会非常混乱。此外，假设 CPU 在处理搜索时饱和，总时间会更长。

*   如果我们选择了`exhaustMap()`，我们将得到一组详尽的结果(因此得名！)按照正确的顺序，但是总的时间会比 T2 长得多，因为我们必须按顺序等待。

在这种情况下，我认为`switchMap()`是正确的行为，因为用户在完成输入之前对中间搜索结果不感兴趣，我们通过立即取消不相关搜索结果的计算来提高效率。

回到输出分析，在标记为`7`的行中，我们看到我们已经切换到使用新的搜索短语来获取夏洛克·福尔摩斯。成功！

好了，我们现在已经对一般的数据流非常有信心了，让我们来看看我们将用来对段落评分的算法，然后再开始构建应用程序。

## 算法

考虑到我们的搜索词可能包含打字错误和拼写错误的要求，找到向用户展示的最佳段落的算法是非常重要的。有许多不同的模糊字符串匹配算法，每种算法都有自己的优点和缺点。鉴于我们的要求是关于打字错误和拼写错误，而不是词序错误，计算 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)听起来是一个很好的选择。

Levenshtein 距离是将一个字符串更改为另一个字符串所需的更改次数。这些改变可以是插入、删除或替换。距离就是变化的最小次数。

例如，`hello`和`helo`之间的距离是`1`，因为需要一次插入。两个相同的字符串将有一个距离`0`。

现在，我们的输入不太适合这种算法，因为我们正在比较一个小的搜索短语和一个大的段落——我们的距离得分将主要表明搜索词和段落长度的差异。相反，我们将修改算法以忽略搜索字符串的开始和结束，这样当搜索字符串放在段落中的任何位置时，我们计算搜索字符串的最小 Levenshtein 距离。

最后，我们将根据搜索字符串的长度来归一化 Levenshtein 距离，以便计算相似性得分。这将用于按最佳匹配排列段落。

为了不使这篇文章变得太长，我将跳过这个实现，但是您可以自己在[https://github . com/zak Henry/blog-posts/tree/master/posts/observable-workers-deep-dive/src/app/book-search/common/fuzzy-substring . ts](https://github.com/zakhenry/blog-posts/tree/master/posts/observable-workers-deep-dive/src/app/book-search/common/fuzzy-substring.ts)中深入研究它

重要的是要知道有一个函数`fuzzySubstringSimilarity` :

```
// src/app/book-search/common/fuzzy-substring.ts#L70-L73

export function fuzzySubstringSimilarity(
  needle: string,
  haystack: string,
): FuzzyMatchSimilarity { 
```

返回`FuzzyMatchSimilarity` :

```
// src/app/book-search/common/fuzzy-substring.ts#L1-L9

export interface FuzzyMatch {
  substringDistance: number;
  startIndex: number;
  endIndex: number;
}

export interface FuzzyMatchSimilarity extends FuzzyMatch {
  similarityScore: number;
} 
```

我们将使用这个返回类型对小说的段落进行评分，并返回匹配的开始和结束索引，这样我们就可以在输出中突出显示它。

让我们根据上面定义的用户情景来测试算法:

```
import { fuzzySubstringSimilarity } from '../src/app/book-search/common/fuzzy-substring';

const similarity = fuzzySubstringSimilarity(
  'were all madd her',
  '‘Oh, you can’t help that,’ said the Cat: ‘we’re all mad here. I’m mad. You’re mad.’',
);
console.log(`Similarity: `, similarity); 
```

我们在这里所做的就是将针定义为搜索字符串，将干草堆定义为预期的匹配句子。如果我们运行这个文件，我们会得到以下结果:

```
Similarity:  {
  substringDistance: 2,
  startIndex: 42,
  endIndex: 59,
  similarityScore: 0.8823529411764706
} 
```

正如所料，我们看到了一个子串距离`2`(为了得到期望的子串，我们需要添加一个`'`并删除一个`d`，两个变化，距离是`2`)。我们还可以看到,`similarityScore`非常高(范围是 0-1 ),所以我们可以期待这个搜索在这个段落上比其他段落得分高。

好了，我们现在有了一个排序的算法，和一个管理它的数据流。我们来建个 app 吧！

## 申请

到目前为止，我们一直在使用单一的类型脚本文件来完成我们的想法，但现在我们将使用一个框架，因为这对于我们希望通过理解本文来解决的现实世界的问题来说更加现实。

我将使用 Angular，但是如果你喜欢任何其他优秀的框架，不要担心，因为这将是一个相当不可知的框架。如果您已经做到了这一步，那么可以肯定您已经掌握了框架开发的基础知识，所以我将忽略这一点。

```
ng new observable-workers-deep-dive 
```

```
ng generate component book-search 
```

好了，我们已经搭建了一个基本的框架，假设我们正在尝试测试直接使用 observables 和在 web workers 中使用 observables 之间的区别，当我们构建应用程序时，我们将尝试将通用功能提取到一个地方。这样，在我们的性能测试中，尽可能多的变量得到控制，因为工作线程和主线程策略将使用完全相同的核心算法代码。

### 常用功能

首先，我们将构建一个实用函数来处理获取一系列模糊匹配的段落:

```
// src/app/book-search/common/book-search.utils.ts#L8-L25

interface SearchMatch {
  paragraph: string;
  paragraphNumber: number;
  searchMatch: FuzzyMatchSimilarity;
}

function getSearchResults(
  searchTerm: string,
  paragraphs: string[],
): Observable<SearchMatch> {
  return from(paragraphs).pipe(
    observeOn(asyncScheduler),
    map((paragraph, index) => {
      const searchMatch = fuzzySubstringSimilarity(searchTerm, paragraph);
      return { searchMatch, paragraph, paragraphNumber: index };
    }),
  );
} 
```

这里需要注意一些非常重要的事情。我们用`from()`将段落数组转换成一个可见的单个段落流。

在这之后，我们使用`observeOn(asyncScheduler)`——这对于应用程序的响应能力来说是关键的。基本上这是在重新安排`from()`可观测的发射从同步到异步。这允许我们流的订阅者从段落流**断开连接，而不必计算整本书的模糊子串分数**。这将允许我们在搜索字符串由于用户输入更多字符而无效时丢弃部分搜索结果。

最后，我们`map()`到计算函数，并将段落索引号附加到输出中——我们稍后将使用它来计算出计算的进度百分比。

下一个常见函数是累加函数，它将

1.  以我们的搜索结果流为例
2.  将它们整理成一个数组
3.  按分数对结果数组进行排序
4.  取前十名结果
5.  将得到的段落分割成不匹配文本和匹配文本

```
// src/app/book-search/common/book-search.utils.ts#L27-L77

export interface MatchingParagraph {
  before: string;
  match: string;
  after: string;
  score: number;
}

export interface SearchResults {
  paragraphs: MatchingParagraph[];
  searchedParagraphCount: number;
  paragraphCount: number;
}

function accumulateResults(paragraphCount: number) {
  return (obs$: Observable<SearchMatch>): Observable<SearchResults> => {
    return obs$.pipe(
      scan((searchResults: SearchMatch[], searchResult: SearchMatch) => {
        searchResults.push(searchResult);
        return searchResults;
      }, []),
      startWith([]),
      map(
        (searchMatches: SearchMatch[]): SearchResults => {
          const last = searchMatches[searchMatches.length - 1];

          return {
            searchedParagraphCount: last ? last.paragraphNumber : 0,
            paragraphCount,
            paragraphs: searchMatches
              .sort(
                (a, b) =>
                  b.searchMatch.similarityScore - a.searchMatch.similarityScore,
              )
              .slice(0, 10)
              .map(({ searchMatch, paragraph }) => {
                return {
                  score: searchMatch.similarityScore,
                  match: paragraph.substring(
                    searchMatch.startIndex,
                    searchMatch.endIndex,
                  ),
                  before: paragraph.substring(0, searchMatch.startIndex),
                  after: paragraph.substring(searchMatch.endIndex),
                };
              }),
          };
        },
      ),
    );
  };
} 
```

除了行数之外，这里没有什么特别复杂的，但是有几点需要注意:

*   使用了`scan()`操作符，这样我们就可以在结果进来的时候得到结果流，如果我们想等待搜索完成，我们可以使用`reduce()`(rxjs 操作符，而不是`Array.prototype.reduce`)
*   我们使用`startWith([])`,以便订阅者在订阅时立即获得一个空的结果集

最后，我们将这两个函数合并成一个函数，接受一个搜索词和一本书，并返回累积的搜索结果流。

```
// src/app/book-search/common/book-search.utils.ts#L79-L87

export function getAccumulatedSearchResults(
  searchTerm: string,
  bookText: string,
): Observable<SearchResults> {
  const paragraphs = bookText.split('\n\n');
  return getSearchResults(searchTerm, paragraphs).pipe(
    accumulateResults(paragraphs.length),
  );
} 
```

### 服务

好了，完成繁重工作的基本函数都已经排序好了，我们差不多到了最后阶段了！让我们创建一个服务来包装这个逻辑，稍后我们将在组件中使用它。

```
// src/app/book-search/main-thread/book-search.service.ts#L11-L44

@Injectable({
  providedIn: 'root',
})
export class BookSearchService {
  constructor(private http: HttpClient) {}

  public search(
    bookSelection$: Observable<BookChoice>,
    searchTerm$: Observable<string>,
  ): Observable<SearchResults> {
    return this.processSearch(bookSelection$, searchTerm$).pipe(
      auditTime(1000 / 60), // emit results at a maximum of 60fps
      share(),
    );
  }

  protected processSearch(
    url$: Observable<string>,
    search$: Observable<string>,
  ): Observable<SearchResults> {
    const sharedSearchTerm$ = search$.pipe(shareReplay(1));

    return url$.pipe(
      switchMap(url => this.http.get(url, { responseType: 'text' })),
      switchMap(bookText => {
        return sharedSearchTerm$.pipe(
          switchMap(searchTerm => {
            return getAccumulatedSearchResults(searchTerm, bookText);
          }),
        );
      }),
    );
  }
} 
```

通过这个服务，我们将一个`HttpClient`注入到构造函数中；这允许我们获取书的内容。接下来是我们将在组件中使用的公共方法。它需要两个流:

*   首先是`BookChoice`。回想一下前面的内容，这是一个字符串枚举，其值是书的 URl
*   第二个是搜索短语本身，用于对书中的段落进行模糊匹配

我们调用一个本地的`processSearch`方法，该方法简单地获取 url 并通过管道将其发送到获取内容的`switchMap()`，然后依次通过管道发送到搜索词 observable，最后我们打开该 observable 来调用我们之前构建的`getAccumulatedSearchResults`。

回到`search()`方法，我们通过管道从`processSearch()`得到结果，并首先使用`auditTime()`操作符。这将数据输出速率限制为每秒 60 帧，否则我们可能每秒得到数万个`SearchResults`，当我们试图在 DOM 中显示结果时，这将使框架的变化检测策略完全过饱和。

最后，我们给搜索结果添加了一个`share()`,因为我们不希望多个订户不止一次地触发搜索结果的计算。

接下来，让我们定义将该服务连接到用户输入的组件逻辑。

### 组件

我们的组件需求非常简单——它需要为图书选择和搜索词提供一个表单控件，然后输出搜索结果。

```
// src/app/book-search/main-thread/book-search.component.ts#L13-L59

@Component({
  selector: 'app-book-search',
  templateUrl: './book-search.component.html',
  styleUrls: ['./book-search.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class BookSearchComponent {
  public componentName = 'Main thread search';

  public bookChoices = [
    {
      url: BookChoice.ALICE_IN_WONDERLAND,
      name: 'Alice in Wonderland',
    },
    {
      url: BookChoice.SHERLOCK_HOLMES,
      name: 'Sherlock Holmes',
    },
  ];

  public bookSelectionFormControl = new FormControl(null);
  public userBookSelection$: Observable<BookChoice> = this
    .bookSelectionFormControl.valueChanges;

  public searchTermFormControl = new FormControl(null);
  public userSearchTerm$: Observable<string> = this.searchTermFormControl
    .valueChanges;

  private searchResults$: Observable<
    SearchResults
  > = this.bookSearchHandler.search(
    this.userBookSelection$,
    this.userSearchTerm$,
  );

  public searchResultParagraphs$: Observable<
    MatchingParagraph[]
  > = this.searchResults$.pipe(map(result => result.paragraphs));

  public searchResultProgress$: Observable<
    [number, number]
  > = this.searchResults$.pipe(
    map(result => [result.searchedParagraphCount, result.paragraphCount]),
  );

  constructor(private bookSearchHandler: BookSearchService) {}
} 
```

该组件的关注点:

*   我们为图书选择构造一个新的`FormControl`，然后立即设置一个观察控件的`valueChanges`的可观察对象
*   我们对搜索词做同样的事情
*   接下来，我们使用前面的两个可观测量来构造`private searchResults$: Observable<SearchResults>`,它调用我们之前刚刚定义的服务
*   最后两个公共成员观察`searchResults$`，第一个提取匹配的段落，第二个提取搜索进度信息

接下来让我们看看这将如何在模板中显示:

```
<!-- src/app/book-search/main-thread/book-search.component.html -->

<h2>{{ componentName }}</h2>

<select [formControl]="bookSelectionFormControl">
  <option *ngFor="let book of bookChoices" [value]="book.url">
    {{ book.name }}
  </option>
</select>

<input type="text" [formControl]="searchTermFormControl" />

<span *ngIf="searchResultProgress$ | async as searchResultProgress">
  Progress: {{ searchResultProgress[0] }} / {{ searchResultProgress[1] }} ({{
    searchResultProgress[0] / searchResultProgress[1] | percent
  }})
</span>

<ng-container *ngFor="let matchingParagraph of searchResultParagraphs$ | async">
  <hr />
  <blockquote>
    <span>{{ matchingParagraph.before }}</span>
    <strong>{{ matchingParagraph.match }}</strong>
    <span>{{ matchingParagraph.after }}</span>
    <footer>Score: {{ matchingParagraph.score }}</footer>
  </blockquote>
</ng-container> 
```

我们有一个简单的模板

*   一个基本的`<select>`管理图书选择
*   然后是一个`<input>`来管理用户搜索
*   然后是输出搜索结果进度的`<span>`
*   最后一次重复`<blockquote>`来输出搜索的结果

注意，我们使用`AsyncPipe`来管理*所有的*订阅——这极大地简化了组件逻辑，并允许我们使用`OnPush`变更检测策略，因为`AsyncPipe`管理标记要检查的组件。

终于，我们到了，让我们测试一下。

### 主线程测试

好了，当我们运行应用程序时，我们看到了两个控件。因此，让我们选择“爱丽丝和仙境”并键入那个充满错别字的字符串`"were all madd her"`

[![Main thread Demo](img/de40ad2012b5ebadc80f51087c46a584.png)](https://i.giphy.com/media/WQrOW2zcL4mxlF6odx/giphy.gif)

好吧！这很好——我们已经设法获取了搜索结果，第一个结果是预期的匹配:

```
were all madd her
 Progress: 900 / 901 (100%)
‘Oh, you can’t help that,’ said the Cat: ‘we’re all mad here. I’m mad. You’re mad.’
Score: 0.8823529411764706 
```

然而,应用程序的响应速度很糟糕 gif 并没有停顿，它确实在输入第一个字符后锁定了，然后几秒钟后所有的结果都出现了。
幸运的是，我们有 Web Workers 这个令人难以置信的工具，我们可以使用它来释放这个过饱和的主线程，只做 UI 的事情，并将我们昂贵的搜索结果计算放在一个单独的线程中。

### 工人

所以，让我们创建一个工人

```
ng generate web-worker book-search 
```

这将把一个`book-search.worker.ts`文件放入我们的应用程序:

```
/// <reference lib="webworker" />

addEventListener('message', ({ data }) => {
  const response = `worker response to ${data}`;
  postMessage(response);
}); 
```

我们不想要任何样板文件，所以我们将删除它并实现我们自己的工人版本的`processSearch`方法，我们在前面的服务中创建了:

```
// src/app/book-search/worker-thread/book-search.worker.ts

import { DoWork, ObservableWorker } from 'observable-webworker';
import { Observable } from 'rxjs';
import { ajax } from 'rxjs/ajax';
import {
  distinctUntilChanged,
  map,
  shareReplay,
  switchMap,
} from 'rxjs/operators';
import {
  getAccumulatedSearchResults,
  SearchResults,
  WorkerInput,
} from '../common/book-search.utils';

@ObservableWorker()
export class BookSearchWorker implements DoWork<WorkerInput, SearchResults> {
  public work(input$: Observable<WorkerInput>): Observable<SearchResults> {
    const url$ = input$.pipe(
      map(({ url }) => url),
      distinctUntilChanged(),
    );

    const searchTerm$ = input$.pipe(
      map(({ searchTerm }) => searchTerm),
      distinctUntilChanged(),
      shareReplay(1),
    );

    return url$.pipe(
      switchMap(url => ajax({ url, responseType: 'text' })),
      map(result => result.response),
      switchMap(bookText => {
        return searchTerm$.pipe(
          switchMap(searchTerm =>
            getAccumulatedSearchResults(searchTerm, bookText),
          ),
        );
      }),
    );
  }
} 
```

这应该看起来很熟悉，因为它实际上只是来自 [`observable-webworker`](https://github.com/cloudnc/observable-webworker) 包的`@ObservableWorker()`修饰类中的`processSearch`函数。唯一的区别是`work()`方法的输入是一个单独的`Observable<WorkerInput>`流，我们将它分开并分别观察。

从那以后，它基本上是相同的，尽管我们使用 rxjs `ajax()`方法而不是`HttpClient`，因为`Injector`在 worker 上下文中不可用。

现在让主线程类来管理这个 worker:

```
// src/app/book-search/worker-thread/book-search-worker.service.ts

import { Injectable } from '@angular/core';
import { fromWorker } from 'observable-webworker';
import { combineLatest, Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { BookSearchService } from '../main-thread/book-search.service';
import { SearchResults, WorkerInput } from '../common/book-search.utils';

@Injectable({
  providedIn: 'root',
})
export class BookSearchWorkerService extends BookSearchService {
  protected processSearch(
    url$: Observable<string>,
    search$: Observable<string>,
  ): Observable<SearchResults> {
    const input$: Observable<WorkerInput> = combineLatest(url$, search$).pipe(
      map(([url, searchTerm]) => ({ searchTerm, url })),
    );

    return fromWorker(
      () => new Worker('./book-search.worker', { type: 'module' }),
      input$,
    );
  }
} 
```

因为我们已经在`BookSearchService`中实现了`search()`逻辑，所以我们简单地扩展了那个类，并在一个新的`BookSearchWorkerService`类中覆盖了`processSearch()`的实现。

在真实的情况下，您可能只需要替换`processSearch`方法，但是在本文中我们将有两个实现，所以我们稍后可以并排比较它们。

我们的`processSearch`实现非常简单，我们将两个输入流`combineLatest`成工人需要的形式。然后我们使用来自`'observable-webworker'`的`fromWorker()`方法来包装`new Worker`的构造，并将输入流作为第二个参数传递。

现在，当我们试图保留旧的行为时，我们需要一个新的组件来展示这个闪亮的新的工人支持的服务:

```
// src/app/book-search/worker-thread/book-search-worker.component.ts

import { ChangeDetectionStrategy, Component } from '@angular/core';
import { BookSearchWorkerService } from './book-search-worker.service';
import { BookSearchComponent } from '../main-thread/book-search.component';
import { BookSearchService } from '../main-thread/book-search.service';

@Component({
  selector: 'app-book-search-worker',
  templateUrl: '../main-thread/book-search.component.html',
  styleUrls: ['../main-thread/book-search.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush,
  providers: [
    { provide: BookSearchService, useClass: BookSearchWorkerService },
  ],
})
export class BookSearchWorkerComponent extends BookSearchComponent {
  public componentName = 'Worker thread search';
} 
```

这里没发生什么事！我们所做的就是扩展`BookSearchComponent`，重用它的模板和样式，并提供我们新的`BookSearchWorkerService`而不是默认的`BookSearchService`。

现在，在我们的`app.component.html`模板中，我们可以插入这个新组件:

```
<!-- src/app/app.component.html -->

<app-book-search></app-book-search>
<app-book-search-worker></app-book-search-worker> 
```

现在我们有了图书搜索的两个实现——一个在主线程中，另一个由工作线程服务支持。

### 工作者线程测试

让我们测试新的 worker 组件！

[![Worker thread Demo](img/e039ec65d4522de9726f26397c74a803.png)](https://i.giphy.com/media/XAyCxNHS30wkyHcKq9/giphy.gif)

哇！性能怎么样？！现在我不会称之为最好的用户体验，但是你真的可以在这里看到网络工作者的力量。

此外，在这个演示中，您可以真正看到我们不得不`auditTime()`搜索结果的原因，因为这里的 DOM 更新频率非常高。事实上，如果您注释掉该函数，您会发现 web worker 实现的性能明显比 T2 差得多，因为它以如此高的频率发出搜索结果，导致 Angular 的变化检测周期的反压力，使整体性能实际上更差。对我来说这是一堂真正的外卖课，希望你也是-

Web 工作者可以让你的计算输出如此之快，以至于在 DOM 中显示结果会成为一个主要的整体瓶颈

视觉比较当然很好，但是让我们来看看这两种策略之间的性能差异

## 表现

为了测试相对性能，我将在夏洛克·福尔摩斯的书中搜索短语`"There is nothing more deceptive than an obvious fact"`。我将使用常规的主线程策略执行三次，每次都清除输入，然后对 web worker 策略执行同样的操作。

[![Strategy Performance](img/602dcd2e1ced444f4d6943b5ff6df459.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MofV2XWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rso3qkd37i1bvqtvd4ct.png)

从这些图中可以清楚地看出使用 web worker 的区别——主线程(图中黄色部分)在整整 15 秒内保持 100%,在此期间重画会受到很大影响。对于 web worker 策略，主线程的利用率几乎没有达到 50%的峰值，实际上在此期间大部分时间都是空闲的。

此时值得注意的是，总的持续时间大致相同，并且可以预期，在某些情况下，worker 策略总体上可能会稍慢一些。这是因为这种策略需要启动一个新的线程，而线程间数据流的结构化复制并不是零成本的。

另一个考虑是我们没有充分利用 web workers 的能力——我们正在运行的计算任务可以很容易地被分解成更小的任务，供多个工作线程使用，并使用线程池策略进行处理。这是另一篇文章的主题，我可能会在下一篇文章中讨论。

## 总结起来

你已经到达终点了！恐怕这有点像马拉松，但是有太多的内容要涵盖。总之，我们概述了一个真实世界的场景，在这个场景中，用户希望对一本小说进行全文搜索，然后我们提出了一个数据流策略，然后是一个算法，然后将其构建到一个应用程序中，然后进行重构以使用一个可观察的 web worker 策略，最后做了一些性能指标来证明 web worker 的效用。唷！

这些代码在 Github 上都可以找到，所以请随意克隆它，玩一玩。如果您发现任何错误或改进空间，请提出问题或拉动请求，我也在学习！

在本系列的下一篇文章中，我将演示如何使用线程池来进一步提高该应用程序的性能，或者实现一个新的真实用例来进行密集的图像处理，这将演示在线程之间传递大量数据的高性能技术。如果你有偏好，请告诉我！

再次感谢你和我呆在一起，我希望这篇文章对你有用。

[<sub>安妮·斯普拉特在 Unsplash</sub> 上的照片](https://unsplash.com/@anniespratt?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)