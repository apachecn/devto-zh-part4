# 使用 Clojure 模拟护照 Seva Kendra

> 原文：<https://dev.to/vedang/simulating-the-passport-seva-kendra-using-clojure-5df6>

一年前，我去普纳蒙德瓦的 PSK 护照中心更新护照。当时，政府已经修改了这一程序，使之成为一件简单的、不费力的事情。不幸的是，我碰到了系统中的一个边缘情况，花了比预期长得多的时间来完成。我在那里呆了将近 4 个小时。我利用这段时间观察 PSK 的行为，并思考如何改善申请人的体验。我认为这是一个需要解决和写出来的有趣问题。

最近，我妻子在 PSK 预约更新她的护照，这为我写这篇文章提供了灵感。一年后，我们在这里谈论 PSK 的护照更新程序。

## 问题陈述

我先给你描述一下过程。

*   PSK 每隔 15 分钟就有预约时段，每个时段大约有 25 人。
*   一旦你进入 PSK，有 4 到 6 个柜台验证你的文件。
*   验证时，您会被分配一个唯一的令牌号。我们稍后会谈到这一点。
*   代币号码显示在电子显示板上。告示牌指示这个人应该去哪个柜台。你要在等候区等候，看着显示屏。你很快就会被安排在柜台前，PSK 的一名员工会在这个过程的特定阶段帮助你。
*   这个过程有 3 个阶段。第一个是“生物识别”(A 阶段)。在这个柜台，PSK 的员工会收集你的指纹并给你拍照。您的在线表单将根据此信息进行更新。有 36 个柜台为这个阶段服务(A-1，A-2...A-36)。
*   第二阶段是“形式检查”(阶段 B)。此时，PSK 的员工会检查您表单中的详细信息。如果他发现任何问题，他会把你带到更正柜台。在我访问期间，A-34 号、A-35 号和 A-36 号柜台被留作更正之用。修正之后，你必须回到这个阶段。有 12 个计数器(B-1，B-2...B-12)。
*   最后一个阶段是“表格复查”(阶段 C)。此时，代理会仔细检查前面计数器的工作，并接受您的表单。如果有任何更正，您必须回到更正柜台并重新开始。检查完表格后，您的补发申请将被处理。你可以自由离开 PSK 回家了。有 10 个计数器(C-1，C-2...C-10)。
*   回到代币号码。这些是 N-10、S-4 等形式。字母表代表申请人类别。这些类别如下:
    *   **正常(用 N 表示):**大部分人都属于这一类。
    *   **老年人(用 S 表示):**60 岁以上的人
    *   **Tatkal(由 T 代表):**针对希望快速处理护照的人(并为此支付了额外费用)
    *   **需要警方许可(用 P 表示):**需要警方许可的人(可能是因为有犯罪记录，或者在政府敏感部门工作)。
*   在处理他们的申请时，除了“正常”类别之外的类别具有更高的优先级。出于本文的目的，我假设 P 的优先级最高，其次是 S、T 和 n。

在这篇文章的其余部分，我们将如上所述构建这个系统，看看我们能否修复其中的缺陷。我们将使用 Clojure 来编写解决方案。Clojure 的并发原语非常棒，帮助我以优雅和可读的方式为这个系统建模。随着我们的进行，我将简要地解释这些原语。Eric Normand 在 Clojure 中写了一本关于[理解并发原语](https://purelyfunctional.tv/guide/clojure-concurrency/)的优秀指南， [Rich Hickey](https://twitter.com/richhickey) 也有一篇关于这个的精彩演讲。我向感兴趣的读者强烈推荐这两个资源。这篇文章的重点是使用这些工具来实现一个重要的系统。

### 那么系统有什么问题呢？

首先，让我们来解决这个问题。这个系统的问题是申请人不知道他什么时候会被安排与代理人见面。他必须一直盯着显示板。例如，如果你是 N-30，阶段 A 花了你特别长的时间，其他人已经越过你进入下一个阶段(B 和更远)。显示板可以读取 N-41、N-42 等在柜台 B-1、B-2 等处。然而，这并不意味着接下来会出现 N-30。在 N-30 出现之前，显示板可能会一直显示到 N-60。由于无法确定你的号码何时会出现，你别无选择，只能一直盯着棋盘。过了一段时间后，这真的很烦人。

以下是处理这个问题的一些方法:

1.  不要在阶段之间使用简单的队列，而是使用优先级队列。这意味着，即使 N-30 在阶段 A 花费了很长时间，并且董事会已经转移到阶段 B 的 N-40 和以上，一旦 N-30 完成阶段 A，他将被转移到 B 的队列的顶部。这样，申请人可以看着董事会，总是知道他是否将是下一个。
2.  在每个阶段之间生成新的令牌号。这将使申请人清楚地知道在任何给定的时间点排在他前面的人数。
3.  为这个人提供一个新的显示板，他可以在那里输入他的令牌，并查看他在该阶段的队列中的位置。

我们将在模拟程序中看到这些操作。

## 代表关于 PSK 的所有信息

描述 PSK 行为的一切都可以用代码捕捉。对于我们的模拟，数据如下所示:

```
(def  stages  "The various stages in the PSK, and transition from one stage to the other.

  - `processing-time-range` represents the amount of time spent at the
  counter in this stage (in mins).
  - `next` represents the next stage for the person.
  - `counters` represent the number of counters/agents serving this stage."  ;; Actual Values:  ;; 4 Doc verification Counters  ;; 33 counters for Biometrics  ;; 12 for form checking  ;; 10 for final checking  ;; 3 for corrections  ;; Using different values here so that the display board is  ;; human-readable.  {::enter  {:next  ::doc-verification}  ::doc-verification  {:next  ::biometrics  :counters  10  :display-str  "0-"  :processing-time-range  [1  5]}  ::biometrics  {:next  ::form-check  :counters  4  :display-str  "A-"  :processing-time-range  [3  15]}  ::form-check  {:next  ::final-check  :counters  3  :display-str  "B-"  :processing-time-range  [2  4]  :failure  ::corrections}  ::final-check  {:next  ::exit  :counters  2  :display-str  "C-"  :processing-time-range  [2  4]}  ::corrections  {:next  ::form-check  :counters  1  :display-str  "D-"  :processing-time-range  [5  15]}})  (def  waiting-room-capacity  500)  ; max number of waiting people  (def  total-capacity  "number of people that can be in the PSK at max"  (apply  +  waiting-room-capacity  (keep  :counters  (vals  stages))))  (def  processing-batch-size  "no of people entering the center at one time."  25)  (def  new-batch-in-mins  "time between one batch and the next"  15)  (def  stage-status  "For the given stage, the map of possible states that a person can be
  in."  [;; Person is in the waiting area, looking at the display to see  ;; when he's called to a counter.  ::waiting  ;; Counter is assigned to the person  ::in-process  ;; Person is done with the counter and heading to the next stage.  ::done]) 
```

我们使用一个简单的图- `stages` -来表示 PSK 中的所有阶段、每个阶段的计数器数量、每个阶段的时间量以及这个阶段之后的下一个阶段。一个人要么在等待(看着显示板)，要么在柜台，要么完成了一个阶段(`done`触发移动到下一个阶段)。从这个人的角度来看，他不是在等待，就是在柜台前。

## 让人进入 PSK，并为他们生成代币号码。

我们要构建的第一件事是令牌生成器，我们将使用最直观的 Clojure 并发原语:`atom`。

### Clojure 并发原语- Atoms

原子在读/写单个数据时很有用(可能跨多个线程)。这是共享资源的常见情况，我在编写的大多数并发代码中都使用了原子。

```
(def  token-generator  "Give the next token number to the applicant."  (atom  {:normal  0  :senior  0  :tatkal  0  :police-clearance  0}))  ;; Use the token generator to get the next token as follows:  (comment  (get  (swap!  token-generator  update  person-type  inc)  person-type))  ;; swap! is a way to atomically change the value held by the atom.  ;; This does an internal compare and set operation, and thus the function  ;; passed in to manipulate the value - in this case update - may be  ;; called multiple times. This function should be free of side effects. 
```

Clojure 提供了更强大的功能——`ref`——当您想要处理一起变化的多段数据时。我们将在接下来的小节中看到它的实际应用。

如果我们选择一些权重来表示某个申请者属于某个类别的概率，我们可以写一些代码来随机生成申请者。相关代码这里是[这里是](https://gist.github.com/vedang/969a726e1f49f5fc550268a22c4e4b0d#file-psk-clj-L87-L131)。我们现在有办法给每个进入 PSK 的新人分配递增的代币号码。

### Clojure 并发原语-未来

我们将使用另一个 Clojure 并发原语——一个`future`——不断地将人们移入 PSK。未来对象调用在不同线程中提供给它的主体。

在这种情况下，我们在一个新线程中开始了一个无限循环。这段代码创建一些人(代表进入 PSK 的人)，休息一会儿，然后重复。我们将使用我们方便的工具——原子——来控制何时停止循环:

```
(def  working-hours?  "Am I working right now?"  (atom  false))  (defn  let-people-through  "Send people into the PSK in batches as defined by
  `processing-batch-size` and `new-batch-in-mins`. Note that in our
  code we use seconds to represent minutes."  [active-applicants  done-applicants]  (future  (loop  []  (if  @working-hours?  (do  ;; let new people through  ;; <logic goes here> ...  ;; then sleep for a while  (Thread/sleep  (*  1000  new-batch-in-mins))  ;; repeat  (recur))  (ctl/info  "[Entry] Working hours are over! Closing Shop! Come back later!"))))) 
```

## 将人排队，模拟每个阶段完成的工作

现在人们正在进入 PSK，我们需要一种方法让他们在各阶段之间排队。我们还想为每个柜台完成的工作编写一个模拟。如问题陈述中所述，PSK 在每个阶段之间使用简单的 FIFO 队列。我们将使用`LinkedBlockingQueue`数据结构来表示这些。这个数据结构是由久经考验的`java.util.concurrent`包提供的。在需要的时候降级到 Java 是 Clojure 的额外优势！

```
(defn  create-kendra-queues  "Given the counter-types / `stages` in the kendra, create the
  appropriate queues."  [kendra-stages  q-capacity]  (let  [queues-we-need  (->  kendra-stages  keys  set  ;; Remove the stages where no queue of  ;; people is needed.  (disj  ::enter  ::exit))]  (reduce  (fn  [m  s]  (assoc  m  s  (LinkedBlockingQueue.  q-capacity)))  {}  queues-we-need))) 
```

柜台工作包括以下内容:

1.  挑选下一个排队的人。
2.  通过在显示器上显示他的代币号码来叫他到柜台。
3.  处理人，做工作。
4.  将此阶段标记为完成。这将使他进入下一个队列。

我们可以用如下代码表示它:

```
(defn  process-applicant  "Get a person from the queue. Process this person as per the rules
  of the counter."  [psk-agent  my-queue  notice-board]  (if  @working-hours?  (if-let  [person  (.poll  my-queue  1  java.util.concurrent.TimeUnit/SECONDS)]  (let  [stage  (:type  psk-agent)  stage-config  (:config  psk-agent)]  (call-person-to-counter  stage  notice-board  psk-agent  person)  (process-person  stage  stage-config  psk-agent  @person)  (mark-processing-as-complete  stage  notice-board  psk-agent  person)  (send-off  *agent*  process-applicant  my-queue  notice-board)  (assoc  psk-agent  :last-processed  (person-representation  @person)))  (do  (send-off  *agent*  process-applicant  my-queue  notice-board)  psk-agent))  (ctl/info  (format  "[Agent: %s] Working hours are over! Closing Shop! Come back later!"  (agent-representation  psk-agent))))) 
```

正如我们之前看到的，我们使用`working-hours?`原子控制代码的运行。我们在这里看到了一些新东西 Clojure 代理使用的`send-off`函数。暂时忽略这一点，我们将在看到 refs 和 transactions 后对此进行解释。

## 跟踪人和显示板

这个项目最难的部分是跟踪每个人的当前状态和显示板在每个瞬间的变化。这两个视图应该始终保持一致，因为在不同的阶段会同时处理多个人。Clojure 通过引用和事务使这变得非常容易。

### Clojure 并发原语- Refs(和事务)

Refs 可以被认为是指向可变存储位置的永久指针。使用事务中的`alter`、`ref-set`和`commute`功能，可以安全地更改存储的值——全部更改或根本不更改。Clojure 实现了一个软件事务内存系统 <sup id="fnref1">[1](#fn1)</sup> 并给了我们 A、C 和 I 著名的 ACID 属性 <sup id="fnref2">[2](#fn2)</sup> 。(因为它在内存中，所以没有耐久性)。在代码中使用这些事务对于任何有使用 DB 事务经验的人来说都是熟悉的。

实际上，更新值是这样的:

```
(defn-  call-person-to-counter  "Announce that person should come to the processing counter. Takes
  `person` and `notice-board` refs, performs a transactional update."  [stage  notice-board  psk-agent  person]  (dosync  (alter  person  assoc  :stage  stage  :stage-status  ::in-process  :psk-agent  (agent-representation  psk-agent))  (store-stage-change  person  stage  ::in-process)  (alter  notice-board  assoc  (person-representation  @person)  (agent-representation  psk-agent)))) 
```

看，妈妈，没有锁！在我看来，这比考虑用哪个锁来锁哪个数据要简单得多。让我们看看上面代码中的`store-state-change`函数。这是我编写的一个小型数据收集函数，用于计算每个人在每个阶段花费的时间。

```
(defn-  store-stage-change  "For the given `Person` ref, store the change to their stage for later analysis."  ([person  new-stage  new-status]  (store-stage-change  person  new-stage  new-status  (ct/now)))  ([person  new-stage  new-status  time-instant]  (let  [stage-log  (->StageHistory  new-stage  new-status  time-instant)]  (dosync  (alter  person  update  :stage-history  conj  stage-log))))) 
```

编写这个函数很简单:我们知道我们想要修改一个现有的人，所以我们将它包装在一个事务中。调用代码碰巧已经在一个事务中，但是 Clojure 会正确地处理这一点，并将所有工作折叠到一个事务中。从我们的角度来看，我们知道，无论何时调用这个函数，它都会安全而永久地修改这个人，并在其中存储阶段变化。

## 跨所有开放柜台处理人并发

难题的最后一块是在所有可用的计数器上同时处理人。这对于线程池来说很简单，但是 Clojure 提供了另一个我们可以使用的工具:`agent`。

### Clojure 并发原语-代理

代理是访问/改变可变状态的另一种方式，但是它们是以异步方式完成的。函数`send`和`send-off`将动作(函数)应用于代理保存的值。动作的返回值成为代理的新值。但是，这些操作在不同的线程中异步执行。Clojure 还保证按照提交的顺序执行。代理的价值在任何时候都是可检验的。

在我们的例子中，这允许我们将处理计数器表示为代理。代理的状态只是它的一个标识符。在幕后，每个代理都从线程池中产生一个线程并执行一个函数。这个函数从提供给它的队列中取出下一个人，处理他，并向当前代理发送另一个动作。我们已经看到了这个函数，但是为了清楚起见，让我再贴一次:

```
(defn  process-applicant  "Get a person from the queue. Process this person as per the rules
  of the counter."  [psk-agent  my-queue  notice-board]  (if  @working-hours?  (if-let  [person  (.poll  my-queue  1  java.util.concurrent.TimeUnit/SECONDS)]  (let  [stage  (:type  psk-agent)  stage-config  (:config  psk-agent)]  (call-person-to-counter  stage  notice-board  psk-agent  person)  (process-person  stage  stage-config  psk-agent  @person)  (mark-processing-as-complete  stage  notice-board  psk-agent  person)  (send-off  *agent*  process-applicant  my-queue  notice-board)  (assoc  psk-agent  :last-processed  (person-representation  @person)))  (do  (send-off  *agent*  process-applicant  my-queue  notice-board)  psk-agent))  (ctl/info  (format  "[Agent: %s] Working hours are over! Closing Shop! Come back later!"  (agent-representation  psk-agent))))) 
```

我们创建代理如下:

```
(defrecord  PSKAgent  [id  type  config])  (defn-  create-agents  "For the given `agent-type`, create the given `num` of agents."  [agent-type  stage-config  num]  (map  (comp  agent  (fn  [i]  (PSKAgent.  (inc  i)  agent-type  stage-config)))  (range  num)))  (defn  create-kendra-agents  "Given the `stages` and their config for the kendra, create the
  appropriate agents to work these counters."  [kendra-stages]  (let  [stages-with-counters  (->  kendra-stages  keys  set  ;; Remove the stages where no counter  ;; of agents is needed.  (disj  ::enter  ::exit))]  (mapcat  (fn  [s]  (let  [config  (get  kendra-stages  s)]  (create-agents  s  config  (:counters  config))))  stages-with-counters))) 
```

## 把一切联系在一起——主要功能

在我们的主函数`start-the-kendra!`中，我们将代码的所有部分联系在一起。对于不熟悉 Clojure 语法的人来说，注释解释了每个步骤的作用。

```
(defn  start-the-kendra!  "Setup our Passport Seva Kendra."  []  (let  [;; Create queues for the various stages, returns a map of  ;; stage-name -> queue  stage->queue  (create-kendra-queues  stages  total-capacity)  ;; Create all the agents  list-of-agents  (create-kendra-agents  stages)  ;; Create a display board for waiting members  notice-board  (ref  (sorted-map))  ;; Track all the active applicants  active-applicants  (ref  [])  ;; Track all the completed applicants (for debugging /  ;; historical data purpose)  done-applicants  (ref  [])]  (ctl/info  "[PSK] Welcome, today is a good day.")  ;; For each agent at each counter, start processing!  (doseq  [a  list-of-agents]  ;; Get the stage this agent is working at, and the queue of  ;; people for that stage.  (let  [s  (:type  @a)  q  (stage->queue  s)]  ;; Start processing people from the queue concurrently in  ;; independent threads.  (send-off  a  process-applicant  q  notice-board)))  ;; Start a continuous future for applicants to periodically enter  ;; the PSK.  (let-people-through  active-applicants  done-applicants)  ;; Start a helper process to move people from one stage to the  ;; other.  (move-people-through  stages  stage->queue  active-applicants)  ;; Return the data. We'll use this to monitor our system.  [notice-board  active-applicants  done-applicants])) 
```

我们还没有看到上面使用的`move-people-through` helper 函数。这是一个简单的`future`，它有规律地扫过所有的人，把一个人从一个阶段移到另一个阶段。

最初，我编写的代码使得每个代理都知道输入队列和输出队列。代理明白他必须从输入队列中挑选下一个人，并在完成时将该人移动到输出队列。我对此进行了重构，以表明围绕修改现有共享资源的现有并发代码添加函数是很简单的。`move-people-through`的代码看起来像这样:

```
(defn  move-people-through  "Review all the active applicants and move them into appropriate stages."  [kendra-stages  stage->queue  active-applicants]  (future  (loop  []  (if  @working-hours?  (let  [people  @active-applicants]  (doseq  [person  (->>  people  (group-by  (comp  :stage-status  deref))  ::done)]  (if  (=  ::exit  (get-in  kendra-stages  [(:stage  @person)  :next]))  (mark-applicant-process-as-complete  person)  (when-let  [next-stage  (get-in  kendra-stages  [(:stage  @person)  :next])]  (move-applicant-to-next-stage  stage->queue  next-stage  person))))  (Thread/sleep  guide-people-to-next-stage-ms)  (recur))  (ctl/info  "[Guide] Working hours are over! Closing Shop! Come back later!"))))) 
```

`mark-applicant-process-as-complete`和`move-applicant-to-next-stage`都是对人的微小事务更新:

```
(defn-  mark-applicant-process-as-complete  "Takes a `person` ref object and marks its processing as complete."  [person]  (let  [time-instant  (ct/now)]  (dosync  (alter  person  assoc  :stage  ::exit  :exit-time  time-instant  :total-time  (->  @person  :enter-time  (ct/interval  time-instant)  ct/in-seconds))  (store-stage-change  person  ::exit  ::done  time-instant))))  (defn-  move-applicant-to-next-stage  "Given a `person` ref and the next stage they should go to, move
  them to the stage. Does a transactional update."  [stage->queue  next-stage  person]  (dosync  (alter  person  assoc  :stage  next-stage  :stage-status  ::waiting)  (store-stage-change  person  next-stage  ::waiting))  (.put  (stage->queue  next-stage)  person)) 
```

我还增加了一个记账功能，让人们进入 PSK。此功能从有效申请者列表中删除已完成的申请者。这释放了 PSK 容量。我还将这些数据移到不同的列表中，因为浏览这些数据并寻找有趣的见解很有趣。

```
(defn-  book-keeping-for-applicants  "Remove all applicants who are completely done from
  `active-applicants`. Store them in `done-applicants` for
  book-keeping.

  *NOTE* : Since this goes through the entire collection, it is slow.
  Hence we run it when sending in new batches of people."  [active-applicants  done-applicants]  (dosync  (let  [[active-people  done-people]  (reduce  (fn  [[aa  da]  p]  (if  (and  (=  (:stage  @p)  ::exit)  (=  (:stage-status  @p)  ::done))  [aa  (conj  da  p)]  [(conj  aa  p)  da]))  [[]  []]  @active-applicants)]  (ref-set  active-applicants  active-people)  (alter  done-applicants  into  done-people)))) 
```

这里我们使用`ref-set`和`alter`函数来重置活动申请人的值，并修改已完成申请人的值。

## 跟我到目前为止？一些想法

*   Clojure 的并发原语让我很容易**思考**这个问题。我编写代码就像编写问题的高级伪描述一样，它工作得很好。我认为这种简化在处理并发代码时是一个巨大的好处。
*   编写和测试少量并发代码的能力是一大优势。对我来说，修改原始代码并围绕它设计实验很简单。
*   我们还没有真正看到它运行时的样子！让我们现在就做吧！

## 能看出问题吗？

让我们运行这个系统！我们正在设置一个小循环来展示板子。我们运行的计数器比实际的 PSK 少得多。这是为了使显示板可消耗。

[![A normal day at the Passport Seva Kendra.](img/5cfac88a0131cc7b1d578c2e68ed7fea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bZkakl96--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/poxjkwrqvkcz8zs5bel2.gif)

这个循环看起来不错，棋盘看起来是可预测的。当某人在某个阶段运气不佳，并且比一般人花更多的时间时，问题就出现了。让我们通过将某人标记为不幸运，并通过增加处理早期阶段的计数器的数量(这与现实相符)来重复运行

```
(def  unlucky-applicant?  "Introduce a little anarchy!"  (atom  #{"N-3"  "S-2"}))  (defn-  process-person  "Do the work for processing the given person. Takes a `person`
  object and not a ref."  [stage  stage-config  psk-agent  person]  (let  [processing-time*  (get-processing-time-for-stage  stage-config)  processing-time  (if  (@unlucky-applicant?  (person-representation  person))  ;; You will need more time because the gods  ;; are against you.  (*  10  processing-time*)  processing-time*)]  (Thread/sleep  processing-time))) 
```

这看起来是这样的:

[![N-3 is not having a good day.](img/be1be37b683b3fb76d71cf428b084a49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kOpMA7Je--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/litaffdjvwsc7n18928p.gif)

如你所见，当 N-3 完成阶段 0 时，已经有很多人领先了。n-40 目前正在第 0 阶段进行处理。他不知道自己在人群中的位置，必须时刻盯着显示板。

我将简单谈谈我们最初提出的三个解决方案。

### 解决方案 1:使用优先级队列

在每个阶段之间使用优先级队列解决了申请人不知道下一个是他的问题。但是，有两点需要考虑:

1.  优先级队列解决给定类别内的问题，但不解决跨类别的问题。你知道你是 B 柜台的下一个 N 类人，但你不知道那将是什么时候，因为所有 S/T/P 类人将首先被服务。
2.  这样做的一个潜在缺点可能是:如果某一组申请人在每个阶段总是很慢，那么在这个模型中，他们将降低 PSK 服务的平均人数(因为我们将优先为他们服务，而不是那些走在他们前面的人)。

在我们的代码中使用优先级队列是微不足道的。我们回到`java.util.concurrent`，把我们的`LinkedBlockingQueue`换成`PriorityBlockingQueue`。现在我们要做的就是提供一个比较器函数。这里的代码是我们也可以通过重复运行程序来生成跨人群的时间样本。这将使我们了解平均处理时间是否受到使用优先级队列的影响。

### 解决方案 2:每阶段新令牌数

使用新的令牌号码很好地解决了这个问题。由于一次又一次向申请人发放新代币的后勤困难，这种方法可能没有被使用。在现实世界中，我敢肯定，如果不仔细设计，这个过程可能会造成混乱。在我们的系统中，我们已经有了一个非常好的方法来获得下一个令牌数——我们的原子令牌生成器。实现该解决方案非常简单，留给读者作为练习！(这篇博文已经相当冗长了！)

### 解法三:“我在哪里？”估计等待时间

PSK 可以帮助申请人的另一种方式是提供单独的自助展示。申请人输入他的令牌号码，并开始查看在给定阶段有多少人在他前面。`LinkedBlockingQueue`和`PriorityBlockingQueue`都提供了一个`toArray`函数，该函数按顺序返回队列中的所有元素。

因为我们跟踪每个人在每个阶段花费的时间，所以我们也可以预测这个人需要等待的估计时间。这可能是一篇完整的博文 <sup id="fnref3">[3](#fn3)</sup> 。

我将此作为读者的练习:)

## 最后的想法

这个练习的完整代码可以在[这里](https://gist.github.com/vedang/969a726e1f49f5fc550268a22c4e4b0d)找到。要点末尾的[注释块](https://gist.github.com/vedang/969a726e1f49f5fc550268a22c4e4b0d#file-psk-clj-L530-L545)解释了如何针对 Clojure REPL 运行程序。请注意，这不是一个微不足道的模拟 PSK，我相信，整个代码实际上可以被他们使用的一小部分变化。我们尚未实施的事项列表:

1.  在现实世界中，我们需要将每个人的信息存储在数据库中。由于内存中的结构保证是一致的，这是我们可以通过定期读取所有申请人的信息并提交给 DB 来实现的(类似于`book-keeping-for-applicants`的工作方式)。
2.  我们还没有实现错误和更正流程。未能通过一个阶段可以表示为另一个状态(比如`::error`)。`process-applicant`代码将识别阶段的成功/失败并设置适当的状态。`move-people-through`需要一些小的改变来处理这个问题。我将此作为读者的一个练习。
3.  在现实世界中，我们需要为一个真实的 PSK 雇员构建输入，以通知我们处理已经完成。这不过是`process-applicant`中的一个循环，它检查 DB 以查看工作是否完成。

我很想听到关于这篇文章的反馈。有没有更好的实现方式？告诉我。我是否遗漏了 PSK 的用例，并实现了一个简单得多的问题的解决方案？一定要说！我提出的解决方案可能有我看不到的缺点，而且这个系统以这种方式建立可能有合理的原因。我很想了解我错过的现实世界的问题。

如果你有兴趣与 Clojure 一起工作，解决诸如估计等待时间等问题，[我们正在招聘](https://jobs.lever.co/helpshift/)！发送电子邮件至 jobs@helpshift.com 的[给我们。](//mailto:jobs@helpshift.com)

/非常感谢以下人员审阅本文初稿:Kapil Reddy、Kiran Kulkarni、Mourjo Sen、Suvrat Apte、Dinesh Chhatani。/

非常感谢 [Bhargava Chowdary](https://twitter.com/renderkid) 创作了这幅 PSK 的插图。

## 参考文献

*   Eric Normand 的帖子解释了所有 Clojure 并发原语:[https://purelyfunctional.tv/guide/clojure-concurrency/](https://purelyfunctional.tv/guide/clojure-concurrency/)
*   Rich Hickey 关于 Clojure 并发的演讲:[https://www.youtube.com/watch?v=nDAfZK8m5%5F8](https://www.youtube.com/watch?v=nDAfZK8m5%5F8)
*   原子:[https://clojure.org/reference/atoms](https://clojure.org/reference/atoms)
*   https://clojure.org/reference/refs
*   期货:[https://clojuredocs.org/clojure.core/future](https://clojuredocs.org/clojure.core/future)
*   经纪人:[https://clojure.org/reference/agents](https://clojure.org/reference/agents)

* * *

1.  STM:[http://en . Wikipedia . org/wiki/software % 5f transactional % 5f memory](http://en.wikipedia.org/wiki/Software%5Ftransactional%5Fmemory)↩

2.  acid:[https://en . Wikipedia . org/wiki/acid %5F(计算机% 5f 科学)](https://en.wikipedia.org/wiki/ACID%5F(computer%5Fscience)) [↩](#fnref2)

3.  估计等待时间:[https://en.wikipedia.org/wiki/Queueing%5Ftheory](https://en.wikipedia.org/wiki/Queueing%5Ftheory)↩