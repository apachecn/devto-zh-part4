# React Tldr 规范化数据的基本概念

> 原文：<https://dev.to/maniekm/react-tldr-basic-concepts-of-normalizing-data-fbc>

*   每种类型的数据在状态中都有自己的“表”。
*   每个“数据表”都应该在一个对象中存储单个项目，项目的 id 作为键，项目本身作为值。
*   对单个项目的任何引用都应该通过存储项目的 ID 来完成。
*   应该使用 id 数组来表示顺序。
*   如果您想自动化标准化过程，请使用“Normalizr”库

来源:[https://redux . js . org/recipes/structuring-reducers/normalizing-state-shape](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape)