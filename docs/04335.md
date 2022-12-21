# QBASIC 模块化程序设计

> 原文：<https://dev.to/anupmaharjan/qbasic-modular-programming-1lo5>

此代码

同
申报功能一样反$(W $)
【CLS】
输入“输入姓名”；w $
PRINT " Reverse name = "；
反转$ (W$)结束

函数反转$ (W$)

```
FOR P = 1 TO LEN(W$)
    C$ = MID$(W$,P,1)
    R$ = C$ + R$
NEXT P

reverse$ = R$ 
```

结束功能

但是它仍然滞后，为什么？