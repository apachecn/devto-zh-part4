# 颤振性能提示(#2):使用 ListView.builder()创建包含大量条目的列表

> 原文：<https://dev.to/pedromassango/flutter-performance-tips-2-use-listview-builder-for-list-with-a-large-amount-of-items-37d1>

在`ListView`类中我们有不止一个构造函数，有时你可能想知道在确定的情况下使用哪一个。

### **ListView(儿童:[])**

这是默认的 ListView 构造器，接受一个小部件列表。我们必须使用这种情况下，我们有一个小的列表视图项目。

### **ListView.builder(...)**

这是我们在有大量列表视图项目的情况下必须使用的构造函数，因为与`ListView(children: <Widget>[])`不同，这个构造函数*只构建在屏幕*中可见的项目。每当用户滚动列表时，它就建立下一个可见的项目。

### **ListView.separated(...)**

这个和`ListView.builder(...)`做的一样，但是有一个额外的好处:它需要一个`separatorBuilder`来给我们在项目之间添加分隔符的能力。