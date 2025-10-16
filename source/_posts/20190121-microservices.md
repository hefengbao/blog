---
title: 微服务
date: 2019-01-21 17:17:17
tags:
categories:
---
### 微服务定义

微服务将功能分解为由RESTful API松散耦合的独立应用程序。例如，eBay在2006年开发的独立Java servlet应用程序，用于处理用户、项目、账户、反馈、交易和70多项其他要素，其中每一个逻辑功能应用程序都是一个微服务。

这些微服务都是独立的，并且不共享数据层，每一个都有自己的数据库和负载均衡器，隔离是微服务架构的关键要素。不同的微服务需要不同的扩展技术，例如，有些微服务可能使用关系型数据库，而其他的可能使用NoSQL数据库。

### 微服务的好处

微服务架构可以将内部架构非常复杂的大型单体应用程序，分解成小型的可独立扩展的应用程序。每个微服务都很小，开发、更新和部署也不太复杂。

在考虑微服务时，为什么首先要将这些功能都构建到单个应用程序中呢？至少在理论上，你可以想象为：它们可以存在于单独的应用程序和数据孤岛中，这不会有什么大问题。例如，如果在拍卖中收到两份投标，但只有四分之一的销售收到反馈，那么在一天中的任何时间里，投标服务的活跃程度至少是反馈应用程序的八倍。如果将这些组合到一个应用程序中，你最终运行并更新的代码将比经常需要的代码更多。在本质上，将不同的功能组分隔成不同的应用程序，自有其道理。

而且，围绕微服务架构进行开发可获得一些隐性优势，例如可与PaaS、Docker和Linux容器等新技术紧密结合。

以微服务方式构建应用程序不仅可使应用程序更加灵活，更具可扩展性，它们还增加了构建应用程序的团队的可伸缩性。使用单一代码，你可以建立一支大型团队，虽然团队成员能够处理大段代码，但是他们始终彼此掣肘，随着代码整体数量的增长，开发速度会呈指数级下降。

不过，借助微服务架构，应用程序可由小型的、分散的开发团队进行构建，他们可以独立地工作和修改微服务。这样做的好处是升级服务和添加功能更加容易，软件和开发流程也将变得更加灵活。

![](images/8T4OTyhPmO.png)

### 微服务的挑战

但每种架构都有优点和缺点。虽然优点明显，但是微服务架构也带来了一系列难以解决的新问题--特别是记录、监控、测试和调试去中心化且松耦合的新应用程序。

如果有一个漏洞，那么哪个微服务应当对此负责呢？微服务之间的相互依存关系使得这个问题很难回答。微服务通常通过轻量级JSON REST API相互交换数据。与其前身XML-RPC和SOAP不同的地方是，REST接口的定义正变得越来越松散。虽然这些轻量级API更灵活，更容易扩展，但是它们增加了需要监控的新接口，这可能会产生中断或导致出现漏洞。

在单体应用程序中，你可以在代码中添加调试钩子，并在逻辑上逐步执行每个执行层，以发现问题区域。如果当数十个甚至数百个独立的微服务使用松散定义的API相互交换数据，那么在处理由这些微服务组成的网格时，你就不能再这么做了。

尽管如此，只要精心安排，这些困难是可以克服的。一些调试工具可以提供帮助，不过你可能需要根据其他部分的情况整合自己的解决方案。

### 微服务与容器和PaaS的关系

有一种常见的误解是，如果要使用微服务，那么你就需要使用PaaS或Linux容器。其实事实根本不是这么回事。你可以在没有微服务的情况下使用PaaS和Linux容器，也可以在没有PaaS或Linux容器的情况下使用微服务。它们彼此并不需要对方。

不过，它们之间确实能够很好地相互补充。无论是Heroku等公有云，还是Cloud Foundry或者OpenShift等私有云，PaaS环境都可以优化运行许多小型应用程序。像将330万行C ++应用程序移植到PaaS平台的事情永远都不会发生。

如果将应用程序分解为小的、可独立扩展的自足型应用程序，那么这些小型应用程序通常都非常适合在PaaS环境中运行。

同样，Linux容器更适合如微服务这样小型的无状态应用程序，而不是大型的单体应用程序。

毕竟，虚拟机和Linux容器之间最大和最明显的区别之一是缺少状态。虚拟机可通过配置保持其状态，而Linux容器的架构在本质上已经不再与基础映像有任何差异。你可以在Linux容器中安装状态文件夹，但是除非你提交更改，否则容器本身不会进行更改。

微服务架构的横向扩展理念促进了无共享、无状态应用程序的观念。它们不存储或修改底层文件系统。这就是为什么人们容易将微服务与Linux容器混为一谈，原因在于两者都不保留状态。

### 微服务与SOA的关系

微服务与SOA（面向服务的架构）关系密切，但又存在明显差异。从表面上看，SOA与SOAP和XML-RPC相关联，而微服务则与JSON相关联。但在某些方面，相关的API格式有着明显的外观差异。

同样，SOA使用企业服务总线，而微服务使用更轻量级的发布-订阅服务总线。尽管后者更为轻便，但原理是相似的。

微服务架构和SOA之间最大的区别在于微服务必须是可独立部署的，而SOA服务通常在部署整体中实现。

http://www.ccw.com.cn/intelumbrellacampaign/163yun/index.php/Home/Index/wenzhang/id/5818/lanid/8



```
In microservice architecture, multiple loosely coupled services work together. Each service focuses on a single purpose and has a high cohesion of related behaviors and data.
```

This definition includes three microservice design principles:

- *Single purpose* — each service should focus on one single purpose and do it well.
- *Loose coupling* — services know little about each other. A change to one service should not require changing the others. Communication between services should happen only through public service interfaces.
- *High cohesion* — each service encapsulates all related behaviors *and data*together. If we need to build a new feature, all the changes should be localized to just one single service.

![](images/Three-Principles-of-Modeling-Microservices.png)

https://medium.engineering/microservice-architecture-at-medium-9c33805eb74f