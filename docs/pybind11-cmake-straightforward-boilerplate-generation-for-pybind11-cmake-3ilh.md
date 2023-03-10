# pybind11-cmake:为 pybind11 + cmake 直接生成样板文件

> 原文：<https://dev.to/safijari/pybind11-cmake-straightforward-boilerplate-generation-for-pybind11-cmake-3ilh>

pybind11 非常适合制作 C++代码的 python 包装器，我一直在使用它。在大多数情况下，我需要使用 cmake 文件，所以我使用他们的 cmake 例子来开始这个项目。这通常包括将 CMakeExtension / Builder 类复制到我的 setup.py 文件中，并将 pybind11 作为 git 子模块添加到我的项目中。在 python_example 中所示的简单项目中，我可以不使用 cmake，但我发现它很难用于更复杂的项目。

所以我做了一个样板文件生成器([https://github.com/safijari/pybind11-cmake](https://github.com/safijari/pybind11-cmake))，它也封装了扩展模块，并提供了一个脚本来生成您开始一个项目所需的所有文件。这允许您使用 cmake 纯粹在 virtualenv 中处理 pybind11 项目(不需要创建 git 子模块)。

我已经用这个简化了我现有的项目，希望它对其他人也有用。