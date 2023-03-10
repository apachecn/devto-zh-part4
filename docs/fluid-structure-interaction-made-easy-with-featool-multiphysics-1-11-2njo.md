# FEATool Multiphysics 1.11 简化了流体与结构的相互作用

> 原文：<https://dev.to/featool/fluid-structure-interaction-made-easy-with-featool-multiphysics-1-11-2njo>

FEATool Multiphysics 1.11 现已推出，通过引入流体-结构相互作用(FSI)解算器(除了现有的内置 Multiphysics、OpenFOAM 和 FEniCS 解算器集成之外)，进一步扩展了多模拟解算器概念。

[![](img/4a6b9c173af46d0cd839d8b5f7b09422.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMcfKSLe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3az2vgf9bjejnrkg1j24.jpg)

此外，3D 几何引擎已经更新，支持 OpenCASCADE 和 BRL-CAD 开源 CSG 几何引擎，以及用于更复杂几何的新内置几何图元。

FEATool Multiphysics 版还具有以下特性:

*   对更高效的稀疏线性求解器的新支持，如带有 ILU(k)预处理的腮腺炎和 GMRES/BiCGStab。
*   内部/内部边界的专用和改进处理
*   支持 Gmsh v4.x GEO 文件格式几何图形导入和导出
*   热机械耦合、流体结构相互作用和非牛顿流的新例子和教程
*   P1/P2 气泡、内德莱克旋度和拉维亚特-托马斯矢量有限元形状函数
*   各种 GUI 和可用性改进

详情及工具箱下载请见[https://www . FEA tool . com/news/2019/07/24/FEA tool-v1p 11-fluid-structure-interaction-in-MATLAB](https://www.featool.com/news/2019/07/24/featool-v1p11-fluid-structure-interaction-in-matlab)。

# 多物理# fsi #仿真#matlab