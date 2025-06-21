# 设计模式

## 工厂方法模式（简单工厂、工厂方法、抽象工厂）

以咖啡店点餐系统为例，其基础设计结构如下：

![](https://secure2.wostatic.cn/static/q3GMirnqpWjimRNHY9jmXL/image.png?auth_key=1750484352-bTbEUa6f2N5SNECt5TFNTo-0-8e491452d033e918d1205aef36d5ddb5)

其中，

- **1.类图中的符号**
    - +：表示public
    - -：表示private
    - #：表示protected
- 2**.泛化关系**(继承)用带空心三角箭头的实线来表示
- 3.**依赖关系**使用带箭头的虚线来表示

### 简单工厂模式

简单工厂包含如下角色：

- **抽象产品：**定义了产品的规范，描述了产品的主要特性和功能。
- **具体产品：**实现或者继承抽象产品的子类
- **具体工厂：**提供了创建产品的方法，调用者通过该方法来获取产品。

改进后如下：

![](https://secure2.wostatic.cn/static/3DRUBDmMzcsFfThQBdVtZn/image.png?auth_key=1750484352-mGSRQQZxGPwiPeGR63uR7E-0-b35759084934c67303c63d619c46cadb)

工厂类：

![](https://secure2.wostatic.cn/static/vZcHGQFGHjmL8GJ8NKDMhS/image.png?auth_key=1750484352-c4Sn4i1UerQmsnV8jMMtwf-0-8c71dfefe336bd57186393133b29c43b)

增加新产品时还是需要修改工厂类的代码，**违背了“开闭原则”**。

咖啡店类：

![](https://secure2.wostatic.cn/static/fTnswaYVVnBuhq8BPyvseL/image.png?auth_key=1750484352-bE3rNeaKPNuPwPw7GpzcQG-0-ede8e5676e449f126e4816c1155c9435)

### 工厂方法模式

定义一个用于创建对象的接口，让子类决定实例化哪个产品类对象。工厂方法使一个产品类的实例化延迟到其工厂的子类。

工厂方法模式的主要角色：


- **抽象工厂（Abstract Factory）**

    提供了创建产品的接口，调用者通过它访问具体工厂的工厂方法来创建产品。
- **具体工厂（ConcreteFactory）**

    主要是实现抽象工厂中的抽象方法，完成具体产品的创建。

- **抽象产品（Product）**

    定义了产品的规范，描述了产品的主要特性和功能。

- **具体产品（ConcreteProduct）**

    实现了抽象产品角色所定义的接口，由具体工厂来创建，它同具体工厂之间一一对应。

![](https://secure2.wostatic.cn/static/pni4M6Nq3fUJShLg6dRWFm/image.png?auth_key=1750484352-nMuJZu4HEdLZejrpnehsgA-0-37061fe7db9b600a278e27856ff51810)

![](https://secure2.wostatic.cn/static/63Tcb98gC7tkgFeNh5qwJA/image.png?auth_key=1750484352-vhFzYjjAz2F8mhzC5biSc6-0-3afa271ad6fe960891add014b06e139c)



抽象工厂：

![](https://secure2.wostatic.cn/static/9PbnMpLPtduanGtyR2Kcik/image.png?auth_key=1750484352-ooU6C1tAenFTnRFpHSvz87-0-d8e205d5511fc5b07e3d1537efda565d)

具体工厂：

![](https://secure2.wostatic.cn/static/fhu2qEbmPtBe1KRP3mfZrE/image.png?auth_key=1750484352-ac7HGHTgzmAkuDcGzQdX1N-0-987192c0b2b29378182db447395b553c)

咖啡店类：

![](https://secure2.wostatic.cn/static/JHnFgxhnq8wVtMp5w9Z3S/image.png?auth_key=1750484352-q879DrGcxgvMD6t33ExgMu-0-ac8e92777cb1dc941269e8196520dc61)

通过咖啡店中传入不同的抽象工厂，产生不同的咖啡类型。

**优点：
**

- 用户只需要知道具体工厂的名称就可得到所要的产品，无须知道产品的具体创建过程；

- 在系统增加新的产品时只需要添加具体产品类和对应的具体工厂类，无须对原工厂进行任何修改，**满足开闭原则**；

**缺点：
**

- 每增加一个产品就要增加一个具体产品类和一个对应的具体工厂类，这**增加了系统的复杂度**。

### 抽象工厂模式

抽象工厂模式将考虑多等级产品的生产，将同一个具体工厂所生产的位于不同等级的一组产品称为一个产品族，下图所示


![](https://secure2.wostatic.cn/static/fb7GuwP5bixWKCbToNRXK/image.png?auth_key=1750484352-b1epikBooDzjnvGgRRqwN2-0-974547e97a31d8630dfcd39826124e8b)

- **产品族：**一个品牌下面的所有产品；例如华为下面的电脑、手机称为华为的产品族；
- **产品等级：**多个品牌下面的同种产品；例如华为和小米都有手机电脑为一个

抽象工厂模式是工厂方法模式的升级版本，工厂方法模式只生产一个等级的产品，而抽象工厂模式可生产多个等级的产品。

抽象工厂模式的主要角色如下：


- **抽象工厂（Abstract Factory）**

    提供了创建产品的接口，它包含多个创建产品的方法，可以创建多个不同等级的产品。
- **具体工厂（Concrete Factory）**

    主要是实现抽象工厂中的多个抽象方法，完成具体产品的创建。
- **抽象产品（Product）**

    定义了产品的规范，描述了产品的主要特性和功能，抽象工厂模式有多个抽象产品。
- **具体产品（ConcreteProduct）**

    实现了抽象产品角色所定义的接口，由具体工厂来创建，它 同具体工厂之间是多对一的关系。

**实现：**


现咖啡店业务发生改变，不仅要生产咖啡，还要生产甜点。同一个**产品等级**（**产品分类**）

- 咖啡：拿铁咖啡、美式咖啡 

- 甜点：提拉米苏、抹茶慕斯


同一个风味，就是同一个**产品族**（相当于同一个**品牌**）

- 美式风味：美式咖啡、抹茶慕斯

- 意大利风味：拿铁咖啡、提拉米苏


要是按照工厂方法模式，需要定义提拉米苏类、抹茶慕斯类、提拉米苏工厂、抹茶慕斯工厂、甜点工厂类，很容易发生**类爆炸**情况。

所以这个案例可以使用抽象工厂模式实现。类图如下：

![](https://secure2.wostatic.cn/static/shbDMr6m6Vhsn2sRt3HPEW/image.png?auth_key=1750484352-2rNcUZCYdvEJ7am9ScHcFm-0-f90a4b3ddbcf57a89245e89a0094157d)

调用思路：



![](https://secure2.wostatic.cn/static/uAAhddBAMB1fepPSE66Hsi/image.png?auth_key=1750484352-oJcDuroxeTnGVLdHQKfYby-0-1d6d2a89fc0c17ca73a429cd12eedf2e)

- **优点：**

    当一个产品族中的多个对象被设计成一起工作时，它能保证客户端始终只使用同一个产品族中的对象。
- **缺点：**

    当**产品族**中需要增加一个新的产品时，**所有的工厂类**都需要进行修改。

## 策略模式

**举例：**

同样是出行，有多种方式可以选择：

![](https://secure2.wostatic.cn/static/iMjua8xPnxFPYtMHfXd47e/image.png?auth_key=1750484352-awt7cYx69Vtxp1aRE5vHrb-0-43fd523d66ccb185af860daec6826ee3)

该模式定义了一系列算法，并将每个算法封装起来，使它们可以**相互替换**，且算法的变化不会影响使用算法的客户。

策略模式属于**对象行为模式**，它通过对算法进行封装，把使用算法的责任和算法的实现分割开来，并委派给不同的对象对这些算法进行管理。

**结构：**

策略模式的主要角色如下：


- **抽象策略（Strategy）类**

    这是一个抽象角色，通常由一个接口或抽象类实现。此角色给出所有的具体策略类所需的接口。
- **具体策略（Concrete Strategy）类**

    实现了抽象策略定义的接口，提供具体的算法实现或行为。

- **环境（Context）类**

    持有一个策略类的引用，最终给客户端调用。

**举例：**

一家百货公司在定年度的促销活动。针对不同的节日（春节、中秋节、圣诞节）推出不同的促销活动，由促销员将促销活动展示给客户。类图如下：

![](https://secure2.wostatic.cn/static/q3J7T1ugti6JWnBzn5DDQg/image.png?auth_key=1750484352-sDdKcafjrP8Fu36uDaxDXB-0-646296f971523d035f10f415850fc1ec)



定义百货公司所有促销活动的共同接口

![](https://secure2.wostatic.cn/static/7npS9FF9DT9H1iiwJgigQU/image.png?auth_key=1750484352-hNsCBvnA9JD8VKLagELUbE-0-23c1e394e7c4c3d913c5ce95056901d0)

定义具体策略角色（Concrete Strategy）：每个节日具体的促销活动：

![](https://secure2.wostatic.cn/static/xz1frJ7WfBJe5BrAy2SkAy/image.png?auth_key=1750484353-9AoUKQTuBjff98XncnT3vr-0-fe9ffd38db235df5f5f092017b2e7750)

定义环境角色（Context）：用于连接上下文，即把促销活动推销给客户，这里可以理解为销售员

![](https://secure2.wostatic.cn/static/anBzaM2PYPQgLjRFQ9X7Rb/image.png)

案例：多种方式可以进行登录

只要代码中有冗长的`if-else` 或`switch` 分支判断都可以采用**策略模式优化**

## 责任链模式

一个请求有多个对象可以处理，但每个对象的处理条件或权限不同。

**定义：**


又名职责链模式，为了避免请求发送者与多个请求处理者耦合在一起，将所有请求的处理者通过前一对象记住其下一个对象的引用而连成一条链；当有请求发生时，可将请求沿着这条链传递，直到有对象处理它为止。


比较常见的springmvc中的**拦截器**，web开发中的**filter过滤器**

![](https://secure2.wostatic.cn/static/aKkWfKWcifNaYCud8xdFvd/image.png)

**结构
**

职责链模式主要包含以下角色:


- **抽象处理者（Handler）角色**

    定义一个处理请求的接口，包含**抽象处理方法**和一个**后继连接**。
- **具体处理者（Concrete Handler）角色**

    实现抽象处理者的处理方法，判断能否处理本次请求，如果可以处理请求则处理，否则将该请求转给它的后继者。

- **客户类（Client）角色**

    创建处理链，并向链头的具体处理者对象提交请求，它不关心处理细节和请求的传递过程。

**案例**

![](https://secure2.wostatic.cn/static/eL6UkhSyz79CKkraFXKf8p/image.png)

抽象处理者

![](https://secure2.wostatic.cn/static/9tWFMhUcLjYh6aBzUNpZBk/image.png)

订单信息类：

![](https://secure2.wostatic.cn/static/6uKNhFxtrj1kdJ1xMzvJM5/image.png)

![](https://secure2.wostatic.cn/static/h8kt41gWGRV1ppSWfypD3Y/image.png)

具体处理者：

![](https://secure2.wostatic.cn/static/rQPTaUgKQzuP1HLcjnmmny/image.png)

![](https://secure2.wostatic.cn/static/eBGXZcsC3rPZL4bdyDm4jr/image.png)

客户类：

![](https://secure2.wostatic.cn/static/wG9X7Ua6MGyywGdnP8vMUP/image.png)

![](https://secure2.wostatic.cn/static/gZkbSGSenDtA3EsK5eeKCN/image.png)

**优缺点**

**优点**

- **降低**了对象之间的**耦合度**
- 该模式降低了请求发送者和接收者的耦合度。

- **增强**了系统的**可扩展性**
- 可以根据需要增加新的请求处理类，**满足开闭原则**。
- 增强了给对象指派职责的灵活性

- 当工作流程发生变化，可以动态地改变链内的成员或者修改它们的次序，也可动态地新增或者删除责任。
- 责任链简化了对象之间的连接

- 一个对象只需保持一个指向其后继者的引用，不需保持其他所有处理者的引用，这避免了使用众多的 if 或者 if···else 语句。责任分担每个类只需要处理自己该处理的工作，不能处理的传递给下一个对象完成，
- 明确各类的责任范围，符合类的单一职责原则。

**缺点：**


- 不能保证每个请求一定被处理。由于一个请求没有明确的接收者，所以不能保证它一定会被处理，该请求可能一直传到链的末端都得不到处理。
- 对比较长的职责链，请求的处理可能涉及多个处理对象，**系统性能**将**受到一定影响**。
- 职责链建立的**合理性要靠客户端来保证**，增加了客户端的复杂性，可能会由于职责链的错误设置而导致系统出错，如可能会造成循环调用。

