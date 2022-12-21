# 📦CIEDE2000 色差算法

> 原文：<https://dev.to/samandar/ciede2000-color-difference-algorithm-4fm7>

```
from functorflow import ff

# ------ Returns the closest color --------- 
color = { 'R': 255, 'G': 1, 'B': 30 }

#  red, green, blue 
palette = [ {'R': 255, 'G': 0, 'B': 0 },
            {'R': 0, 'G': 255, 'B': 0 },
            {'R': 0, 'G': 0, 'B': 255} ]

print(ff('color-diff').closest(color, palette))
# {R: 255, G: 0, B: 0 }, red 

# ------ Return the most different color ----------- 
color = { 'R': 255, 'G': 255, 'B': 255 };
# black, white palette = [ {'R': 0, 'G': 0, 'B': 0 }, {'R': 255, 'G': 255, 'B': 255 } ];

print(ff('color-diff').furthest(color, palette)) 
# {'R': 0, 'G': 0, 'B': 0 }, black 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/color-diff?lite=true](https://repl.it/@functorflow/color-diff?lite=true)