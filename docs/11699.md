# 代数效应——你可以摸摸这个！

> 原文：<https://dev.to/temporal/algebraic-effects-you-can-touch-this-1nb7>

丹·阿布拉莫夫(因[反应](https://reactjs.org/)而出名)的一篇[文章今天](https://overreacted.io/algebraic-effects-for-the-rest-of-us/)在互联网上流传。它向读者介绍了一套名为“代数效应”的理论著作，提供了一个概述和一组例子，说明如何在 JavaScript 的假想扩展中实现这些思想。

这篇文章中让我吃惊的是两件事。首先，这篇文章声称这一领域的工作仍然是理论性的，“只有一些专门为探索这一想法而创建的语言支持”，而且肯定不是生产就绪。第二，我以前见过类似本文示例的代码——事实上，我经常编写类似的代码，无论是在专业还是业余环境中。给出的例子是一个 JavaScript 伪代码版本的 **Common Lisp 的条件和重启系统**。

老实说:我不熟悉“代数效应”这个名称下的工作，尽管我知道它的范围比这里和原始文章中描述的要大(见本文末尾的注释)。然而，Dan 的文章描述了实际应用中的一个子集，所以这是本文的重点。

# 条件和重启

条件和重启有点像主流语言中的 try/throw 异常处理，只是功能更强大。它不仅仅是为了处理错误，而是任何类型的流，其中调用堆栈上下的一些通信必须发生。

引用*实用普通话* 中[精彩介绍:](http://gigamonkeys.com/book/beyond-exception-handling-conditions-and-restarts.html)

> 条件系统比异常系统更灵活，因为条件系统不是在发出错误信号的代码和处理错误的代码之间提供两部分的划分，而是将责任分成三部分- *发出*条件信号，*处理*条件，以及*重启*。
> 
> (…)您可以使用条件系统来允许低级函数在解析日志文件时检测到问题并发出错误信号，允许中级代码提供几种可能的方法来从这种错误中恢复，并允许应用程序最高级别的代码定义一个策略来选择要使用的恢复策略。

在这里我不会尝试总结 PCL，但是我想介绍一些词汇:

*   **条件**——是一个代表某种“情况”的对象——即发生了值得注意的事情，但不一定是错误
*   **信号**——引用来自 CLHS 的[，“v .使用标准协议宣布，已经检测到由*条件*表示的特定情况”](http://www.lispworks.com/documentation/HyperSpec/Body/26_glo_s.htm)
*   **处理程序** -一个接收到*条件*的函数，并且能够以某种方式处理它(例如，通过调用*重启*)，或者拒绝并且仅仅传递*条件*
*   **重启** - [再次引用](http://www.lispworks.com/documentation/HyperSpec/Body/t_rst.htm#restart)，“表示可以被调用来执行某种形式的恢复动作的函数”

在 Lisp 之外，条件通常被称为异常对象，信令由`throw`完成，重启和处理程序被捆绑在一起作为`catch` & `finally`块。

要了解更多细节，请阅读上面链接的 PCL 第一章。

## 错误处理示例

这里不打算重复原文；反正真的很值得一读。相反，我要做的是向您展示如何在 Common Lisp 中完成来自的示例，其中它不是伪代码，而是一个真正的、完全受支持的 ANSI 标准化功能。

```
;;; Bookkeeping, to make the example compile and run.
(define-condition ask-for-name () ()
  (:documentation "A mock condition that means a name needs to be provided."))

(defun name (user)
  "Reader for name of `USER'."
  (car user))

(defun friend-names (user)
  "Reader for a list of names of friends of `USER'."
  (cdr user))

(defun (setf friend-names) (new-value user)
  "Setter for `FRIEND-NAMES'."
  (setf (cdr user) new-value))

;;; A helper macro wrapping an idiom allowing to do a test and request a new value if test fails.
(defmacro ensure (test place condition)
  "If the `TEST' fails, signal a `CONDITION', with a restart `USE-VALUE' ready for a new value."
  `(restart-case
       (unless ,test
         (signal ,condition))
     (use-value (new-value)
       (setf ,place new-value))))

;;; Implementation of the first example from the original article
(defun get-name (user)
  (let ((name (name user)))
    (ensure (not (null name)) ;Just NAME would suffice, but spelling it out for clarity.
      name
      'ask-for-name)
    name))

(defun make-friends (user-1 user-2)
  (push (get-name user-2) (friend-names user-1))
  (push (get-name user-1) (friend-names user-2)))

(let ((ricky (cons nil nil))
      (bubbles (cons "Bubbles" nil)))
  (handler-bind ((ask-for-name (lambda (c) (use-value "Ricky" c))))
    (make-friends ricky bubbles)
    ;; Let's return the two objects for the sake of REPL output.
    (list ricky bubbles)))

    ;;; REPL output:
    ((NIL "Bubbles") ("Bubbles" "Ricky")) 
```

零件:

```
if (name === null) { name = perform 'ask name'; } 
```

由`ensure`表单实现，该表单执行测试并确保名为`use-value`的重启就位，以从外部设置传递位置的值(如变量)。这个小工具本质上是一个简化的普通 Lisp 的`assert`宏，除了后者强迫你交互地指定新的值(事实上，你可以通过将`ensure`改为`assert`，将`(use-value "Ricky" c)`改为`(continue c)`来重写这段代码，以便与 Lisp 的交互调试器一起工作)。

`handle (effect) { ... }`部分完全由`handler-bind` Common Lisp [form](http://clhs.lisp.se/Body/m_handle.htm) 处理——它的工作是绑定函数来处理来自它所封装的代码的特定信号。你可以看到它符合我们之前定义的`ask-for-name`条件，为了处理它，它调用了`use-value`。`use-value`是一个用于调用名为`use-value`的重启的通用 Lisp 内置函数(这并不罕见)，但是如果没有提供这样的内置函数，您需要如下重写处理程序绑定:

```
(handler-bind ((ask-for-name (lambda (c)
                               (let ((restart (find-restart 'use-value c)))
                                 (when restart
                                   (invoke-restart restart "Ricky"))))))
  (make-friends ricky bubbles)
  ;; Let's return the two objects for the sake of REPL output.
  (list ricky bubbles)) 
```

也就是说，您可以以编程方式找到并调用在条件被发出时安装的任何重启。Common Lisp 只是为常见的重启`abort`、`continue`、`muffle-warning`、`store-value`和`use-value`提供了一个简化的功能接口。

## 超越错误

如前所述，条件/重启系统不仅仅用于错误处理。文章中的第二个例子演示了函数名的动态绑定，这可以在 Common Lisp 中以不同的方式完成(可以说应该这样做)，尽管需要做一些工作。clo jure——另一个 Lisp——为此提供了一个很好的内置工具: [with-redefs-fn](https://clojuredocs.org/clojure.core/with-redefs-fn) 。

因此，让我来描述一个我最初在 HN 上发布的[示例，关于如何使用条件和重启来实现进度报告和中止长时间运行的计算，可能是在交互/ GUI 环境中。](https://news.ycombinator.com/item?id=20513136)

```
(define-condition progress ()
  ((amount :initarg :amount
           :reader amount
           :documentation "How done is the operation, [0..1]."))
  (:documentation "A condition signaled by long operations that report progress."))

(defun process-partial-data (data)
  (declare (ignore data))
  ;; Some data processing here.
  )

(defun process-data (data)
  (restart-case
      ;; Main flow
      (loop
         ;; Report that we've started
         initially
           (signal 'progress :amount 0)

         ;; Perform the data processing
         with total = (length data)
         for datum in data
         for i below total
         do
           (process-partial-data datum)
           (signal 'progress :amount (/ i total))

         ;; Report that we're done
         finally
           (signal 'progress :amount 1)
           (return :done))
    ;; Restart flow
    (abort-work ()
      (format *trace-output* "Aborting work!")
      :failed))) 
```

我们函数的“核心”是循环形式。您会注意到它通过发出一个`progress`条件来报告它的进度，如果没有安装处理程序，这基本上是一个空操作(不像抛出一个异常)。“肉”被包装成`restart-case`的形式，以提供一种叫做`abort-work`的替代流程。

让我们看看一些 REPL 会话日志(`->`表示返回的结果，以区别于打印输出)。第一，常规使用:

```
CL-USER> (process-data '(1 2 3 4 5 6))
-> :DONE 
```

让我们通过实际监听`progress`条件来模拟一个 GUI 进度条:

```
CL-USER> (handler-bind ((progress (lambda (p) (format *trace-output* "~&Progress: ~F~%" (amount p)))))
           (process-data '(1 2 3 4 5 6)))

Progress: 0.0
Progress: 0.0
Progress: 0.16666667
Progress: 0.33333334
Progress: 0.5
Progress: 0.6666667
Progress: 0.8333333
Progress: 1.0
-> :DONE 
```

让我们假设用户点击了大约 50%的进度标记，来模拟用户按下“取消”按钮。我们可以通过编程调用`abort-work` restart 来实现:

```
CL-USER> (handler-bind ((progress (lambda (p) (format *trace-output* "~&Progress: ~F~%" (amount p))
                                                (when (>= (amount p) 0.5)
                                                  (invoke-restart 'abort-work)))))
             (process-data '(1 2 3 4 5 6)))
Progress: 0.0
Progress: 0.0
Progress: 0.16666667
Progress: 0.33333334
Progress: 0.5
Aborting work!
-> :FAILED 
```

正如[在我最初的例子中指出的](https://news.ycombinator.com/item?id=20515543),在实际使用中，你会想把这个机制隐藏在一个宏中。举个例子，写一个非常简单:

```
(defmacro dolist-with-progress-noted ((datum data return) (&key on-abort) &body body)
  (alexandria:once-only (data)
    (alexandria:with-gensyms (total i)
      `(restart-case
           (loop
              initially
                (signal 'progress :amount 0)

              with ,total = (length ,data)
              for ,datum in ,data
              for ,i below ,total
              do
                ,@body
                (signal 'progress :amount (/ ,i ,total))

              finally
                (signal 'progress :amount 1)
                (return ,return))
         (abort-work ()
           ,on-abort))))) 
```

现在，下面的代码扩展到了上面的原始示例:

```
(defun process-data (data)
  (dolist-with-progress-noted (datum data :done)
      (:on-abort (progn (format *trace-output* "Aborting work!") :failed))
    (process-partial-data datum))) 
```

# 离别的思念

在 HN 线程上有人指出,代数效应作为一个概念超出了普通 Lisp 所能支持的范围。所以可能是；我不太了解那里理论工作的范围。缺少的成分被暗示为“延续”，这在 Common Lisp 中是不支持的。然而，[方案](https://en.wikipedia.org/wiki/Scheme_(programming_language))口齿不清家族**有**延续。而且显然[条件](https://www.gnu.org/software/mit-scheme/documentation/mit-scheme-ref/Error-System.html#Error-System)。在 Lisp 的最高传统中，他们应该能够结合任何来自代数效果工作的聪明想法。

所以，事实证明，在一个生产就绪的环境中，你绝对*可以*接触到这个，并且可以在过去的 30 多年里([或更久](https://news.ycombinator.com/item?id=20514404))。我很高兴看到 JavaScript 社区重新发现了编程艺术中被遗忘的技术，但是在人们开始为 JS transpilers 编写自己的 DSL 之前，我恳求您——请至少看一看它是如何在实用语言中成功完成的，并注意他们的社区所学到的经验。

(原载于[我的博客](http://jacek.zlydach.pl/blog/2019-07-24-algebraic-effects-you-can-touch-this.html)。我应该向谁请求 dev.to 来启用公共 Lisp 代码的语法高亮显示？)