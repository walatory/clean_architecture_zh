# 第七章 单一职责原则（SRP）

在接下来的所有原则（SOLID）中，单一职责原则可能是比较难懂的，这很可能因为它的名字取得不准确，很容易让开发者从这个名字中认为它的含义是每个模块都只做一件事。

诚然，单一职责原则有点像类似的含义，一个函数有且只做一件事。我们用单一职责原则去重构一个繁杂庞大的函数成更小的函数，我们在最底的层面上使用它。但是，这并不是我们的SOLID原则中的单一职责原则！

传统上，单一职责原则是这么表述的：

> 一个模块有且只能由于一种原因被改变

软件系统发生改变是为了满足用户和利益相关者，这些用户和利益相关者是上述描述里“改变的原因”，那么，我们可以这么表述这项原则：

> 一个模块有且只能对其中之一负责，用户或者利益相关者

不幸的是，“用户”，“利益相关者”这些词在这里并不恰当，因为可能有多个用户想在一样的地方改变系统，利益相关者也是。然而，我们真的指代一个群体，要求修改的一个或更多的人，我们把这中群体称为角色。[^1]

最终，我们这么描述单一职责原则：

> 一个模块有且只能对一个角色负责

现在，这里的“模块”我们指的是什么呢？最简单的定义就是源文件，



[^1]: Unfortunately, the words “user” and “stakeholder” aren’t really the right words to use here. There will likely be more than one user or stakeholder who wants the system changed in the same way. Instead, we’re really referring to a group—one or more people who require that change. We’ll refer to that group as an actor.

