# TIL:使用 Chudnovsky 算法计算圆周率的 n 位数

> 原文：<https://dev.to/parambirs/til-calculating-n-digits-of-pi-using-chudnovsky-algorithm-1j10>

今天偶然发现了 [Chudnovsky 算法](https://en.wikipedia.org/wiki/Chudnovsky_algorithm)计算π的值到 N 位数的精度。这个算法的 Python 代码如下:

```
import decimal

def compute_pi(n):
    decimal.getcontext().prec = n + 1
    C = 426880 * decimal.Decimal(10005).sqrt()
    K = 6.
    M = 1.
    X = 1
    L = 13591409
    S = L

    for i in range(1, n):
        M = M * (K ** 3 - 16 * K) / ((i + 1) ** 3)
        L += 545140134
        X *= -262537412640768000
        S += decimal.Decimal(M * L) / X

    pi = C / S
    return pi 
```

我编写了一个简单的 CLI 来测试这一点:

```
#!/usr/bin/env python3 
import argparse
from lib import compute_pi

parser = argparse.ArgumentParser(description='Print n digits of pi.')
parser.add_argument('num_digits', metavar='N', type=int, help='no. of digits of pi to print')
args = parser.parse_args()

print(compute_pi(args.num_digits)) 
```

下面是运行中的 CLI:

```
> ./pi.py 30
3.141592653589741586517727315459

> ./pi.py 300
3.141592653589741586517727315457865035780252691261563179943288214795808630531389642185274931230804430454419117074147967105366083976712333542218321180274249883145873143454428446008580088034341219473373000151443532721504141865178673966393142941520166862874509797611548477147655085787688540025728361617601 
```

完整的源代码可以在 Github 库中找到。