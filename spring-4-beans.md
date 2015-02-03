<h1 id="spring-core">Part III. 核心技术</h1>

本部分参考手册完全覆盖了Srping 框架的全部技术

首先是Spring IoC控制反转。深入彻底的IoC讲解之后，紧随其后的是全面解说Spring AOP。Spring有自己的AOP框架,该框架概念简单易于理解，能解决Java企业应用中80%的需求

Spring也集成了AspectJ，AspectJ是现今java领域功能最丰富、最成熟的AOP实现。

最后面的部分是tdd测试驱动开发，也是Spring 开发团队最为推崇的开发方法，主要内容有单元测试和spirng对集成测试的支持。Spring 团队发现，正确的使用Ioc，会使单元测试和集成测试更加简单（因为类中使用Setter和构造函数，将使它们更容易的配合，而无需使用set up组装）。同时，为测试弄了专门的单独章节，希望你能领悟这一点

* [Chapter 4, The IoC container](#beans)
* Chapter 5, Resources
* Chapter 6, Validation, Data Binding, and Type Conversion
* Chapter 7, Spring Expression Language (SpEL)
* Chapter 8, Aspect Oriented Programming with Spring
* Chapter 9, Spring AOP APIs
* Chapter 10, Testing  



<h2 id="beans">IoC容器</h2>  
<h3 id="beans-introduction">springIOC容器和beans简介</h3>  

本章讲解spring的控制反转(IoC)的spring 框架实现 [1] 原理. IoC 又名 依赖注入 (DI). 它是一个由对象定义依赖的处理手法，也就是如何与其他对象协同工作, 可以通过以下途径定义依赖：构造参数注入、工厂方法的参数注入、属性注入(是指对象实例化后或者从工厂方法返回一个实例后设置其属性)。容器创建bean时候， 注入 依赖。 这个控制倒转了, 因此得名控制反转 (IoC)。反转了哪些控制，不再是由bean自己控制依赖类的实例化和定位, 而是使用了类似于 服务定位 模式的机制来控制。


`org.springframework.beans` 和 `org.springframework.context` 这两个包是spring IOC容器的基础包. `BeanFactory` 接口 提供了各种配置，用于管理任何对象. `ApplicationContext` 是 `BeanFactory`的子接口. 它提供以下功能，使集成Spring’s AOP功能 更容易; 消息资源处理(用于国际化);事件发布;应用层指定上下文环境，像用于web应用的`WebApplicationContext`.

简而言之, `BeanFactory` 提供了配置框架和基础功能, `ApplicationContext` 增加了更多的企业应用用能. `ApplicationContext` 是 `BeanFactory`的超集, 是本章的spring IOC示例中的指定容器. 用`BeanFactory` 替代 `ApplicationContext`, 更多的信息请参看 Section 4.17, “The BeanFactory”.

应用中的对象并且是由spring 容器 管理的，被称为beans.就是对象，由spring容器管理的诸如实例化、组装等等操作.  bean可以由应用中的多个对象组成。Bean通过容器和配置元数据 ，使用反射技术，去组装依赖对象。

<h3 id="beans-basics">Container概述</h3>  
接口`org.springframework.context.ApplicationContext`代表了srping IoC 容器，负责实例化、配置和组装前面提到的beans。容器依据配置配置元数据去实例化、配置、组装。配置元数据可以用XML、Java 注解、或者Java编码表示。在配置元数据中，可以定义组成应用的对象，以及对象之间的依赖关系。

Spring 提供了一些开箱即用的`ApplicationContext`接口的实现。在单独的应用中，通常使用`ClassPathXmlApplicationContext`或者`FileSystemXmlApplicationContext`。当使用XML定义配置元数据时，可通过一小段xml配置使容器支持其他格式的配置元数据，比如Java 注解、Java Code。


大多数的应用场景中，不需要硬编码来实例化一个Spring IoC 的容器。举个栗子，web应用中，在web.xml中大概8行左右的配置就可以实例化一个Spring Ioc容器(see Section 4.16.4, “Convenient ApplicationContext instantiation for web applications”)。若再有STS(Spring eclipse 套件)，简单的钩钩点点即可完成此配置。

下面的示意图是spring工作原理。`ApplicationContext`将应用中的类与配置元数据相结合，实例化后，即可得到一个可配置、可执行的系统或应用。  
**The Spring IoC container**  
![spring 工作原理示意图](http://docs.spring.io/spring/docs/4.0.5.RELEASE/spring-framework-reference/htmlsingle/images/container-magic.png)

<h4 id="beans-factory-metadata">配置元数据</h4>
如上图所示，Spring IoC容器使用某种格式的配置元数据；配置元数据，就是告诉Ioc容器如何将对象实例化、配置、组装。

配置元数据是默认使用简单直观的xml格式，也是本章样例中使用最多的，这些样例程序用以说明spring Ioc 容器的核心概念和功能

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 基于XML的元数据并不是唯一的格式。Spring IoC容器已经和提及到的元数据格式完全解耦了。目前，很多码农都选择[Java-based configuration](#beans-java) 
  
欲了解其他元数据格式的使用，请参看:  
* [Annotation-based configuration](#beans-annotation-config): Spring 2.5 引进的支持java 注解配置元数据  
* [Java-based configuration](#beans-java):  Spring3.0时，将Spring JavaConfig project的很多功能集成到核心Spring框架中。Thus you can define beans external to your application classes by using Java rather than XML files.你可以使用java配置类定义bean，无需xml，该配置类与应用类无关。想要尝鲜的话，请参看`@Configuration`,`@Bean`,`@Import`,`@DependsOn`注解

Spring configuration consists of at least one and typically more than one bean definition that the container must manage.Spring配置由Spring bean的定义组成，这些bean必须被容器管理，至少1个，通常会有多个。基于XML的配置元数据，大概这么配置，根节点`<beans>`中配置子节点`<bean>`。Java configuration使用是这样的，一个带有`@Configuration`类注解的类中，方法上使用`@Bean`方法注解。

bean的定义要与应用中实际的类相一致。可以定义service 层的对象、Dao对象、类似Struts的表现成的对象、像Hibernate SessionFactories这样的基础对象，JMS队列等等。通常不会去定义细粒度域对象，因为它们由DAO或者Service负责创建、加载。然而，通过集成AspectJ，可以配置非Srping容器创建的对象。参看[Using AspectJ to dependency-inject domain objects with Spring](#aop-atconfigurable)



