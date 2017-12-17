# 第九章 里氏代换原则（LSP）

1988年，Barbara Liskov写下了定义子类型的方式，如下

> 某处需要的东西应该遵从类似的性质：如果类型S的每个实例对象o1，类型T的每个实例对象o2，就任意程序P用到的类型T，o1可替代o2使得程序P的行为没有任何改变，那么类型S是类型T的子类。

这段即是为人熟知的里氏代换原则（LSP），让我们来看些例子。

## 使用继承的引导指南

假设我们名为License的类，如图9.1。这个类有名为calcFee\(\)的方法，被应用Billing调用。License有两个子类型PersonalLicense 和BusinessLicense，他们的calcFee方法有着不同的算法实现。

![](/assets/9/Figure_9.1_License_,_and_its_derivatives,_conform_to_LSP.png)

图9.1 License和其相关类，满足LSP  
这个设计满足LSP，因为Billing应用的行为不依赖具体用的是哪个子类型，两个子类型都可以替换掉License类型。

## 正方形/矩形问题

这是经典的违反LSP的例子，著名的（我觉得）正方形/矩形问题（如图9.2\)

![](/assets/9/Figure_9.2_The_infamous_square/rectangle_problem.png)

图9.2 “不”著名的正方形/矩形问题

在这个例子里，Square并不是Rectangle的恰当的子类型，因为Rectangle的高度和宽度是独立可变的；相反，Square里的宽度和高度必须是一起变化的。如果User相信Rectangle的定义，很容易遇到麻烦，例子如下：

```Java
Rectangle r = …
r.setW(5);
r.setH(2);
assert(r.area() == 10);
```

例子中”…“如果是产生Square的实例对象，那么断言失败。  
解决这种违反LSP的唯一方法是在User中增加某种机制（比如if判断）去识别Rectangle类是不是Square的实例，因此User的行为取决于它依赖的Rectangle类的具体类别，也就不能替换了。

## LSP和架构

面向对象早期变革中，我们像之前的章节一样，把它作为使用继承的引导指南。但是多年来LSP已经演变成软件中接口和实现的边界原则。  
问题里的接口可以是多种形式的，可能是Java语言里的interface，有许多类实现。或者Ruby里许多class里有相同的方法签名。或者是有着反应相同REST接口的一组services。  
处理以上这些情况，LSP在很多方面都是可应用的，因为一个类依赖于良好定义的接口，可以替换这个接口的任意实现。  
理解架构角度中LSP的最好方式是看如果违反这个原则后系统架构所遇到的问题。

## 违反原则的例子

假设我们要为许多出租车构建一个聚合的分派服务。乘客通过访问我们的网站可以叫到离自己最近的出租车（不考虑出租车公司）。一旦乘客使用了，我们系统将用REST服务通知我们的分派系统选择最近的出租车。  
现在假设REST的分派服务的URI是司机数据库信息里的一部分，我们系统在选择了最合适乘客的司机后，从司机数据库信息获取URI，然后通知出租车。  
比如司机Bob有类似的URI分派地址：

```js
purplecab.com/driver/Bob
```

我们系统将追加分发信息到URI中，并用PUT方式发送，如下：

```js
purplecab.com/driver/Bob
    /pickupAddress/24 Maple St.
    /pickupTime/153
    /destination/ORD
```

很明显，所有的分派服务，对于所有的不同的公司都得满足同样的REST接口。他们都得解析pickupAddress , pickupTime , 和 destination字段。  
现在假设出租车公司Acme雇了一些开发人员，他们并没有看清规范，将destination字段写成了dest，Acme在我们这个地区是最大得出租车公司，Acme得CEO前妻是我们CEO的妻子，还有.......算了，我讲完了，我们的系统架构现在怎么样了？  
很明显，我们需要加入特殊的逻辑了，任何来自Acme的司机的分派请求都得用有别于其他司机得规则特殊去构造。  
解决这个问题最简单得就是在构造分派请求的模块里增加if语句：

```java
if (driver.getDispatchUri().startsWith("acme.com"))…
```

但是，没有哪个称职的架构师会允许系统里存在这样的构造，把”acme“这个词放进代码里本身就引起了可怕和难以理解的可能，更别提系统安全漏洞了。  
比如，要是Acme变得更成功，收购了Purple出租车公司，保留两家的品牌和网站，但要合并两家公司原有的系统，我们难道要在构造分派请求的模块里再加Purple的逻辑吗？  
我们的系统架构师必须隔离类似会产生bugs的系统，比如通过构造创建这类分发命令的模块，使用数据库配置去映射这些分发URI。比如配置类似:

| URI | Dispatch Format |
| :--- | :--- |
| Acme.com | /pickupAddress/%s/pickupTime/%s/dest/%s |
| \*.\* | /pickupAddress/%s/pickupTime/%s/destination/%s |

我们的架构师在REST服务的接口并不能相互替的情况，就得增加意义重大又复杂的机制来处理。

## 小结

LSP可以，也应该在架构层面上扩展，因为小小的违反替换原则都可能造成系统架构被大量需要额外引进的机制所污染。

