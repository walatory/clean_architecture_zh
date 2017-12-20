# 第三部分 设计原则

良好的软件系统都是从整洁的代码开始。一方面，如果砖块早不好，建筑的架构无从谈起，另一方面，好的砖块也可能搭得一塌糊涂。这就引出了SOLID原则了。

SOLID原则告诉我们怎样在类中安排我们的程序和数据结构，这些类之间的关系应该如何。用“类”这个词并不意味着这些原则只能用在面向对象的软件里。一个类简单来说是一组函数和数据的耦合。每个软件系统包含如此多组，他们可能被称为类或其他名字。SOLID原则就应用于这些组。

SOLID原则的目标是创建中层软件架构，满足：

* 容忍改变
* 易于理解
* 基础组件可以用在多个软件系统中

“中层”这事是指程序员在模块层应用这些原则。[^1]这些原则的应用高于代码层面，帮助在模块和组件中定义软件结构的类型使用。

就像好砖也可能搭得一塌糊涂，良好设计的中层组件也可能把系统搭得一沓糊涂。处于这个原因，我们讲完SOLID原则，我们将继续江组件世界和他们同样重要的原则，进而再讲高层架构原则。

SOLID历史很悠久，我开始整理这些原则是在20世纪80年代末，在USENET（类似现在的Facebook）上争论软件设计原则。多年以来，这些原则不断发生改变，一些原则消失了，一些合并了，还有一些仍在增加。到了21世纪初期逐渐稳定下来了，我将以不同次序讲述这些原则。

大约在2004年， Michael Feathers发了一封电子邮件给我，如果他重排这些原则，这些原则的首字母是单词SOLID的拼写，这就是SOLID原则的由来。

以下章节将详细的讲述每个原则，以下是笼统的总结：

* SRP：单一职责原则
* OCP：开闭原则
* LSP：里氏替换原则
* ISP：接口隔离原则
* DIP：依赖倒置原则

多年来，这些原则在很多不同出版物都有描述。以下章节将关注这些原则在架构上的一样，而不是这些原则的细节套路。如果你对这些原则还很陌生，建议你可以用脚注的文档预先学习[^2]。

[^1]: The term “mid-level” refers to the fact that these principles are applied by programmers working at the module level.

[^2]: _Agile Software Development, Principles_, Patterns, and Practices, Robert C. Martin, Prentice Hall, 2002,[http://www.butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod](http://www.butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod), [https://en.wikipedia.org/wiki/SOLID\_\(object-oriented\_design\)](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design\)\) \(或者Google SOLID\)

