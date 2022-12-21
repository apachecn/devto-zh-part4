# 关于两个指针数组问题的思考- #27 LeetCode

> 原文：<https://dev.to/wenhan129/thoughts-about-a-two-pointer-array-problem-27-leetcode-26pg>

请首先查看链接以了解问题的详细信息。
[清除元素](https://leetcode.com/problems/remove-element/)

> 给定一个数组`nums`和一个值`val`，就地移除该值的所有实例，并返回新的长度。不要为另一个数组分配额外的空间，必须通过用 **O(1)** 额外的内存就地修改输入数组来做到这一点。元素的顺序可以改变。超出新长度的内容无关紧要。
> 
> 给定`nums = [3,2,2,3], val = 3`，你的函数应该返回 length = 2，其中`nums`的前两个元素是 2。超出返回长度的内容无关紧要。

这个问题的第一个想法是一个天真的解决方案——为经过过滤的值创建另一个数组。但是，在内存使用量的要求下。我们必须适当地修改输入数组。
输出应该是一个代表数组的*有效长度*的数字 N，这意味着前 N 个元素不应该有`val`。

我决定用两个指针的方法来解决这个问题。

*   [ ]为什么？用两个指针`index`、`targetIndex`、`index`代表`nums`循环函数的索引，`targetIndex`代表从 0 开始的结果数组索引。假设您正在基于原始数组操作一个新数组，您只是将限定值向前移动到新数组中。在`nums`的循环中，如果`nums[index]`不等于`val`，我们将值移动到新数组`nums[targetIndex] = nums[index]`中，并将`targetIndex`增加 1 以扩展新数组。如果`nums[index]`等于`val`，我们就跳过值的移动，所以新的数组将只包含合格的值。

```
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
    const arrLength = nums.length;
    if (arrLength === 0)
        return 0;
    let targetIndex = 0;
    for(let index = 0; i < arrLength; index++) {
        if (nums[index] !== val)
            nums[targetIndex++] = nums[index];}
    return targetIndex;
}; 
```