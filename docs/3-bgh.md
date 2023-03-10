# 三个人直上了刺，却忘了背，所以数被吃掉了。

> 原文：<https://dev.to/uatthaphon/3-bgh>

# [t 1¨有风险的运气](#%E0%B9%80%E0%B8%AA%E0%B8%B5%E0%B9%88%E0%B8%A2%E0%B8%87%E0%B9%82%E0%B8%8A%E0%B8%84)

"不幸的是，你忘了数字，否则--～"我哥哥的抱怨声，这就像你每月 1 日和 16 日的口头禅，好了

我心里想了很多事情，不管你有多可怜，我都没被他吃掉

但这好像是我们家社会的一部分。我也相信，全世界都在为我玩。很久以前，我就买了一张政府奖券。

## 建立故障排除功能

当我把它看作是很多人应该经历的问题时，我把它转换成一个命题，来表达自己。

今天，我们来试着为两个数字和三个数字建立一个概率函数，主要使用**【概率】t1㎡。**

**样本**假设他哥哥买了两个数字，即**12㎡T3㎡基本上大多数人都会买一个数字。**

很容易理解，对吧？但如果是三个数字呢？万一他哥哥没买表数字，那他哥哥买的是个奖，只有他哥哥没擦过我的天啊！如果他哥不买 3 个数字回去就算不便宜了

### 命题为【5559】\\\𑨉

所以，我们就可以得到一个 exersice 中的一个命题，就是构造两个 2 数字和三个数字的函数，从所有的概率中找到一组数字，这样就可以了。

**样本，他哥哥得到了两个，三个是从那里走来的，一个是从圣树，一个是从寺庙里走来的，一个是坐在省烛，一个是在那里写的。**

 **> ต้องขอขอบคุณ [排列——所有可能的数字集合](https://stackoverflow.com/questions/5506888/permutations-all-possible-sets-of-numbers)

```
<?php

    function pc_permute(array $items, array $perms = [ ])
    {
        if (empty($items)) {
            $return = [$perms];
        } else {
            $return = [];
            for ($i = count($items) - 1; $i >= 0; --$i) {
                $newitems = $items;
                $newperms = $perms;
                list($foo) = array_splice($newitems, $i, 1);
                array_unshift($newperms, $foo);
                $return = array_merge($return, pc_permute($newitems, $newperms));
            }
        }

        return $return;
    } 
```

对于上面的代码，它是一个函数，用于从我们得到的任意数字中找到所有可能的数字集，它以数组的形式获取值。

```
<?php

    function all_possibility($number)
    {
        $digits = pc_permute(str_split($number));
        $result = [];

        foreach ($digits as $data) {
            $result[] = (int) implode("", $data);
        }

        $result = array_unique($result);
        sort($result);

        return $result;
    } 
```

我们创建了另一个函数来管理输入和输出数据，并且因为它的`pc_permute`函数而更容易输入数据。

我们就能得到一个函数，从我们得到的整数的来回位置转换中得到一个可行的值，先生，这个函数是一个确定概率的函数。

### 测试了他哥哥从圣树里得到的两个数字。

```
// เมื่อเราทำการรันเลข 64
all_possibility(64);

// ผลลัพธ์ที่ได้ก็จะเป็น
array:2 [
  0 => 46
  1 => 64
] 
```

### 测试了他哥哥从圣树里得到的 3 个数字。

```
// เมื่อเราทำการรันเลข 639
all_possibility(639);

// ผลลัพธ์ที่ได้ก็จะเป็น
array:6 [
  0 => 369
  1 => 396
  2 => 639
  3 => 693
  4 => 936
  5 => 963
] 
```

这一次，如果按他哥哥的话，他只把他的位置调换了，他哥哥就可以满的了(有人会想，为什么不把他哥哥的位置弄成他不喜欢的样子。

结束了**