# 字谜检查器-三种 JavaScript 解决方案

> 原文：<https://dev.to/sarah_chima/anagrams-checker-three-javascript-solutions-53kp>

我开始了一个关于常见算法和 javascript 问题的 JavaScript 解决方案的系列。如果你错过了第一个，这里有一个[链接到它](https://dev.to/sarah_chima/reverse-a-string-four-javascript-solutions-2nbm)。本周早些时候，我写了一篇关于[大 O 符号](https://dev.to/sarah_chima/the-big-o-notation-an-introduction-34f7)的文章。如果您不熟悉它，您可能希望阅读它，因为本文中使用了一些概念。让我们直接进入问题陈述。

### 寻找字谜——问题

变位词是具有相同数量的相同字符的单词。这意味着两个字符串是变位词，如果我们可以重新排列一个得到另一个。

这里有一些变位词的例子。

1.  “倾听”和“沉默”
2.  “铁路安全”和“童话故事”
3.  “宿舍”和“脏房间”
4.  “眼睛”和“他们看见了”

为了解决这个问题，我们将假设如下:

1.  我们忽略了多余的字符，比如“！”、“@”等。和空白。
2.  我们只想处理小写字符。

让我们来看看这个问题的一些解决方案。然后我们将根据它们的时间复杂度来比较它们。

### 解决方案 1 -创建两个字符串的字符映射表并比较映射表

这个上下文中的字符映射表是包含字符串中每个唯一字符的映射表或对象。它将字符存储为键，并将它在该字符串中出现的次数存储为值。

```
 function anagrams(stringA, stringB) {
        /*First, we remove any non-alphabet character using regex and convert
        convert the strings to lowercase. */
        stringA = stringA.replace(/[^\w]/g, "").toLowerCase()
        stringB = stringB.replace(/[^\w]/g, "").toLowerCase()

        //Get the character map of both strings
        const charMapA = getCharMap(stringA)
        const charMapB = getCharMap(stringB)

        /* Next, we loop through each character in the charMapA, 
        and check if it exists in charMapB and has the same value as
        in charMapA. If it does not, return false */
        for (let char in charMapA) {
            if (charMapA[char] !== charMapB[char]) {
                return false
            }
        }

        return true
    }

    function getCharMap(string) {
        // We define an empty object that will hold the key - value pairs.
        let charMap = {}

        /*We loop through each character in the string. if the character 
        already exists in the map, increase the value, otherwise add it 
        to the map with a value of 1 */
        for (let char of string) {
            charMap[char] = charMap[char] + 1 || 1
        }
        return charMap
    } 
```

for 循环的运行时复杂度是线性的，即 O(n)。在这种情况下，有 3 个连续的 forloops 没有嵌套。忽略常数和其他因素，时间复杂度近似为线性，即 O(n)。

### 2。对字符串进行排序并检查它们是否相同

这是检查两个字符串是否是变位词的一种更短更简洁的方法。
在本例中，我们将字符串转换为数组，使用 Array.sort()
方法对其进行排序，并将其转换回字符串。然后我们比较两个字符串，检查它们是否相同。

```
 function anagrams(stringA, stringB) {
        /*First, we remove any non-alphabet character using regex and convert       
        convert the strings to lowercase. */
        stringA = stringA.replace(/[^\w]/g, '').toLowerCase()
        stringB = stringB.replace(/[^\w]/g, '').toLowerCase()

        return sortString(stringA) === sortString(stringB)
    }

    /*This function sorts the strings*/ 
    function sortString(string) {
        return string.split('').sort().join('');
    } 
```

Array.sort 使用合并排序，因此其时间复杂度为 O(nlogn)。

### 3。使用 Array.splice()

这是另一种解决方案。在这种情况下，我们将字符串 B 转换为一个数组，遍历字符串 A 中的每个字符，并检查它是否存在于字符串 B 的数组中，`arrB`。如果它存在，我们使用拼接方法将其从数组中删除。我们这样做是为了使在`arrB`中出现不止一次的字符不会被检查两次。

```
 function anagrams(stringA, stringB) {
        /*First, we remove any non-alphabet character using regex and convert       
        convert the strings to lowercase. */
        stringA = stringA.replace(/[^\w]/g, '').toLowerCase()
        stringB = stringB.replace(/[^\w]/g, '').toLowerCase()

        /*Next, we check if the lengths of the strings are equal. 
        If they are anagrams, they will have the same length. */
        if (stringA.length !== stringB.length) {
            return false
        }

        let arrB = stringB.split("")

        for (let char of stringA ){ 
            if (!arrB.includes(char)) {
                return false
                break;
            } else {
                arrB.splice(arrB.indexOf(char), 1)
            }
        }

        return true

    } 
```

所以让我们考虑一下这个解的时间复杂度。在这种情况下，有三个循环运行。`for`回路、`includes`回路和`splice`回路。由于`splice`循环和`includes`没有嵌套，时间复杂度趋于 O(n^2)。

### 结论

我们已经看到了解决方案及其近似的时间复杂度。比较它们的时间复杂度，第一个解决方案似乎具有更好的性能。它的时间复杂度大约为 O(n)。然而，第二个解决方案更简洁。因此，您可以根据什么对您更重要来选择任何解决方案。

有什么问题或补充吗？请留言评论。

感谢您的阅读。