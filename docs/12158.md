# 约束满足问题

> 原文：<https://dev.to/vorakl/constraint-satisfaction-problem-csp-1b6d>

[约束满足问题](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem) (CSP)是一类可以用来表示一大组现实世界问题的问题。特别是，它广泛用于人工智能(AI ),因为为公式化的 CSP 寻找解决方案可以用于决策制定。在解决问题方面，有[几个相邻的领域](http://crest.cs.ucl.ac.uk/readingGroup/satSolvingTutorial-Justyna.pdf)，它们都涉及*约束* : [SAT(布尔可满足性问题)，以及 SMT(可满足性模理论)](https://yurichev.com/writings/SAT_SMT_by_example.pdf)。

一般来说，为 CSP 寻找解的复杂度是 [NP-Complete](https://stackoverflow.com/a/313523/5673383) ，因为一个解可以相对较快地(在多项式时间内)被猜测和*验证*，SAT 问题(NP-Hard)可以转化为 CSP 问题。但是，这也意味着，没有已知的多项式时间*解*。因此,[开发解决 CSP 的有效算法和技术是至关重要的](http://www.cs.toronto.edu/~fbacchus/Papers/liu.pdf),并作为一个主题出现在许多科学研究中。

[最简单的一类 CSP](http://aima.cs.berkeley.edu/newchap05.pdf)是由一组*离散变量*(例如 x，y)*有限非空域*(例如 0 < X < =4，Y < 10)，和一组*约束*(例如 Y=X^2，X < > 3)定义的，这些约束涉及一些变量。有两个不同的相关术语:CSP 的*可能世界*(或*完全赋值*)是对所有变量赋值，而*模型*(或 CSP 的*解*)是满足所有约束的可能世界。

在题目内部，有一个编程范式——[约束编程](https://en.wikipedia.org/wiki/Constraint_programming)。它允许建立一个基于约束的系统，其中变量之间的关系以约束的形式陈述。因此，这定义了某些细节:

*   该范式并不告诉寻找解决方案要执行的特定步骤序列，而是声明解决方案的属性。
*   它通常以非定向计算为特征。当满足约束条件时，计算会根据变化的条件或变量值在系统中传播。

CSP 可以应用于解决许多领域中的许多现实世界的问题，如映射、分配、计划和调度、游戏(例如数独)、解方程组等。还有一些软件框架，比如 [python-constraint](https://labix.org/python-constraint) 和 [Google OR-Tools](https://developers.google.com/optimization/) ，这里仅举几个例子。