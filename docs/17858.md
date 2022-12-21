# 算法挑战:1

> 原文：<https://dev.to/langtips/algorithm-challenge-1-2ehf>

# 挑战

```
This time no story, no theory. The examples below show you how to write function accum:

Examples:

accum("abcd") -> "A-Bb-Ccc-Dddd"
accum("RqaEzty") -> "R-Qq-Aaa-Eeee-Zzzzz-Tttttt-Yyyyyyy"
accum("cwAt") -> "C-Ww-Aaa-Tttt"

The parameter of accum is a string which includes only letters from a..z and A..Z. 
```

# 解

```
fn format_string(word: &str) -> String{
    let mut result: Vec<char> = vec![] ; //This is where we store our result which is array of character
    let mut conversion: Vec<char>; 
    for (i,c) in  word.chars().enumerate(){
         if i > 0{
            result.append(&mut vec!['-']);
         }
         result.append(&mut c.to_uppercase().collect());
         conversion = c.to_lowercase().collect();
         for _x in 0..i {
            // you need to clone else it prints seems a series of same object append and print only one
            result.append(&mut conversion.clone());
         }
    }
    return result.iter().collect();
}
fn main(){
     println!("{:?}", format_string("RqaEzty"))
} 
```

```
//go implementation coming soon 
```