# 如何在 ruby 中反转字符串

> 原文：<https://dev.to/webcoderph/how-to-reverse-a-string-in-ruby-5ec1>

你可以这样做:

```
 def my_reverse(str)
  str.chars.inject {|a, b| b + a}
 end

 > my_reverse("idol")
 => "lodi" 
```

你也可以猴子补丁字符串对象:

```
 class String
   def my_reverse
     self.chars.inject {|a, b| b + a}
   end
 end

 > "idol".my_reverse
 => "lodi" 
```

并将打印相同的结果:)

警告:在猴子修补 ruby 对象时要格外小心。