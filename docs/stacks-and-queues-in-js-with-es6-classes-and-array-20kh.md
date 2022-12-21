# 带有 es6 类和数组的 JS 中的堆栈和队列

> 原文：<https://dev.to/dilantha111/stacks-and-queues-in-js-with-es6-classes-and-array-20kh>

有了 es6 特性，我们可以使用 extends 关键字来扩展现有的类。说到堆栈和队列，我们没有特定的关键字或数据结构，所以我们必须自己创建它们。但是如果我们仔细观察 Array.prototype，我们会发现像 pop 和 push 这样的方法已经存在。所以对于堆栈，我们可以像下面这样:

```
class Stack extends Array {
    peek() {
        return this[this.length -1];
    }

    isEmpty() {
        return this.length === 0;
    }
} 
```

那么我们这里有什么栈类:

*   推送(来自 Array.prototype)
*   pop(来自 Array.prototype)
*   peek(我们在堆栈上实现了这一点)
*   isEmpty(我们在堆栈上实现了它)
*   Array.prototype 上的长度属性

*问题！！！假设 Array.prototype.length 很丑，你会如何在 Stack 上实现一个更漂亮的 size()方法？*

```
size() {
 return this.length;
} 
```

会成功的。但我认为长度足够了。我们可以这样使用这个堆栈:

```
const stack = new Stack();
stack.push(1);
stack.push(2);
stack.pop();
stack.isEmpty();

const stack1 = new Stack(1,2,3,4); 
```

## 队列

同样，我们可以编写一个队列类，如下所示:

```
class Queue extends Array {
    enqueue(val) {
        this.push(val);
    }

    dequeue() {
        return this.shift();
    }

    peek() {
        return this[0];
    }

    isEmpty() {
        return this.length === 0;
    }
} 
```

这样我们就有了:

*   使…入队
*   出列
*   偷看
*   isEmpty

我们添加到队列类的方法。我们可以像下面这样使用队列:

```
const queue = new Queue();
queue.enqueue(2);
queue.isEmpty();
queue.dequeue(); 
```

*如果你想阅读没有 es6 类和扩展数组类的堆栈和队列的替代实现，请参考这里:[https://dev . to/emmawedekind/stacks-vs-queues-in-JavaScript-4d1o](https://dev.to/emmawedekind/stacks-vs-queues-in-javascript-4d1o)T3】*

请让我知道，如果你认为，有一个更好的替代上述任何。我乐于接受建议。如果有任何不清楚的地方，请评论，我会尽力解释。非常感谢您的阅读。编码快乐！！！