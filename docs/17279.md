# 算法挑战:2

> 原文：<https://dev.to/langtips/algorithm-challenge-2-3o4o>

# 挑战

> 返回给定字符串中元音的数量。我们将把 a、e、I、o 和 u 作为这个形的元音。输入字符串将只包含小写字母和/或空格。

# 解

#### 生锈

```
fn get_count(string: &str) -> usize {
  let vowels: &str = "aeiou";
  let mut vowels_count: usize = 0;
  for c in string.chars() {
      if vowels.contains(c) {
          vowels_count = vowels_count + 1; 
      }
  }
  vowels_count
} 
```