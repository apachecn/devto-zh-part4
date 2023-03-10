# TDD 示例

> 原文：<https://dev.to/delbetu/a-tdd-example-e4o>

# 简介

作为我的 TDD 学习过程的一部分，我面对这个问题，并发现遵循 TDD 规则很难解决它。
于是我开始了这个讨论->[https://dev . to/del betu/solve-this-simple-problem-with-TDD-5e 41](https://dev.to/delbetu/solve-this-simple-problem-with-tdd-5e41)

我当时尝试遵循的 TDD 流程是:

1.  编写一组测试(输入->预期输出)
2.  挑选最简单的测试用例来解决。(红色)(写一个失败的规范)
3.  编写解决当前测试集的最简单的代码更改。(绿色)
4.  检查是否需要干燥(重构)
5.  回到第 2 页

## 使我达成解决方案的变化

我没有创建预定义的测试集，而是为每个 TDD 周期方便地创建了一个新的测试。

1.  为算法的当前状态方便地编写一个测试用例(红色)
2.  编写解决当前测试集的最简单的代码更改。(绿色)
3.  如果需要，干燥(重构)
4.  回到 1

在示例中，我们将理解`conveniently`的含义。

# 问题

```
flatten an array of arbitrarily nested arrays of integers into a flat array of integers. e.g. [[1,2,[3]],4] -> [1,2,3,4]. 
```

Enter fullscreen mode Exit fullscreen mode

在你继续阅读之前，试着用 TDD 来解决它。

# 动起手来！

### 周期 1

```
def test_empty
  assert_equal [], flatten([])
end

def flatten(array_tree)
  []
end 
```

Enter fullscreen mode Exit fullscreen mode

### 周期 2

```
def test_no_subarray_one_element
  assert_equal [1], flatten([1])
end

def flatten(array_tree)
  array_tree
end 
```

Enter fullscreen mode Exit fullscreen mode

### 周期 3

第一个也是唯一的元素是一个数组。
在这一点上，我决定解决算法的`recursiveness`行为。
所以我顺手选择了下一组测试用例。

```
def test_with_subarray_one_element
  assert_equal [1], flatten([[1]])
end

def flatten(array_tree)
 if array_tree[0].is_a?(Array)
   array_tree[0]
 else
   array_tree
 end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 周期 4

```
def test_with_subarray_one_element
  assert_equal [1], flatten([[1]])
end

def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    array_tree[0]
  else
    array_tree
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第五周期

```
def test_with_subsubarray_one_element
  assert_equal [1], flatten([[[1]]])
end

def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    if array_tree[0][0].is_a?(Array)
      array_tree[0][0]
    else
      array_tree[0]
    end
  else
    array_tree
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第六周期

此时，您可以识别出一种递归模式。

```
def test_with_subsubarray_one_element
  assert_equal [1], flatten([[[[1]]]])
end

def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    if array_tree[0][0].is_a?(Array)
      if array_tree[0][0][0].is_a?(Array)
        array_tree[0][0][0]
      else
        array_tree[0][0]
      end
    else
      array_tree[0]
    end
  else
    array_tree
  end
end

# Refactor
def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    flatten(array_tree[0])
  else
    array_tree
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第七周期

在这一点上，我意识到了关于算法泛化的两件事。
(留在注释中)
这两点将使我在未来的测试中选择概括这部分行为的测试。

```
def test_when_first_and_second_are_subarray
  assert_equal [1,2], flatten([[1], [2]])
end

def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    result = flatten(array_tree[0]) # I will need this for each sub_array
    if array_tree[1].is_a?(Array)
      result = result + flatten(array_tree[1])
    end
    result
  else
    array_tree # This should return only the numbers not the subarrays
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第八周期

此时，我意识到如何组合递归调用，所以我决定进行重构。

```
def test_when_all_are_subarrays
  assert_equal [1, 2, 3], flatten([[1], [2], [3]])
end

def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    result = flatten(array_tree[0]) # call flatten for each sub_array and concatenate results
    if array_tree[1].is_a?(Array)
      result = result + flatten(array_tree[1])
      if array_tree[2].is_a?(Array)
        result = result + flatten(array_tree[2])
      end
    end

    result
  else
    array_tree # elements without subarrays
  end
end

# Refactor --> Generalize the ifs
def flatten(array_tree)
  if array_tree[0].is_a?(Array)
    sub_arrays = array_tree.select {|x| x.is_a?(Array)}
    sub_arrays.reduce([]) do |result, sub_array|
      result + flatten(sub_array)
    end
  else
    array_tree
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第九周期

处理第 5 周期中发现的一个待定行为。
else 分支应该只返回数字而不是子数组
如果输入的第一个元素是数字，而第二个是子数组，那么 else 分支就会失败。
所以下一个测试可能是:
`[1, [2]] --> [1, 2]`

```
def test_when_first_is_number_and_second_is_subarray
  assert_equal [1, 2], flatten([1, [2]])
end

def flatten(array_tree)
  result = array_tree.reject {|x| x.is_a?(Array)} # elements without subarrays
  sub_arrays = array_tree.select {|x| x.is_a?(Array)}
  result = result + sub_arrays.reduce([]) do |accumulator, sub_array|
    accumulator + flatten(sub_array)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 第十周期

在这一点上，我想我找到了广义解，所以下一个测试是一个复杂的情况。

```
def test_complicated_case
  assert_same_elements [1, 2, 3, 4, 5, 6, 7, 8, 9, 0], flatten([[[1, 2, 3]], [4, [5, 6]], 7, [[[[8]], 9]], 0])
end

def flatten(array_tree)
  result = array_tree.reject {|x| x.is_a?(Array)} # elements without subarrays
  sub_arrays = array_tree.select {|x| x.is_a?(Array)}
  result = result + sub_arrays.reduce([]) do |accumulator, sub_array|
    accumulator + flatten(sub_array)
  end
end

private

def assert_same_elements(array1, array2)
  assert array1 - array2 == []
end 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读，
欢迎评论！