# 为什么你在写圣诞卡的时候会想使用数组

> 原文：<https://dev.to/calier/why-you-might-want-to-use-an-array-when-writing-your-christmas-cards-87>

免责声明:本博客包含代码片段，但你不必理解它们就能理解要点。

当我学习新的东西时，我经常试着用真实世界的例子来思考它。我经常发现自己在想‘我该如何向父母解释这件事？’这包括去掉行话，让不懂代码的人也能理解。如果你喜欢足球，并且你曾经试图用盐瓶、杯子和餐具来解释越位规则，你就会明白我的意思。

当我妈妈和爸爸做圣诞卡的时候，通常是我爸爸写，我妈妈告诉他写给谁。我们可以写一个非常简单的函数来显示这个*(如果你只是来这里寻求解释，现在把目光移开):*

```
const writeChristmasCard = name => {
  console.log("Merry Christmas " + name + "!")
}; 
```

这个函数是这样工作的:如果可以的话，画一张我妈妈在客厅里的照片，上面有名字。爸爸在餐桌旁，拿着卡片。妈妈对他喊了一个名字，他拿起一张卡片，用那个名字写了一条很好的信息。他们必须对每张卡重复这一过程:

```
writeChristmasCard("Spongebob");
// Merry Christmas Spongebob!

writeChristmasCard("Timothy");
// Merry Christmas Timothy!

writeChristmasCard("Uncle George");
// Merry Christmas Uncle George! 
```

所以所有的卡片都被写好了，但这是一个漫长的过程，包括相当多的叫喊。

如果你正在写卡片，你可能会有一个名单上的人要寄给他们，对不对？用编码语言来说，你可以称这个列表为数组。我们让妈妈像往常一样写名单，但不要依次喊出每个名字，走进餐厅，把名单交给我爸爸，让他自己看，并为名单上的每个名字写一张卡片。更快，更安静！在代码中，该列表类似于以下内容:

```
christmasList = [ "Spongebob", "Timothy", "Uncle George" ]; 
```

这些名字中的每一个都被称为数组的一个“元素”，所以这个列表有 3 个元素。可以放入数组的元素数量没有限制——数组的大小(或长度)取决于你有多少朋友。你可以给数组一个描述它的标题——在这个例子中是‘Christmas list’——然后你可以写一个函数把这个标题传递给它。该函数可以使用这个标题找出数组中包含的所有内容。

您可以使用许多不同的方法来处理数组，但是这种方法很好，因为它是不言自明的。它抽象出了机制，但本质上它是在数组上“迭代”，或者“查看每个元素并对其做些什么”:

```
const writeAllTheChristmasCards = christmasList => {
    christmasList.forEach(name => {
        console.log("Merry Christmas " + name + "!");
    });
 } 
```

回到我们的例子，爸爸从妈妈那里得到名字列表，并像这样迭代:他得到一张圣诞卡，用列表上的第一个名字写下美好的信息，然后他对下一个名字和随后的每个名字做完全相同的事情，直到他到达列表的末尾。到那时，他已经有了一叠写得很好的卡片，只用了一半的时间就完成了，而且喊得更少了:

```
writeAllTheChristmasCards(christmasList);
// Merry Christmas Spongebob!
// Merry Christmas Timothy!
// Merry Christmas Uncle George! 
```

综上...数组是事物的列表，而不是单一的事物。我们可以使用数组更高效地执行重复性任务。

如果我们真的想要递送卡片，我们也需要地址，尽管数组并不真正适合这样做，所以请继续关注下一篇激动人心的文章——“为什么你在写圣诞卡时会想要使用一个对象”