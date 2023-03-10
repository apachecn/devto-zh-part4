# 前端类比:闭包的咖啡——递归

> 原文：<https://dev.to/recss/front-end-analogies-closure-s-coffee-recursion-2f5e>

### 清洁车间

#### 递归

杰克是个洁癖者。不去管他，整个地方就会一尘不染。很好，但是，呃，我们实际上是在卖咖啡。

随着时间的推移，我们对他完成任务所需的时间有了一个很好的估计。给他一个严格的时间限制，我们都不会被逼得走投无路。或者被谋杀。

```
 "use strict";

const timeLimit = 45;
const cleaningTasks = [15, 10, 8, 5, 3, 25, 7];
const tasksCompleted = [];

// Jack's thinking: "I've only got 45 minutes?!"
// "I'll just go down the list, one by one."

const jackCleaning = (_tasks) => {
    return (_timeLimit) => {
        return (_tasksCompleted) => {
            console.log('_tasks', _tasks);

            let taskTimeChecker = _tasks.find(task => task <= _timeLimit);
            // "Find the first one, get it done."

            console.log('taskTimeChecker', taskTimeChecker);

            if (taskTimeChecker != undefined) {
                _timeLimit -= taskTimeChecker;
                console.log('_timeLimit', _timeLimit);
                // "Runnin' out of time."

                _tasksCompleted.push(taskTimeChecker);
                console.log('_tasksCompleted', _tasksCompleted);
                // "Another one bites the dust."

                _tasks.splice(_tasks.indexOf(taskTimeChecker), 1);
                // "Check that one off the list."
            }
            else {
                console.log(_tasksCompleted);
                return `_tasksCompleted: ${_tasksCompleted}`;
            }

            return jackCleaning(_tasks)(_timeLimit)(_tasksCompleted);
            // "All play and no work makes Jack a dull boy."
            // And he just keeps going down the list like a madman.
        }
    }
}

console.log( jackCleaning(cleaningTasks)(timeLimit)(tasksCompleted) );

// "_tasks" [15, 10, 8, 5, 3, 25, 7]
// "taskTimeChecker" 15
// "_timeLimit" 30
// "_tasksCompleted" [15]

// He's done one 15-minute task, and has 30 minutes left…

// "_tasks" [10, 8, 5, 3, 25, 7]
// "taskTimeChecker" 10
// "_timeLimit" 20
// "_tasksCompleted" [15, 10]

// "_tasks" [8, 5, 3, 25, 7]
// "taskTimeChecker" 8
// "_timeLimit" 12
// "_tasksCompleted" [15, 10, 8]

// "_tasks" [5, 3, 25, 7]
// "taskTimeChecker" 5
// "_timeLimit" 7
// "_tasksCompleted" [15, 10, 8, 5]

// "_tasks" [3, 25, 7]
// "taskTimeChecker" 3
// "_timeLimit" 4
// "_tasksCompleted" [15, 10, 8, 5, 3]

// "_tasks" [25, 7]
// "taskTimeChecker" undefined
// [15, 10, 8, 5, 3]
// "_tasksCompleted: 15,10,8,5,3"

// With only 4 minutes left, neither one of those tasks is short enough.
// Mr. Scrubbing Bubbles has to go back to slingin' beans. 
```

### 奖励课！

#### 变量名称

这些例子都有很好的名字，难道不容易理解吗？如果他们不是，我没有参与，你也不会看到我。

#### 在出去的路上，我们经过一块牌子，上面写着:

A.总是
B .被
C .编码