# 📦不同单位的量之间的转换

> 原文：<https://dev.to/samandar/converting-between-quantities-in-different-units-22n8>

```
from functorflow import ff, f

# gram to kilogram r = f('convert-units.kg', g=2000 )
print(r)

# meter to inches r = f('convert-units.in', m=5 )
print(r) 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@functorflow/convert-units?lite=true](https://repl.it/@functorflow/convert-units?lite=true)

## 支持的单位

长度

```
mm
cm
m
in
ft-us
ft
mi 
```

Enter fullscreen mode Exit fullscreen mode

面积

```
mm2
cm2
m2
ha
km2
in2
ft2
ac
mi2 
```

Enter fullscreen mode Exit fullscreen mode

团

```
mcg
mg
g
kg
oz
lb
mt
t 
```

Enter fullscreen mode Exit fullscreen mode

卷

```
mm3
cm3
ml
l
kl
m3
km3
tsp
Tbs
in3
fl-oz
cup
pnt
qt
gal
ft3
yd3 
```

Enter fullscreen mode Exit fullscreen mode

体积流率

```
mm3/s
cm3/s
ml/s
cl/s
dl/s
l/s
l/min
l/h
kl/s
kl/min
kl/h
m3/s
m3/min
m3/h
km3/s
tsp/s
Tbs/s
in3/s
in3/min
in3/h
fl-oz/s
fl-oz/min
fl-oz/h
cup/s
pnt/s
pnt/min
pnt/h
qt/s
gal/s
gal/min
gal/h
ft3/s
ft3/min
ft3/h
yd3/s
yd3/min
yd3/h' 
```

Enter fullscreen mode Exit fullscreen mode

温度

```
C
F
K
R 
```

Enter fullscreen mode Exit fullscreen mode

时间

```
ns
mu
ms
s
min
h
d
week
month
year 
```

Enter fullscreen mode Exit fullscreen mode

频率

```
Hz
mHz
kHz
MHz
GHz
THz
rpm
deg/s
rad/s 
```

Enter fullscreen mode Exit fullscreen mode

速度

```
m/s
km/h
m/h
knot
ft/s 
```

Enter fullscreen mode Exit fullscreen mode

节奏

```
s/m
min/km
s/ft
min/km 
```

Enter fullscreen mode Exit fullscreen mode

压力

```
Pa
hPa
kPa
MPa
bar
torr
psi
ksi 
```

Enter fullscreen mode Exit fullscreen mode

数字的

```
b
Kb
Mb
Gb
Tb
B
KB
MB
GB
TB 
```

Enter fullscreen mode Exit fullscreen mode

照明

```
lx
ft-cd 
```

Enter fullscreen mode Exit fullscreen mode

零件数量

```
ppm
ppb
ppt
ppq 
```

Enter fullscreen mode Exit fullscreen mode

电压

```
V
mV
kV 
```

Enter fullscreen mode Exit fullscreen mode

目前的

```
A
mA
kA 
```

Enter fullscreen mode Exit fullscreen mode

力量

```
W
mW
kW
MW
GW 
```

Enter fullscreen mode Exit fullscreen mode

视在功率

```
VA
mVA
kVA
MVA
GVA 
```

Enter fullscreen mode Exit fullscreen mode

无功功率

```
VAR
mVAR
kVAR
MVAR
GVAR 
```

Enter fullscreen mode Exit fullscreen mode

活力

```
Wh
mWh
kWh
MWh
GWh
J
kJ 
```

Enter fullscreen mode Exit fullscreen mode

无功能量

```
VARh
mVARh
kVARh
MVARh
GVARh 
```

Enter fullscreen mode Exit fullscreen mode

角

```
deg
rad
grad
arcmin
arcsec 
```

Enter fullscreen mode Exit fullscreen mode

> 停止浪费时间安装和配置 Python 库。马上使用它们。[📦FunctorFlow](https://functorflow.org) 立即为您自动安装和自动配置它们。