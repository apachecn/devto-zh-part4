# 编码训练营的开始

> 原文：<https://dev.to/fihra/beginning-of-a-coding-bootcamp-48h3>

熨斗学校的第一天:

焦虑。太可怕了。激动人心。紧张。很多人都在想我该如何做好心理准备。冒名顶替综合症。我在做什么？我能熬过去吗？这已经这么多了！

第一周结束:

哇哦。所以。很多。信息。如此短暂的时间……..

我觉得每当我遇到一个无法在 5 到 10 分钟内解决的 bug 时，我会抓狂。每当我向我的 SEC(蔻驰软件工程)询问最近出现的一个 bug 时，我都会觉得自己很愚蠢。我会查看错误，但不知道如何处理这个错误。每次我通过 Stack Overflow 查找时，我都会被超出我们当前学习领域范围的答案淹没。堆栈溢出有助于看到使用许多有用的方法和宝石解决错误的不同方法，但是使用我们到目前为止所学的知识似乎更有利于建立了解方法的来龙去脉的基础。例如，。reverse 方法很容易反转一个字符串，但是知道如何手动操作有助于理解它的工作方式，如果我们没有这个“快捷”方法的话。

第二周末:

我明白了，但我也不明白。我想出了编码双关语。

我不得不重新接受编码挑战，因为与其他人相比，当事情没有发生时，我感到非常慢。我感到落后，因为我对迭代和访问嵌套散列和数组没有把握。令人担忧的是，我不得不平衡 ORM/ActiveRecord 实验室的进度，并重新接受编码挑战。

第三周结束时:

本周初，我重考了编码挑战。学习 ORM/ActiveRecord 实际上有助于理解代码挑战，因为它使用的是一个轻量级数据库，也就是“@@all = []”类数组，其中所有实例化的对象都被推送到@@all 数组。尝试访问 ActiveRecord 的数据帮助我应对了迭代和数据访问代码挑战。

```
######Code Challenge Way######
@@all = []

def initialize(name)

    @name = name
    @@all << self

end

def self.all
   @@all

end

#Iterating
self.all.each{|n|
   n.name #lists all names

}

#####Active Record Way######

create_table :pets do |p|
   p.string :name

   p.integer :age
   p.string :breed
end

#Reading from the migrated database
#Iterating
   pets.all.each {|p|

    p.name #lists all names
} 
```

Enter fullscreen mode Exit fullscreen mode

这种结构确实帮助我处理了项目的规划阶段，因为我在代码挑战实践中工作，使用 READMEs 作为大纲，并与 ActiveRecord 实验室合作。

[![](img/82a498264fe5ac2eec3a9fffda547ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WYpPhTzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2zjprol2smuli445llyc.jpg)

进入项目时间。作为合作伙伴项目，我们开发了一个 CLI(命令行界面)应用程序。我的搭档 Christopher 和我的项目是一个歌词生成器，我们使用 lyric.ovh API 让用户根据他们最喜欢的歌曲歌词来构建自己的歌词。这个项目真正考验了我们在过去 3 周所学的一切，从面向对象的编程，到迭代嵌套散列和 ActiveRecord。

这是一次在大型项目中工作的学习经历。这个项目发生了如此多的起伏，可以扩展到其他未来的博客帖子。在使用 git 和分支、重构、面向对象编程和函数式编程的斗争中，哪一个更合适？

训练营将持续数周。尽管这很伤脑筋，而且总是对即将发生的事情保持警觉，但我的想法总是“我迫不及待地想应用这些知识，在未来创造一些很酷的东西！”

[![](img/11e7b0c08678919df9d4abe4d2371fb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1-4Ur4tt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rw90qy7kqvv7zc31mxwr.jpg)