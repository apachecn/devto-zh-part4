# O(1)开发复杂性——重构如何保持你的应用程序的可靠性，并使你的开发时间最短

> 原文：<https://dev.to/thepeoplesbourgeois/o-1-dev-complexity-1ee5>

##### 跳到前面的[重构](#when-should-i-refactor)

当我对自己所涉足的行业过于自豪时，我喜欢停下来反思这样一个事实:软件很可能是美国这个地区大规模失业危机的核心。事后看来，我们[过去十年的主要成就](https://amp.businessinsider.com/18-tech-products-that-didnt-exist-10-years-ago-2017-7)并不那么显著；大部分软件是我们围绕自己建造的[充满荒谬的纸牌屋](https://www.theatlantic.com/technology/archive/2017/09/saving-the-world-from-code/540393/)；我们现在的主要动力是[凭空创造货币](https://www.elev8con.com/las-vegas-december-2019/)(你知道，地球能源供应的[巨大的、不合理的数量](https://www.vox.com/2019/6/18/18642645/bitcoin-energy-price-renewable-china))。

在那次基础练习之后，我反思了最初是什么吸引了我去编程:它要求你解决的难题，寻找解决方案所涉及的自由形式的本质和批判性思维，它与修辞和写作重叠的无数方式，以及如果你把它作为你的职业，正如约翰所说的，你应该怎样做

> a .别妄想你会完成。
> 
> ##### ——*斯坦贝克:书信中的生活*

人们几乎总能找到更好的方法来写一段，就像人们几乎总能找到更好的方法来写一段代码一样。这部分是因为“更好”是一个主观术语，但等式的另一半是将思想转化为语言是一门粗糙的艺术，我们的语言不一定像我们试图用它传达的感觉那样精确——当然远没有那么敏锐。这也是流程和工作流的方式；如果你曾经感受过用 Turtle 编程的乐趣(我没有)，你会理解试图简洁而清晰地向一个善意但过于直白的生物传达你的意图的矛盾。通过口语向另一个人传达意图要危险得多，超出了本文的范围，读者最好认为这是不可能的。

但是对散文和节目的编辑确实是可以实现的，借用另一句话，

> 写作是人性的，编辑是神性的。
> 
> ##### —斯蒂芬·金，*论写作:手艺回忆录*

关于技术债务和它提供的*“商业价值”*已经说得很多了。传统观点认为，“如果用户看不到这个特性，它就不会给他们带来任何好处，也不值得投资。”根据这个原理，我们作为开发人员应该放弃所有的`private`方法，95-98%的服务器逻辑，100%的测试用例——不管怎样，谁会害怕特性回归呢？–支持和 QA 团队……基本上是除了您企业的营销团队、销售团队之外的所有东西，以及最终不会在客户端界面中呈现的任何东西(此外，保留指标。高管。比如。度量标准。图表是他们的上帝。)

但事实是，**技术债务不可避免地表现为用户*将*看到，**和追逐*的商业价值*在追求交付特性时忽略了这一事实，这使得 ***实际上*** 交付特性的复杂性以非线性的规模增长。你的团队花在弄清楚你的代码试图做什么上的时间越多，你的团队在代码之上构建新特性的时间就越少，更糟糕的是，他们在构建这些新特性时就越有可能出错。这些错误可能会导致新功能无法正常运行，现有功能无法正常运行，或者/和有待开发的功能更加难以构建。处理技术债务，编辑你的代码，使其更加清晰、简洁和易于理解，对于保持一致的特性转变是至关重要的。这听起来可能很奇怪，但是保持代码有条理也会使在代码库中工作成为一种更愉快的体验。

所以让我们练习一下神，一起重构一些代码。

* * *

### 什么时候应该重构？

我决定何时重构的经验法则可以归结为四个关键问题:

1.  这个代码块比文件中的其他代码多两级缩进吗？
2.  作为一串`if…else if… else if… else`或`switch/case/cond`表达式，是否有三个以上的分支条件？
3.  这段代码修改了不止一个对象或模型吗？
4.  为了理解它在做什么，我需要重读这个块吗，哪怕是一次？

如果我对这些问题中的任何一个回答“是”,我会在我的项目管理工具中做一个 todo 或一个 ticket，提醒自己在完成我正在处理的特性时，回到代码中并清理它。如果我对不止一个问题回答“是”,我会将代码*重构为*,因为我正在开发这个特性。你们中的一些人可能会对此犹豫不决，但是相信我，你不会希望陷入一个特性驱动中，并且在一个月或更长的时间后意识到你不知道你写的东西实际上应该在做什么。

让我们用一些例子来分解这些。

### 1。多于两层的内部缩进

如今，几乎在每一种语言中，缩进级别都表示工作正在某种子进程中完成，无论是类声明、方法定义、`if-else` case、`switch`like、循环、块、lambda 还是内部匿名函数。例如，这里有一个样本代码块，是仿照我在一家企业公司工作时，在一个用 Python 写的项目中遇到的一些东西编写的:

```
items_with_classes = []
for item in items:
  for tag in item.tags:
    if tag.type in current_page.selected_types:
      items_with_classes.push({tag, tag.type}) 
```

Enter fullscreen mode Exit fullscreen mode

当然，这不是确切的代码。一部分是因为我非常确定我的 NDA 禁止我“拿走”我在那份工作中看到的任何代码，并在其他地方使用它，一部分是因为我在一年半前遇到了这种情况。但这是那个块的相同的基本结构，它的实际行为嵌套在我找到它的函数的三层深处。实际上，在你使用的任何语言中，都会有更优雅地处理这种情况的结构。在大多数情况下，您可以将每个循环的行为提取到它自己单独的函数中，但是 Python 有一种更优雅的方式来从列表中提取数据创建新列表:理解。

```
items_with_classes = [{tag, tag.type} for item in items for tag in item.tags if tag.type in current_page.selected_types] 
```

Enter fullscreen mode Exit fullscreen mode

三层嵌套逻辑变成了一行代码。而且，如果你觉得这不太可读，你仍然可以把理解分成多行。

```
items_with_classes = [{tag, tag.type} 
  for item in items 
  for tag in item.tags 
  if tag.type in current_page.selected_types] 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下,`items_with_classes`列表的意图是预先加载关于每个项目将用作其类的信息，这是通过利用该语言的一个更有趣和更具表现力的特性来完成的。(另外，它还少了一行代码😛)

### 2。分支逻辑的三种以上情况

随着应用程序处理更多的数据变化，它们变得越来越复杂，一旦数据变化必须以它们自己特定的方式进行编码和处理，这就成为开发人员开销的一个点，而开销增长的速度将取决于您的语言在抽象、动态检索和操作方面的能力。

当您使用 React 和 Flux 范型构建前端时，通常在应用程序的根处有一个`state`对象，它通过组件树向下传递到渲染节点，并处理存储您的应用程序将用来确定渲染什么、如何渲染以及何时渲染的所有数据。这个例子基于一个内部工具，我在与前一个例子相同的公司工作过。我强烈怀疑编写它的工程师来自 Java 背景…不要说对 Java 有任何负面的看法，只是感觉这种模式坚持了对 getters 和 setters 的严格约束，而不是…嗯，任何其他语言，可能除了 C.

```
function updateSearchStateAttribute(state, attrName, newValue) {
  var searchAttributes = state.searchAttributes;

  if (attrName === "inputField") {
    searchAttributes.inputField = newValue;
  } else if (attrName === "searchResults") {
    searchAttributes.searchResults = newValue;
  } else if (attrName === "mediaType") {
    searchAttributes.mediaType = newValue;
  } else if (/*…*/) {
    /* four cases later... */
  } else {
    throw new Error("attribute " + attrName + " does not exist");
  }

  state.searchAttributes = searchAttributes;
  return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

我无言以对。这个应用程序有 Babel 和 Webpack。我仍然没有词，但我最终重构了大约 24 个模仿这种模式的函数，有不同数量的`else if`案例，一直到这个:

```
function updateStateAttribute(state, sectionName, attrName, value) {
  const section = state[sectionName];
  if (!section) {
    throw new Error(`${sectionName} is not part of the state object.`);
  } else if (!section.hasOwnProperty(attrName)) {
    throw new Error(`${attrName} is not part of ${sectionName} state.`);
  }
  section[attrName] = value;
  return {
    ...state,
    section
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，语言结构提供了一种消除心理开销的优雅方式，这一次，消除如此大量的条件逻辑不仅意味着*读取*代码的心理开销得到了改善，而且*编写*新代码所需的心理开销也减少了，这**极大地**改善了这个应用程序的特性周转。

### 3。在一个块中处理多种实体

这一点有时会有例外，因为模型最终会*让*彼此交互，以在用户正在做的上下文中有意义地关联，但是作为一般的经验法则，如果你发现一个方法[承担不止一个责任](https://en.wikipedia.org/wiki/Single_responsibility_principle)，你应该考虑是否有一些方法可以重构它。

这个例子是我自己的一个例子，虽然它本身没有状态机组件，但它受到了我在一家公司定制的状态机中重构的模式的启发。让我们假设我正在构建一个类来编写我自己的个人工作流，并且我正在实现我如何`make_pets_happy` …
的方法

```
class Me < Person
  # def initialize ... end

  def make_pets_happy(pets = self.pets) # mebbe I'm takin' care of other ppls pets idk
    pets.each do |pet|
      water = Water.new # if only creating water were this easy in real life
      case pet.class.to_s
      when "Dog"
        food = DogFood.new                   # always dogfood your code, if you can
        bed = pet.bed.good_condition? ? pet.bed : DogBed.new
        toy = BALLBALLBALLBALLBALL.new      # OMG A BALL
        rawhide = Rawhide.new
        pet.feed(food)
        pet.play(toy)
        pet.fill_water(water)
        pet.reward(rawhide) if pet.goodgirl? # always returns `true` for `Dog`
                                             # aliased to `#goodboy?` on `Pet` and `#gooddog?` on `Dog`
        pet.sleep(bed) if pet.tired?
      when "Cat"
        food = FancyFeast.new
        bed = pet.caretaker.bed
        self.bed = CatBed.new # I... I guess I can sleep here...
        toy = Catnip.new
        world = $world        # grab global `world`
        pet.feed(food)
        pet.play(toy)
        pet.fill_water(water)
        pet.reward(world)  if pet.goodgirl? # returns `true` while cat thinks it's actually *your* owner.
        pet.sleep(bed) if pet.tired?
      when "Bird"
        food = BirdSeed.new
        bed = nil # birds sleep standing up, nerd.
        toy = WhateverBirdsPlayWith.new # I dunno 
        pet.feed(food)
        pet.play(toy)
        pet.fill_water(water)
        pet.sleep(bed) if pet.tired?
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于没有受过训练的人来说，它可能看起来是一个非常大的方法，但是实际上，这是一个可维护性的灾难。假设这个行为树需要更新，我突然不得不担心我的`budget`，它们被定义为`budget = self.wallet.funds`。代码都是命令式的，一行一行地硬编码，所以为了考虑新的`budget`变量，我需要确保我找到了每一行初始化一个新对象的*，或者在它下面添加另一行来获取`budget -= whatever_object_was_initialized.price`，或者将`budget`作为参数传递给`::new`。如果我错过了一次调用，我要么是在每次初始化该对象时数错了我所花的钱，要么是因为没有付钱就从商店里拿了东西而被指控犯了轻罪(别名为`#shoplifting`)。*

而且，如果我开始照顾`Hamster` s，`Lemur` s，或者`Cuttlefish`呢？我将不得不在这个 case 表达式中添加另一个代码块，使得整个条件在这个过程中变得更加脆弱。控制这种混乱的方法是大多数计算机科学课程直到期末才教授的:多态性。

```
def make_pets_happy(pets = self.pets)
  pets.each { |pet| pet.fulfill(self.wallet.funds) }
end 
```

Enter fullscreen mode Exit fullscreen mode

这就是这个方法所需要的。在一行中，我将我的`budget`约束传递给一个方法，该方法定义在我可能负责的每个 pet 类上，上面的`case`的各个分支包含在其中，并从`Me`中抽象出来。`Me`这些都不用担心。`Me`应该只担心`Me`。… …不是…不是“我”我，我应该担心我周围的世界…你明白我的意思，不要假装你不明白。 <sup id="fnref1">[1](#fn1)</sup>

### 4。“等等，我刚才读了什么？”

我长话短说，因为这篇文章开始变得很长了…我担心的最后一个常见重构是解析一个表达式或语句实际上在做什么有多容易。我不知道你是怎么想的，但是长布尔表达式和逆布尔逻辑是我作为一名软件开发人员最痛苦的两个问题。

让我们想象一下某个[活动记录](https://guides.rubyonrails.org/active_record_basics.html)模型上的`#valid?`方法:

```
def valid? 
  state.in?(['active', 'pending', 'finished', 'drafted', 'published', 'deferred', 'unrequited'])
    && user_id.present? && user_id < 5000
    || state.in?(['finished', 'drafted', 'published'])
    && data['status'].between?(200, 299)
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我盯着它看足够长的时间，我可能会猜测这是某种 HTTP 请求，并且`#valid?`试图确保它是由特定用户(可能是管理员)执行的，并且处于可识别的状态，或者它是由普通用户发出的请求，并且处于类似完成的状态，并且请求状态返回到 HTTP 成功状态范围内。这些都是我可以编码成离散方法的东西，这些离散方法是这个表达式的部分的解析布尔。我还应该将这里的文字编码为它们各自类上的常量

```
COMPLETED_STATES = %w[finished drafted published]
RECOGNIZED_STATES = COMPLETED_STATES + %w[active pending deferred unrequited]

def valid?
  admin_request_in_recognized_state?
    || successful_complete_request?
end

def admin_request_in_recognized_state?
  @state.in?(RECOGNIZED_STATES) 
    && @user_id.in?(User::ADMIN_IDS)
end

def successful_complete_request?
  @state.in?(COMPLETED_STATES) && JSON.parse(@data)['status'].success?
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论，最后。

重构不仅仅是关于代码的清晰性和可读性。在 WWDC 2018 上，戴夫·亚伯拉罕斯发表了一篇演讲，讲述了[如何从循环中抽象出算法](https://developer.apple.com/videos/play/wwdc2018/223/)不仅提高了代码可读性，还提高了代码*的性能*(如果视频不能播放，可能只能在 Safari 中播放...如果是这样的话请告诉我，我会尽我所能让它成为一个更通用的编解码器)。你不能朝着更快的编码实现模式努力，直到你开始考虑*抽象的*编码模式，并且为了抽象你的代码库中的大部分代码，你必须开始考虑如何 ***重构*** 你的代码。

编码是人之常情；要重构？良好的...剩下的你都知道了。😉

感谢您花时间阅读这些内容！

* * *

1.  但是如果你不明白我的意思，请在评论中提问，我会很乐意描述背后的逻辑。 [↩](#fnref1)