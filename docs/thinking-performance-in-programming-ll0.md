# 编程中的思维表现

> 原文：<https://dev.to/t4christ/thinking-performance-in-programming-ll0>

作为一名程序员，我们已经编写了理想地解决问题的代码，但是当解决大量问题时，我们是否注意到了性能，或者我们是否只是对“我的代码有效”感到满意。我们可以说，就我的代码而言，这就是全部。不要止步于此，把你的思维扩展到“我的代码能工作吗”到“我的代码能解决大规模的问题吗”这就是在性能层面上思考的地方。对于那些不是 python 程序员的人来说，这也适用于你，因为你在编码中投入了更多的思想。

当我们考虑大 O 时，我们只能考虑性能。哈哈哈哈，你想知道我说的“考虑大 O”是什么意思，对吗？别担心，我说的是大 O 符号。大 O 符号仅仅意味着描述算法的性能或复杂性。大 O 符号处理解决性能问题，大性能问题就是大 O 出现的地方。我强烈推荐你用这个简化的指南[阅读 big o notation。理论讲够了，让我们看看我在实践中的意思。我们将会看到一个简单的 python 数学问题。假设我们想用 python 对从 1 到 100 的所有数字求和，我们可以简单地做到这一点。](https://guide.freecodecamp.org/computer-science/notation/big-o-notation/)

def sum number(number):
sum = 0
for num in number:# #循环遍历一个数字数组
sum +=num ##将数字加到初始和值上
返回 sum ##循环后返回 sum
number = range(100)
sum number(number)

输出:
4950

理想情况下，我们已经用这几行代码解决了这个问题，但这是否意味着这段代码可以很好地处理大量代码。让我们看看这段代码在解决一亿(100，000，000)个相同问题时的性能。让我们来看一下解决这个问题的 3 种不同方法，并选择表现最好的方法。

解决方案 1

导入时间

def sum number(number):
sum = 0
for n in number:
sum+= n
返回 sum

number = range(100)
start _ time = time it . default _ timer()
result = sum number(number)
print(result，time it . default _ timer()-start _ time)

当我们看一下上面的解决方案时，它花了 15 秒来解决问题，考虑到大小，这不是一个真正的好解决方案。

让我们看看使用 python 内置的 sum 方法解决相同大小的相同问题的第二种方法。

解决方案 2

导入时间

def sumNumber(数字):
返回 sum(数字)

number = range(100000000)
start _ time = time it . default _ timer()
sum number(number)
print(time it . default _ timer()-start _ time)

看一下上面的解决方案 2，它花了 5 秒钟来解决同样的问题，我认为它更好，但对我来说它仍然不像是最好的解决方案。我们还可以做得更好，你不觉得吗？最后让我们看看第三种解决方案。

解决方案 3

导入时间

def sumNumber(数字):
返回数字/2 *(1+数字)

number = 10000000000
start _ time = time it . default _ timer()
sum number(number)
print(time it . default _ timer()-start _ time)

哇，我真不敢相信我的眼睛所看到的。这真是太神奇了，这个解决方案在大约 0.00002 秒内解决了问题。与 15 秒和 5 秒的解决方案相比，差距太大了。这就是我所说的在编码时考虑性能的意思。无论你想用你选择的编程语言解决什么问题，你都可以考虑性能，你会对结果感到惊讶。非常感谢你的参与。如果您有任何问题或补充，请留下您的评论。