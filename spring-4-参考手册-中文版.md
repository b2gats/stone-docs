# Part III. Core Technologies

This part of the reference documentation covers all of those technologies that are absolutely integral to the Spring Framework.

Foremost amongst these is the Spring Framework’s Inversion of Control (IoC) container. A thorough treatment of the Spring Framework’s IoC container is closely followed by comprehensive coverage of Spring’s Aspect-Oriented Programming (AOP) technologies. The Spring Framework has its own AOP framework, which is conceptually easy to understand, and which successfully addresses the 80% sweet spot of AOP requirements in Java enterprise programming.

Coverage of Spring’s integration with AspectJ (currently the richest - in terms of features - and certainly most mature AOP implementation in the Java enterprise space) is also provided.

Finally, the adoption of the test-driven-development (TDD) approach to software development is certainly advocated by the Spring team, and so coverage of Spring’s support for integration testing is covered (alongside best practices for unit testing). The Spring team has found that the correct use of IoC certainly does make both unit and integration testing easier (in that the presence of setter methods and appropriate constructors on classes makes them easier to wire together in a test without having to set up service locator registries and suchlike)… the chapter dedicated solely to testing will hopefully convince you of this as well.

* [Chapter 4, The IoC container](#beans)
* Chapter 5, Resources
* Chapter 6, Validation, Data Binding, and Type Conversion
* Chapter 7, Spring Expression Language (SpEL)
* Chapter 8, Aspect Oriented Programming with Spring
* Chapter 9, Spring AOP APIs
* Chapter 10, Testing

## <span id="beans">4. IoC容器</span>
### 4.1 springIOC容器和beans简介
本章包含spring的控制反转(IoC)的spring 框架实现 [1] 原理. IoC 又名 依赖注入 (DI). 它是一个有对象定义依赖的处理手法，也就是和和与其他对象协同工作, 可以通过以下途径定义依赖：构造参数、工厂方法的参数、在它构造实例后或者通过一个工厂方法返回实例后设置对象属性。容器创建bean时候， 注入 依赖。 这个过程倒转了, 因此得名控制反转 (IoC)。反转了哪些控制？不再是由bean自己控制依赖类的实例化和定位, 类似于 服务定位 模式的机制。


`org.springframework.beans` 和 `org.springframework.context` 这两个包是spring IOC容器的基础包. `BeanFactory` 接口 提供了各种配置，用于管理任何对象. `ApplicationContext` 是 `BeanFactory`的子接口. 它使集成Spring’s AOP功能 更容易; 消息资源处理(用于国际化),事件发布;应用层指定上下文环境，像用于web应用的`WebApplicationContext`.

简而言之, `BeanFactory` 提供了配置框架和基础功能, `ApplicationContext` 增加了更多的企业应用用能. `ApplicationContext` 是 `BeanFactory`的超集, 是本章的spring IOC示例中的指定容器. 用`BeanFactory` 替代 `ApplicationContext`, 更多的信息请参看 Section 4.17, “The BeanFactory”.

应用中的对象并且是由spring 容器 管理的，被称为beans.就是对象，由spring容器管理的诸如实例化、组装等等操作. , bean可以由应用中的多个对象组成。Bean通过容器和配置元数据 ，使用反射技术，去组装 依赖对象。

### 4.2 Container 概述
接口`org.springframework.context.ApplicationContext`代表了srping IoC 容器，负责实例化、配置和组装前面提到的beans。容器依据配置配置元数据去实例化、配置、组装。配置元数据可以用XML、Java 注解、或者Java编码表示。在配置元数据中，可以定义组成应用的对象，以及对象之间的依赖关系。

Spring 提供了一些开箱即用的`ApplicationContext`接口的实现。在单独的应用当中，通常使用`ClassPathXmlApplicationContext`或者`FileSystemXmlApplicationContext`。当使用XML定义配置元数据时，可通过一小段xml配置使容器支持其他格式的配置元数据，比如Java 注解、Java Code。


大多数的应用场景中，不需要硬编码来实例化一个Spring IoC 的容器。举个栗子，web应用中，在web.xml中大概8行左右的配置就可以实例化一个Spring Ioc容器(see Section 4.16.4, “Convenient ApplicationContext instantiation for web applications”)。若再有STS(Spring eclipse 套件)，简单的钩钩点点即可完成此配置。

下面的示意图是spring工作原理。`ApplicationContext`将应用中的类与配置元数据相结合，实例化后，即可得到一个可配置、可执行的系统或应用。
![spring 工作原理示意图](http://docs.spring.io/spring/docs/4.0.5.RELEASE/spring-framework-reference/htmlsingle/images/container-magic.png)
