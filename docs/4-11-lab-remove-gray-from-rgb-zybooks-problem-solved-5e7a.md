# 4.11 实验室:从 RGB Zybooks 中移除灰色问题已解决

> 原文：<https://dev.to/abhi6982/4-11-lab-remove-gray-from-rgb-zybooks-problem-solved-5e7a>

摘要:给定红色、绿色和蓝色的整数值，从每个值中减去灰色。

计算机通过组合红色、绿色和蓝色(rgb)来表示颜色。每个子颜色的值可以从 0 到 255。因此(255，0，0)是亮红色，(130，0，130)是中紫色，(0，0，0)是黑色，(255，255，255)是白色，(40，40，40)是深灰色。(130，50，130)是褪色的紫色，这是由于(50，50，50)灰色部分。(换句话说，等量的红色、绿色和蓝色产生灰色)。

给定红色、绿色和蓝色的值，移除灰色部分。

例如:如果输入是:

130
50
130
输出为:

80 0 80
求最小值，然后把三个值都减去，这样就去掉了灰度。

注意:此页面将 rgb 值转换为颜色。

[https://repl.it/@AbishekLakandri/Solved-411-LAB-Remove-gray-from-RGB?lite=true](https://repl.it/@AbishekLakandri/Solved-411-LAB-Remove-gray-from-RGB?lite=true)