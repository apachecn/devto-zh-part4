# 让编译器为您工作吧！

> 原文：<https://dev.to/jvanbruegge/let-the-compiler-do-the-work-for-you-22kl>

最近我遇到了一个小小的编程难题，任务是获取一个二叉查找树并返回一个新的树，其中每个节点都被右边节点的总和所取代。所以给定这棵树:

```
 5
   / \
  2    7
 /    / \
1    6   8
      \
       6 
```

我们从最右边的数字开始，将其设置为零(因为无的和仍然是无)。接下来是上面的 7，当前总和是 8，因为右边只有一个节点。在这之后是“第二个”6，所以是另一个 6 的孩子，因为它更靠右。一般来说，顺序总是右子树，自我，左子树。最后我们以这棵树结束，看看你是否明白每个数字的来源:

```
 27
   /  \
  32   8
 /    / \
34   21  0
      \
       15 
```

# 用 Java 实现这个算法

对于下面的实现，我们将只使用标准库中的东西。

首先，我们给自己定义一棵树:

```
public class Node {
    Node left;
    int value
    Node right;

    public Node(Node l, int v, Node r) {
        this.left = l;
        this.value = v;
        this.right = r;
    }
} 
```

现在，像对待列表和树这样的递归数据结构一样，我们将把算法定义为递归函数。我们需要跟踪当前的总数，并用新的数据建立一个新的树。

```
public class Pair {
    Node n;
    int v;

    public Pair(Node n, int v) {
        this.n = n;
        this.v = v;
    }
}

public class Node {
    Node left;
    int value;
    Node right;

    public Node(Node l, int v, Node r) { /* ... */ }

    public Pair solve(int currentSum) {
        // Store the current sum in here, as fallback if right is null
        Pair rightResult = new Pair(null, currentSum);
        // First, go to the right subtree, if it exists
        if(this.right != null) {
            rightResult = this.right.solve(currentSum);
        }
        int sum = rightResult.v + this.value;

        // Again, save the sum as fallback
        Pair leftResult = new Pair(null, sum);
        if(this.left != null) {
            leftResult = this.left.solve(sum);
        }

        // Finally create a new node (to replace self)
        Node newSelf = new Node(leftResult.n, rightResult.v, rightResult.n);
       // And return it together with the sum
       return new Pair(newSelf, leftResult.v);
    }
} 
```

现在，我们只需要一个主函数来运行它:

```
public class Main {
    static Node testTree = new Node(
        new Node(
            new Node(null, 1, null),
            2,
            null
        ),
        5,
        new Node(
            new Node(
                null,
                6,
                new Node(null, 6, null)
            ),
            7,
            new Node(null, 8, null)
        )
    );

    public static void main(String[] args) {
        Pair result = testTree.solve(0);
        System.out.println(result.n);
    }
} 
```

要查看我们的结果，我们还需要节点上的一个`toString`方法:

```
public class Node {
    Node left;
    int value;
    Node right;

    public Node(Node l, int v, Node r) { /* ... */ }

    public Pair solve(int currentSum) { /* ... */ }

    public String toString() {
        String leftTree = left == null ? " " : left.toString();
        String rightTree = right == null ? " " : right.toString();
        return "Node(" + leftTree + ", " + value + ", " + rightTree + ")";
    }
} 
```

如果您现在运行代码，您将看到

```
Node(Node(Node( , 34,  ), 32,  ), 27, Node(Node( , 21, Node( , 15,  )), 8, Node( , 0,  ))) 
```

这正是我们要找的树！

# 在 Haskell 中实现这个

现在你可能会问，这和题目有什么关系？编译器不会在这里为我们写代码。这是因为 Java 编译器仅仅是一个“检查”编译器(我这样称呼它)。如果你不匹配类型或犯语法错误，它会抱怨，但它不会为你做任何工作，它基本上就像老师一样，事后检查你的答案。

Haskell 编译器是不同的。这有几个原因。首先，Haskell 推断类型，因此您不必到处对它们进行注释，错误还会指出哪种类型应该在哪里。另一个原因是 Haskell 比其他常用语言更关注基础抽象。

这个基本抽象之一是`Functor`。如果你的数据类型是一个函子，你可以在它上面映射一个函数。不多不少。例如，数组或列表是一个函子，您可以在其中为每个元素应用一个函数(您可以从 Java streams 或 JavaScript Array.map 中了解这一点)。在 Haskell 中，这适用于任何“包含”其他数据的数据类型。让我们定义这样一个数据类型——注意，我们把数据类型留在树抽象中，在 Java 例子中是`int` :

```
module Main where

data Tree a = Leaf | Node (Tree a) a (Tree a) 
```

该声明与 Java 示例中的`Node`类基本相同。只是我们用`Leaf`代替`null`来表示空的孩子，并且我们没有命名数据`left`、`value`、`right`，只是把它们按顺序排列。

所以，现在回到 functor:编译器能够自动生成实现这种映射所需的代码。您需要做的就是启用该特性并使用`deriving`子句。

```
{-# LANGUAGE DeriveFunctor #-}
module Main where

data Tree a = Leaf | Node (Tree a) a (Tree a)
            deriving Functor 
```

这已经允许我们将树中的所有节点加 1，例如:

```
incrementNodes :: Tree Int -> Tree Int
incrementNodes tree = fmap (+1) tree 
```

但是现在我们想同时进行地图绘制和收集。我们解决了前半部分，让我们做后半部分。为了将一个结构折叠成一个单一值，有一个类型类`Foldable`要求您的数据已经是一个`Functor`。同样，数组和列表是可折叠的，它的工作方式就像 Java 8 中的 collect 和 JavaScript 中的 Array.reduce 一样。同样，这可以由编译器自动生成:

```
{-# LANGUAGE DeriveFunctor #-}
{-# LANGUAGE DeriveFoldable #-}
module Main where

data Tree a = Leaf | Node (Tree a) a (Tree a)
            deriving (Functor, Foldable) 
```

现在我们可以计算所有节点的总和，例如:

```
sumNodes :: Tree Int -> Int
sumNodes tree = foldr (+) 0 tree 
```

现在到了最后一步:贴图和折叠合二为一。为此，我们需要另一个抽象。这个类允许映射并同时跟踪一些“副作用”，但是需要你的数据已经是`Foldable`了。例如，我们可以跟踪一些本地状态-当前总和。和之前的类一样，编译器可以为我们自动生成:

```
{-# LANGUAGE DeriveFunctor #-}
{-# LANGUAGE DeriveFoldable #-}
{-# LANGUAGE DeriveTraversable #-}
module Main where

data Tree a = Leaf | Node (Tree a) a (Tree a)
            deriving (Functor, Foldable, Traversable) 
```

有了这些，我们最终的解决方案非常简单:

```
solve :: Tree Int -> (Int, Tree Int)
solve tree = mapAccumR (\a b -> (a + b, a)) 0 tree 
```

`mapAccumR`是 Haskell 标准库中的一个函数，定义如下:

```
mapAccumR :: Traversable t => (a -> b -> (a, c)) -> a -> t b -> (a, t c)
mapAccumR fun init x = runStateR (traverse (StateR . flip fun) x) init 
```

这个确切的定义并不重要，只要看到在内括号中它使用了`StateR`来使你的函数表现得像一个副作用。然后它使用`traverse`以正确的顺序组合有状态的效果，然后`runStateR`一个接一个地执行它们。

Java 要求我们手工编写一个`toString`方法。在 Haskell 中，我们可以通过派生`Show`再次使用编译器。因此，通过一个主要的方法使一切都可以运行，我们的谜题的完整代码就是:

```
{-# LANGUAGE DeriveFunctor #-}
{-# LANGUAGE DeriveFoldable #-}
{-# LANGUAGE DeriveTraversable #-}
module Main where

data Tree a = Leaf | Node (Tree a) a (Tree a)
            deriving (Show, Functor, Foldable, Traversable)

solve :: Tree Int -> (Int, Tree Int)
solve tree = mapAccumR (\a b -> (a + b, a)) 0 tree

testTree :: Tree Int
testTree =
    Node
        (Node
            (Node Leaf 1 Leaf)
            2
            Leaf
        )
        5
        (Node
            (Node
                Leaf
                6
                (Node Leaf 6 Leaf)
            )
            7
            (Node Leaf 8 Leaf)
        )

main :: IO ()
main = do
    let (sum, tree) = solve testTree
    print tree 
```

# 结论

正如您所看到的，如果编译器支持您，您可以大幅减少代码行数。更多的代码总是意味着更多的错误，所以我们不必自己写的东西都是好的。