# 更容易利用 ptr 中的 bool

> 原文：<https://dev.to/nickhuanca/more-easily-leveraging-bool-s-from-a-ptr-36l2>

我目前正在做一个项目，利用 golang 中指向 Bool 值的指针。我用的是 [corev1。security context . RunAsNonRoot](https://godoc.org/k8s.io/api/core/v1#SecurityContext)是一个`*bool`。当我进行比较时，我必须将所有的真/假检查包装在[的帮助函数](https://github.com/reactiveops/polaris/blob/c6e3550293d5067a0e2208b8daa93ac35c6cadcb/pkg/validator/bool_pointer_helpers.go#L10-L21)中，因为我不能确定该值是否会被重置(`nil`)，这会导致致命错误。

有没有我应该使用的其他 golang 技巧或专业技巧？或者，当使用具有这种类型的 struct 引用`*bool`的库时，我应该考虑一些其他类型的方法吗？

任何讨论都会很棒！周四快乐！