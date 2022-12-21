# 一种奇怪的 C++子串方式

> 原文：<https://dev.to/therealdarkmage/a-weird-way-to-substring-in-c-49o2>

```
/*

author:  mike bell 
twitter: @therealdarkmage
date:    Fri Aug 9 5:12 PM
website: https://mikebell.xyz

I was playing around and discovered a weird way to perform a substring on a string in C++.
Take the address of a string and then, using array indexing, add the position to substring from.
Works with constant and arbitrary strings! Cool/weird!
Compiles on current macOS g++ as of this writing.
*/
#include <iostream>
#include <string>
using std::string;
using std::cout;
using std::endl;
int main() {
    string s = &"Hello, World"[7];
    string ss = &s[2];
    cout << s << endl; // prints "World"
    cout << ss << endl; // prints "rld"
    return 0;
} 
```