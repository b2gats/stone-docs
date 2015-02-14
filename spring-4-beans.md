 
<div style="font-size:34px">Spring4参考手册中文版</div>

<h1>作者简介</h1>  
翻译 石永明  <shiyongming@sinosoft.com.cn>  
顾问 张丙天  

**石永明** 现任中科软科技股份有限公司应用系统事业群部技术副总监、首席架构师，2008年加入中科软。擅长SOA、企业信息化架构，精通Java、Spring，在多线程、io、虚拟机调优、网络通信及支撑大型网站的领域有较多经验，对技术有浓厚的兴趣。现致力于无线、数据、业务平台、组件化方面取得突破

**张丙天**  

<h1>前言</h1>
我是前言。


<h1 id="spring-core">Part III. 核心技术</h1>
本部分参考手册完全覆盖了Srping 框架的全部技术

首先是Spring IoC控制反转。深入彻底的IoC讲解之后，紧随其后的是全面解说Spring AOP。Spring有自己的AOP框架,该框架概念简单易于理解，能解决Java企业应用中80%的需求

Spring也集成了AspectJ，AspectJ是现今java领域功能最丰富、最成熟的AOP实现。

最后面的部分是tdd测试驱动开发，也是Spring 开发团队最为推崇的开发方法，主要内容有单元测试和spirng对集成测试的支持。Spring 团队发现，正确的使用Ioc，会使单元测试和集成测试更加简单（因为类中使用Setter和构造函数，将使它们更容易的配合，而无需使用set up组装）。同时，为测试弄了专门的单独章节，希望你能领悟这一点

* [Chapter 4, The IoC container](#beans)
* [Chapter 5, Resources](#resources)
* [Chapter 6, Validation, Data Binding, and Type Conversion](#validation)
* [Chapter 7, Spring Expression Language (SpEL)](#expressions)
* [Chapter 8, Aspect Oriented Programming with Spring](#aop)
* [Chapter 9, Spring AOP APIs](#aop-api)
* [Chapter 10, Testing  ](#testing)

<h2 id="beans">IoC容器</h2>  
<h3 id="beans-introduction">springIOC容器和beans简介</h3>  

本章讲解spring的控制反转(IoC)的spring 框架实现 [1] 原理. IoC 又名 依赖注入 (DI). 它是一个由对象定义依赖的处理手法，也就是如何与其他对象协同工作, 可以通过以下途径定义依赖：构造参数注入、工厂方法的参数注入、属性注入(是指对象实例化后或者从工厂方法返回一个实例后设置其属性)。容器创建bean时候， 注入 依赖。 这个控制倒转了, 因此得名控制反转 (IoC)。反转了哪些控制，不再是由bean自己控制依赖类的实例化和定位, 而是使用了类似于 服务定位 模式的机制来控制。


`org.springframework.beans` 和 `org.springframework.context` 这两个包是spring IOC容器的基础包. `BeanFactory` 接口 提供了各种配置，用于管理任何对象. `ApplicationContext` 是 `BeanFactory`的子接口. 它提供以下功能，使集成Spring’s AOP功能 更容易; 消息资源处理(用于国际化);事件发布;应用层指定上下文环境，像用于web应用的`WebApplicationContext`.

简而言之, `BeanFactory` 提供了配置框架和基础功能, `ApplicationContext` 增加了更多的企业应用用能. `ApplicationContext` 是 `BeanFactory`的超集, 是本章的spring IOC示例中的指定容器. 用`BeanFactory` 替代 `ApplicationContext`, 更多的信息请参看 Section 4.17, “The BeanFactory”.

应用中的对象并且是由spring 容器 管理的，被称为beans.就是对象，由spring容器管理的诸如实例化、组装等等操作.  bean可以由应用中的多个对象组成。Bean通过容器和配置元数据 ，使用反射技术，去组装依赖对象。

<h3 id="beans-basics">容器概述</h3>  
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

Spring配置由Spring bean的定义组成，这些bean必须被容器管理，至少1个，通常会有多个。基于XML的配置元数据，大概这么配置，根节点`<beans>`中配置子节点`<bean>`。Java configuration使用是这样的，一个带有`@Configuration`类注解的类中，方法上使用`@Bean`方法注解。

bean的定义要与应用中实际的类相一致。可以定义service 层的对象、Dao对象、类似Struts的表现成的对象、像Hibernate SessionFactories这样的基础对象，JMS队列等等。通常不会去定义细粒度域对象，因为它们由DAO或者Service负责创建、加载。然而，通过集成AspectJ，可以配置非Srping容器创建的对象。参看[Using AspectJ to dependency-inject domain objects with Spring](#aop-atconfigurable)

下面的样例展示了基于XML配置元数据的基本格式:  

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">
    <!-- bean的详细配置 -->
    </bean>
    
    <bean id="..." class="...">
    <!-- bean的详细配置 -->
    </bean>
    
    <!-- 其他bean -->

</beans>
```

`id`属性是个字串，是bean的唯一标示符。`class`属性定义了bean的类型，要使用类的全限定类名（含有包路径）。`id`属性的值，可以作为合作bean的引用标示符。上面未展示如何引用其他对象；详情参看[Dependencies](#beans-dependencies)


<h4 id='beans-factory-instantiation'>容器实例化</h4>  
Spring IoC的实例化易如反掌。`ApplicationContext`构造函数支持定位路径，定位路径也可以是多个，它是标识实际资源的字串，容器使用该标识加载配置元数据，支持多种资源，比如：本地文件系统、CLASSPATH等等。  
```java
	ApplicationContext context = 
		new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});  
```
![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
在学习了Spring IoC容器之后，也许你想了解更多的Spring的资源，如前所述在第6章，资源使用URI语法定位输入流，Spring提供了方便的机制读取输入流。在第6.7章[“Application contexts and Resource paths”](#resources-app-ctx)，专门讲述5用 资源路径构造应用上下文，资源路径也是惯用手法。  
接下来的样例展示了配置service层对象:  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- services -->

	<bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
	<property name="accountDao" ref="accountDao"/>
	<property name="itemDao" ref="itemDao"/>
	<!-- 有关属性配置 -->
	</bean>

	<!--更多的Service bean -->

</beans>
```

下面的样例展示了数据访问对象`dao.xml`配置:  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
    <!-- additional collaborators and configuration for this bean go here -->
    </bean>
    
    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
    <!-- additional collaborators and configuration for this bean go here -->
    </bean>
    
    <!-- more bean definitions for data access objects go here -->
</beans>
```
上述内容中service层由`PetStoreServiceImpl`类、2个dao对象`JpaAccountDao`和`JpaItemDao`(基于JPA ORM标准)。属性`name`元素引用了JavaBean的属性，*ref*元素引用了其他bean定义。这个引用表示实际对象之间的引用依赖。配置一个对象的依赖，详情请参看[Dependencies](#beans-dependencies)

<h5 id='beans-factory-xml-import'>引入基于xml的元数据</h5>
多个配置文件共同定义bean非常有用。通常，每个XML配置文件在你的架构中代表一个逻辑层或者一个模块。

你可以使用应用上下文(applicationContext)的构造函数去加载所有xml中定义的bean。这个构造函数使用多个资源定位，就像前面中提到的。或者，也可以用一个或者多个资源引用，即使用`<import/>`标签加载其他文件定义的bean。举个栗子：
```xml
	<beans>
		<import resource="services.xml"/>
		<import resource="resources/messageSource.xml"/>
		<import resource="/resources/themeSource.xml"/>
		
		<bean id="bean1" class="..."/>
		<bean id="bean2" class="..."/>
	</beans>
```
上例中，从三个外部文件加载定义的bean:`services.xml`,`messageSource.xml`,`themeSource.xml` 。被引入的文件的路径对于引入配置文件来说都是相对路径，所以`service.xml`必须在引入配置文件的相同文件路径或者相同的类路径中。而`messageSource.xml`和`themeSource.xml`必须在引入配置文件所在的文件夹下的`resouce`文件夹下。正如你所看到的 `/`开头会被忽略掉，因为这些路径是相对路径，推荐不要使用`/`开头的格式。导入(imported)文件内容，包含根节点`<beans/>`，配置中XML bean定义 必须经过Spring语法校验通过。


![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)     
> 使用"../"表示父目录的相对路径是可以的，但是真心不推荐这样创建一个依赖应用外部文件的做法。尤其指出，使用*"classpath:"*资源类型的URLs(像这样："classpath:../services.xml")，也是不推荐的，因为运行时处理过程会选择"最近的"根路径然后引入他的父目录配置文件。Classpath配置的改变，会导致应用选择一个不同的、错误的目录。
> 你可以使用全路径限定资源定位取代相对路径，比如："file:C:/config/services.xml" 或者"classpath:/config/services.xml"。还有，你可以使用抽象路径来解耦应用和配置文件。使用一个逻辑定位更可取 ,比如：通过"${..}"占位符，使用JVM运行时计算出的路径。

<h4 id='beans-factory-client'>使用容器</h4>
`ApplicationContext`是一个高级工厂的接口，能维护各种bean以及他们之间依赖的注册。使用方法`T getBean(String name, Class<T> requiredType)`，就能从定义的bean中获取实例。
`ApplicationContext`能让你读取bean定义、访问他们，如下：  
```java
// create and configure beans
ApplicationContext context =
new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();  
```

使用`getBean()`从beans中获取实例。`ApplicationContext`接口有几种方法可以办到,但是理想的做法是不要使用他们。实际上，应用中根本就不该使用`getBean()`方法，这样就不依赖Sprig API了。比如，Spring集成了很多web框架，为各种web框架类提供了依赖注入，比如web框架的Controller和JSF-managed beans

<h3 id='beans-definition'>Bean概述</h3>  
Spring IoC容器管理一个或多个bean。这些bean根据提供给容器的配置元数据创建的，比如使用XML格式`<bean/>`定义。  
在容器内部，这些bean的定义用 `BeanDefinition`对象表示，`BeanDefinition`包含了下列元数据：  
* 全路径（含包路径）类名：代表bean的实际实现类。
* Bean行为配置元素，它规定了bean在容器中行为(作用域，生命周期回调函数等等)
* 引用其他bean，就是为了bean能正常工作而所需的其他bean的引用。这些引用类也称为合作类或者依赖类。
* 其他配置，为实例设置的其他属性配置。比如说，管理连接池的bean的连接数，池大小的上限。

这些元数据将转换成bean定义(`BeanDefinition`类）的属性。

**The bean definition**  

**属性**  |**详情**  
------------- | -------------
**class** |[Section 5.3.2, “Instantiating beans”](#beans-factory-class)  
**name**  |[Section 5.3.1, “Naming beans”](#beans-beanname)  
**scope** |[Section 5.5, “Bean scopes”](#beans-factory-scopes)  
**constructor arguments**|[Section 5.4.1, “Dependency injection”](#beans-factory-collaborators)  
**properties**|[Section 5.4.1, “Dependency injection”](#beans-factory-collaborators)  
**autowiring mode**|[Section 5.4.5, “Autowiring collaborators”](#beans-factory-autowire)  
**lazy-initialization mode**|[Section 5.4.4, “Lazy-initialized beans”](#beans-factory-lazy-init)  
**initialization method**|[the section called “Initialization callbacks”](#beans-factory-lifecycle-initializingbean)  
**destruction method**|[the section called “Destruction callbacks”](#beans-factory-lifecycle-disposablebean)  

除了bean的信息以外，`BeanDefinition`也包含创建特殊bean的信息，`ApplicationContext`的实现也允许注册由用户创建而非IoC容器创建的对象。通过访问ApplicationContext’s BeanFactory的方法`getBeanFactory()`，该方法返回BeanFactory的实现`DefaultListableBeanFactory`。`DefaultListableBeanFactory`类支持这种注册，通过`registerSingleton(..)`和`registerBeanDefinition(..)`方法实现。然而，典型的应用只用元数据定义的bean就可以单独运行。


<h4 id='beans-beanname'>beans命名</h4>
bean有一个或者多个标示符。这些标示符必须是所在容器范围内必唯一的。通常情况一下，一个bean仅有一个标示符，如果有需求需要多个，多出来的将被当做别名。

在XML格式配置元数据中，使用 `id` 或者 `name` 属性来作为bean的标示符。`id`属性只能有1个。命名规范是字符数字混编（myBean,fooService,等等），但也支持特殊字符，可以包含。若想给bean起个别名，则可使用`name`属性来指定，可以是多个，用英文的逗号(`,`)分隔、分号(`;`)也行、空格也行。注意，在Spring3.1以前，`id`属性定义成了`xsd:ID`类型，该类型强制为字符*（译者心里说：估计字母+特殊字符，不支持数字的意思，有待验证，没工夫验证去了，翻译进度太慢了。再说了，现在都用4了，你再说3.0怎么着怎么着，那不跟孔乙己似的跟别人吹嘘茴香豆有四种写法）*。3.1版开始，它被定义为`xsd:string`类型。注意，bean `id`的唯一性约束依然被容器强制使用，尽管xml解析器不再支持了。*译者注：在spring3（含）以前，id是可以相同的，容器会替换相同id的bean，而在新版中，容器初始化过程中发现id相同抛出异常，停止实例化*

`id` 和`name`属性不是bean所必须的。若未明确指定`id`或者`name`属性，容器会给它生成一个唯一name属性。当然了，如果你想通过bean的`name`属性引用，使用`ref`元素方式，或者是类似于[Service Locator模式](#beans-servicelocator)方式检索bean(*译者想：应该是指调用ApplicationContext.getBean()方法获取bean，类似这种方式。Service Locator是一种设计模式，其实换个名字是不是更合适，DL（Dependency Lookup依赖查找）。虽然现在我也不明白，但是下面有专门的章节讲解，翻到时候再详细了解*)，就必须给bean指定	`name`了。之所以支持无name bean特性，是为了使内部类自动装配。
```
Bean命名规范

bean命名规范使用java命名规范中实例属性名(也称域，Field)规范。小写字母开头的驼峰式。像这样
(不包括单引号)`accountManager`，`accountService`，`userDao`，
`loginController`，等等

规范的命名使配置易读易理解。若使用Spring AOP，通过名字增强(译注：大多数Spring AOP教材中
的 通知)一坨bean时，规范的命名将带来极大的方便。
```	
<h5 id='beans-beanname-alias'>bean定义之外设置别名</h5>
定义的bean内，可以给bean多个标识符，组合`id`属性值和任意数量的`name`属性值。这些标识符均可作为该bean的别名，对于有些场景中,别名机制非常有用，比如应用中组件对自身的引用。(*译注：一个类持有一个本类的实例作为属性，看起来应该是这样的，以下代码为推测，可以执行*)  
**Bean类**

```java	
public class SomeBean {
	//注意看这个属性，就是本类
	private SomeBean someBean;
	
	public SomeBean(){}
	
	public void setSomeBean(SomeBean someBean) {
		this.someBean = someBean;
	}
}
```

**配置元数据**  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--看bean的别名，使用,/;/空格 分隔都是可以是-->
	<bean id="someBeanId" name="someBean,someBeanA;someBeanB someBeanC" class="com.example.spring.bean.SomeBean">
		<!--将别名为someBeanA 的bean 注入给 id为someBeanId 的bean的属性 'someBean'-->
		<property name="someBean" ref="someBeanA"></property>
	</bean>
</beans>
```

**测试代码**  
```java
@ContextConfiguration
@RunWith(SpringJUnit4ClassRunner.class)
public class SomeBeanTests {
	
	@Autowired
	@Qualifier("someBeanId")
	private SomeBean someBean;
	
	@Test
	public void testSimpleProperties() throws Exception {
	}
	
}
```
在bean的定义处指定所有别名有时候并不合适，然而，在其他配置文件中给bean设置别名却更为恰当。此法通常应用在大型系统的场景中，配置文件分散在各个子系统中，每个子系统都有本系统的bean定义。XML格式配置元数据，提供`<alias/>`元素，可以搞定此用法。
```xml
<alias name="fromName" alias="toName"/>
```

这种情况下，在同容器中有个叫`fromName`的bean，或者叫其他的`阿猫阿狗`之类的，再使用此别名定义之后，即可被当做`toName`来引用。

举个栗子，子系统A中的配置元数据也许引用了一个被命名为`subsystemA-dataSource`的bean。子系统B也许引用了一个`subsystemB-dataSource`。将这两个子系统整合到主应用中，而主应用使用了一个`myApp-dataSource`，为了使3个bean引用同一个对象，得在MyApp配置元数据中使用别名定义:
```xml
<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
<alias name="subsystemA-dataSource" alias="myApp-dataSource" />
```

现在，每个组件和主应用都能通过bean 名引用dataSource，而bean名都是唯一的保证不与其他定义冲突(实际上创建了一个命名空间),但他们引用的都是同一个bean。

**Java-configuration**

如果你使用了`Java-configuration`，`@Bean`注解也提供了别名，详见[Section 5.12.3, “Using the @Bean annotation”](#beans-java-bean-annotation)

<h4 id='beans-factory-class'>bean实例化</h4>  
bean的定义，本质是如何创建一个或多个对象的配方。容器被请求时，会解析配置元数据中的bean定义并封装，使用封装配置创建（或者获取）对象实例。

若使用XML格式配置元数据，得为将要实例化的对象指定类型(或者说是类)，使用`<bean/>`元素的`class`属性实现。`class`属性 映射到`BeanDefinition`类实例的`Class`属性（域），这个`class`属性是`<bean/>`元素必须的。(例外情况，参看“[Instantiation using an instance factory method”](#beans-factory-class-instance-factory-method) 和 [Section 5.7, “Bean definition inheritance”](#beans-child-bean-definitions)。使用`Class`域属性，通过以下两种方式：  

* 通常，通过指定bean的`class` 属性，容器使用反射调用其构造函数直接创建bean，有点像Java 编码中使用`new`操作符。  
* 指定`class`实际类含有用于创建对象的静态工厂方法，这是不常使用的场景，容器会调用类的静态工厂方法创建bean。调用静态工厂方法返回的对象类型也许是相同类型，也许完全是其他类。
  
  
<div class="sidebar">
<b>内部类命名</b> 若要定义静态内部类，得将类名劈开。<br><br>
举例来说，现在在<span class="scode">com.example</span>包有个类<span class="scode">Foo</span>,该类有静态内部类<span class="scode">Bar</span>,定义<span class="scode">Bar</span>的Spring bean的<span class="scode">`class`</span>属性差不多是这样<br><br>
<code class="scode">com.example.Foo$Bar</code> <br>  
<br>
注意<span class="scode">$</span>字符，用它来分隔内部类名和外围类名
</div>

<h4 id='beans-factory-class-ctor'>用构造函数实例化</h4>
若是使用构造函数方式创建bean，所有的常规类都可以使用Spring来创建、管理。也就是说，开发的类无需实现任何特殊接口或者使用某种特殊编码风格。仅需指定bean的`class`即可。对于特殊的bean管理，取决于你使用的IoC类型，也许需要一个默认的空构造。  

Spring IoC容器几乎能管理任何你需要管理的类，不局限于真正的`JavaBeans`。大多数Spring的用户心中，真正的`JavaBean`是这样的：仅有1个默认的无参构造函数、属性、setter、getter。嗯，比如，现在需要使用一个废弃连接池，它肯定不符合`JavaBean`规范，Spring照样能管理。

使用XML格式配置元数据 定义bean的`class`，如下所示：  
```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>  
```
如何为构造函数指定参数？如何在对象实力话之后设置其属性？请参看[Injecting Dependencies](#beans-factory-collaborators)

<h5 id='beans-factory-class-static-factory-method'>使用静态工厂方法实例化</h5>
定义使用使用静态工厂方法创建的bean时，得指定工厂方法类的作为`class`属性值，并且还得指定工厂方法类中用于创建bean的方法名称，作为`factory-method`属性值。工厂方法可以有参数，调用该方法即可返回对象实例，就像通过构造函数创建对象实例一样。此种bean定义是为了兼容遗留系统中的静态工厂

下面的bean定义，是使用工厂方法创建bean的方式。定义中，无需指定返回对象的类型(class)，而是指定工厂方法类的`class`。下例中，`createInstance()`方法必须是一个`static`静态方法。
```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>  
```
继续
```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}
    
    public static ClientService createInstance() {
    return clientService;
    }
}	
```

<h5 id='beans-factory-class-instance-factory-method'>使用实例工厂方法实例化</h5>
和[静态工厂方法](#beans-factory-class-static-factory-method)类似的还有实例工厂方法，使用实例工厂方法的方式实例化，是调用容器中已存在的bean的一个非静态方法来创建一个bean。用法是，1、`class`属性置空设置。 2、设置`factory-bean`属性，其值为当前容器(或者父容器)中bean的名字，该bean包含可供调用的创建对象的实例方法。3、设置`factory-method`属性，其值为工厂方法名。
```xml
<!-- 工厂类, 包含一个方法createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
<!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

工厂类如下
```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();
    private DefaultServiceLocator() {}
    
    public ClientService createClientServiceInstance() {
    return clientService;
    }
}
```

工厂类可以有多个工厂方法:
```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```
工厂类如下:
```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();
    private static AccountService accountService = new AccountServiceImpl();

    private DefaultServiceLocator() {}

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }

}
```

上例中展示了工厂类本身也可以通过 DI 管理和配置。参看[DI详情](#beans-factory-properties-detailed)

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
>Srping 资料中,factory bean是指一个Spring配置的bean，该bean能通过实例或者静态工厂方法创建对象。对比之下，`FactoryBean`(注意大写)是指Spring术语`FactoryBean`。这段没太理解，解释factory bean和`FactoryBean`。

<h3 id='beans-dependencies'>依赖</h3>
企业应用绝不会只有1个简单对象（或者说Spring bean）。哪怕是最简单的应用，也会包含许多对象协同工作。下一章节讲述，如何为真正的应用定义大量的、独立的bean，并让这些对象一起合作。

<h4 id="beans-factory-collaborators">依赖注入</h4>
*依赖注入(DI)*，是一个有对象定义依赖的手法，也就是，如何与其他对象合作，通过构造参数、工厂方法参数、或是在对象实例化之后设置对象属性，实例化既可以构造也可以是使用工厂方法。容器在它创建bean之后注入依赖。这个过程从根本上发生了反转，因此又名控制反转（Ioc），因为Spring bean自己控制依赖类的实例化或者定位 ，Spring bean中就有依赖类的定义，容器使用依赖类构造器创建依赖类实例，使用*Service Locator*模式`定位依赖类。

DI机制使代码简洁，对象提供它们的依赖，解耦更高效。对象无需自己查找依赖。同样的，类更容易测试，尤其当依赖接口或者抽象类时，测试允许在单元测试中使用`stub`或者`mock`（模拟技术）实现。

DI有2种主要方式，[构造注入](#beans-constructor-injection) 和 [setter注入](#beans-setter-injection)  
构造注入，容器调用构造函数并传参数，每个参数都是依赖。调用静态工厂方法并传参数方式构造bean和构造注入差不多，这里是指构造注入处理参数和静态工厂方法处理参数像类似。下例中展示了一个只能使用构造注入的类。注意，此类无任何特别之处，并未依赖容器指定的接口、基类、注解，就是一个`POJO`
```java
public class SimpleMovieLister {

    // the SimpleMovieLister 依赖 a MovieFinder
    private MovieFinder movieFinder;

    //Spring容器能注入MovieFinder的构造函数
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 实际如何使用MovieFinder的业务逻辑省略了

}
```

<h5 id='beans-factory-ctor-arguments-resolution'>构造函数参数解决方案</h5>
构造参数解决方案，会匹配所使用的参数类型。如果在bean的定义中，构造参数不存在歧义，那么，在bean定义中定义的构造参数的次序，在bean实例化时，就是提供给适合的构造参数的次序。看这个类：
```java
package x.y;

public class Foo {

    public Foo(Bar bar, Baz baz) {
        // ...
    }

}
```

不存在歧义，假设`Bar`和`Baz`类没有集成关系，那么下面的配置是合法的，而且，不需要在`<constructor-arg/>`元素里指定构造参数的明确的`indexes`索引或者类型。
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
    </bean>

    <bean id="bar" class="x.y.Bar"/>

    <bean id="baz" class="x.y.Baz"/>
</beans>
```

若需要引用另一个bean，类型已知，构造函数就可以匹配参数类型(像上面的示例)。使用简单类型时， 想`<value>true</true>`,Srping不能决定value类型情况，Spring就不能自己匹配类型。例如： 
```java
package examples;

public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }

}
```
上面的场景中，如果使用`type`属性明确指定构造参数的类型,容器就可以使用类型匹配。比如：
```xml
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg type="int" value="7500000"/>
	    <constructor-arg type="java.lang.String" value="42"/>
	</bean>
```

使用`index`属性明确指定构造参数的次序。比如
```xml
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg index="0" value="7500000"/>
	    <constructor-arg index="1" value="42"/>
	</bean>
```
当构造函数有2个相同类型的参数,指定次序可以解决此种情况。注意`index`是从0开始
```xml
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg name="years" value="7500000"/>
	    <constructor-arg name="ultimateAnswer" value="42"/>
	</bean>
```
记住，若要使Spring能从构造函数查找参数名字,代码在编译时必须开启调试模式。若你没有开启调试模式（或者不想），可以使用`@ConstructorProperties` JDK 注解明确指定构造参数的`name`。样例程序：
```java	
package examples;

public class ExampleBean {

    // Fields omitted

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }

}
```

<h5 id='beans-setter-injection'>setter注入</h5>
Setter注入是容器调用bean上的setter方法,bean是使用无参构造函数返回的实例，或者无参静态工厂方法返回的实例。
下面样例中展示了只能使用Setter注入的类。这个类是传统java类，就是个POJO，不依赖容器指定的接口、基类、注解。
```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```


`ApplicationContext`对它所管理的bean支持构造注入和setter注入。也支持先构造注入再setter注入。定义依赖，会转换成某种形式的<code class="scode">BeanDefinition</code>类，<code class="scode">BeanDefinition</code>类与<code class="scode">PropertyEditor</code>实例配合，即可将属性从一种格式转换成其他格式。然而，大多数程序员不会直接使用这些类（也就是编程式），更多的是使用XML、注解(也就是<code class="scode">@Component</code><code class="scode">@Controller</code>等等),或者<code class="scode">@Configuration</code>注解的类中的方法上使用 <code class="scode">@Bean</code>。这些配置数据，都会在容器内部转换成`BeanDefinition`，用于加载整个Spring Ioc 容器。

**构造注入对比setter注入**

何时使用构造注入，何时使用setter注入，经验法则是:强制依赖用构造，可选依赖用Setter。注意，在settter方法上使用<code class="scode">[@Required](#beans-required-annotation)</code>注解即可另属性强制依赖。

Spring 团队建议,构造注入的实例是不可变的，不为null的。此外，构造注入组件要将完全初始化后的实例返回给客户端代码。还有，大量参数的构造函数是非常烂的，它意味着该类有大量的职责，得重构。

setter注入主要用于可选依赖,类内部可以指定默认依赖。否则类内所有使用依赖的地方，都得进行非空校验。setter注入的有个好处就是，类可以重配置或者再注入。因此，使用`JMX MBeans`进行管理的场景中，就非常适合setter注入。

使用何种依赖注入方式，对于某些类，非常有意义。有时协同第三方类处理，没有源码，由你来决定使用何种方式。比如，第三方类未暴露任何setter方法，那么构造注入也许就是唯一的可行的注入方式了。


<h5 id="beans-dependency-resolution">依赖处理过程</h5>
容器解析bean依赖如下：
* `ApplicationContext`创建后用配置元数据中描述的所有bean进行初始化。配置元数据格式可以是XML、Java Code，或者注解。
* 每个bean的依赖，都会以下列形式表达:属性、构造参数，静态工厂方法的参数。当bean真正的创建时，这些依赖会被提供给bean。
* 每个属性或者构造函数或者以value值形式在bean处直接设置，或者引用容器中其他bean。
* 每一个属性或者构造参数都是一个值，该值将会从指定的格式转换为属性、构造参数的真正类型。Spring默认会将一个`String`类value转换成内建类型，比如`int`,`long`,`String`,`boolean`等等 

Spring容器在创建bean之前会验证bean的配置。在bean创建之前，bean的属性不会赋值。当容器创建之后，会创建被设置为预先初始化的`sington-scope`单例作用域bean，非单例作用域bean，只有在请求时才会创建。作用域，在5.5章有定义，["Bean 作用域"](#beans-factory-scopes)。一个bean的创建，可能会引起许多bean的创建。因为bean的依赖以及依赖的依赖得先创建好用于引用。不涉及首先创建的bean及其依赖类bean，会稍后创建。

**循环依赖**
如果你主要使用构造注入,可能会创建一个循环依赖，该依赖不能解析。  

举个栗子：类A需要类B的实例，使用了构造注入,类B需要一个类A的实例，也用了构造注入。若在配置文件中配置类A的bean和类B的bean互相注入，Spring IoC容器在运行时发现循环引用，抛出异常`BeanCurrentlyInCreationException`。  

一般使用Setter注入替代构造注入，这需要修改源码改配置，来解决循环依赖。避免使用构造注入或者只使用setter，都能避免循环依赖。 换句话说，虽然不推荐循环依赖，但是你可以使用setter注入来完成循环依赖。

和大多数场景（无循环引用）不一样的是，循环引用中的类A和类B中，得强制其中一个自己能完全初始化，然后注入给另一个（经典的先有鸡现有蛋的问题）。
**循环依赖end**

对于Spring，你经管放心，它非常智能。他能在容器加载期发现配置中的问题，比如：引用了一个不存在的bean、循环依赖。Spring在bean创建后，会尽可能迟的设置bean属性并处理依赖。这意味着，spring容器正确加载之后，当你请求一个对象而该对象的创建有问题或者是该对象的依赖有问题时,也能产生一个异常。举例来说，因为属性找不到，或者属性无效， 导致bean抛出异常。这可能会延迟发现配置问题，这就是为什么`ApplicationContext`默认会预先实例化单例bean。在这些bean被实际请求之前就创建，会消耗一些时间和内存，但是在`ApplicationContext`创建后你就能发现配置问题，而不是更迟。如果你愿意 ,也可以重写该行为，让单例bean延迟初始化。


如果没有循环依赖，当一个或者多个合作bean被注入到他们的依赖类时，每一个合作bean将会比依赖类更早的实例化。也就是说，如果bean A依赖bean B，Spring Ioc容器在调用A的setter方法之前，会先实例化B。换句话说，bean先实例化(非单例)，然后设置依赖，然后调用相关声明周期方法（比如配置的init方法，或者是初始化回调函数）。

<h5 id='beans-some-examples'>注入依赖样例</h5>
The following example uses XML-based configuration metadata for setter-based DI. A small part of a Spring XML configuration file specifies some bean definitions:
下面例子中使用了XML配置元数据，setter注入方式。XML 配置文件中的片段定义了bean:
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- 使用内嵌的ref元素完成setter注入 -->
    <property name="beanOne">
        <ref bean="anotherExampleBean"/>
    </property>

    <!-- 使用ref属性完成setter注入 -->
    <property name="beanTwo" ref="yetAnotherBean"/>
    <property name="integerProperty" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
看java代码
```java
public class ExampleBean {

    private AnotherBean beanOne;
    private YetAnotherBean beanTwo;
    private int i;

    public void setBeanOne(AnotherBean beanOne) {
        this.beanOne = beanOne;
    }

    public void setBeanTwo(YetAnotherBean beanTwo) {
        this.beanTwo = beanTwo;
    }

    public void setIntegerProperty(int i) {
        this.i = i;
    }
}
```
In the preceding example, setters are declared to match against the properties specified in the XML file. The following example uses constructor-based DI:
上例中，setter方法名要和XML文件中的`property`元素的`name`属性相匹配。下面演示使用构造注入 ：
```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <!-- constructor injection using the nested ref element -->
    <constructor-arg>
        <ref bean="anotherExampleBean"/>
    </constructor-arg>

    <!-- constructor injection using the neater ref attribute -->
    <constructor-arg ref="yetAnotherBean"/>

    <constructor-arg type="int" value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
看java代码
```java
public class ExampleBean {

    private AnotherBean beanOne;
    private YetAnotherBean beanTwo;
    private int i;

    public ExampleBean(
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {
        this.beanOne = anotherBean;
        this.beanTwo = yetAnotherBean;
        this.i = i;
    }
}
```

在bean定义中指定的构造函数参数，将会赋值给`ExampleBean`类的参数。

现在考虑下这个样例的变种，将使用构造器改为静态工厂方法返回对象实例：
```xml
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
    <constructor-arg ref="anotherExampleBean"/>
    <constructor-arg ref="yetAnotherBean"/>
    <constructor-arg value="1"/>
</bean>

<bean id="anotherExampleBean" class="examples.AnotherBean"/>
<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
```
看java代码
```java
public class ExampleBean {

    //私有构造函数
    private ExampleBean(...) {
        ...
    }

    // 静态工厂方法; the arguments to this method can be
    // considered the dependencies of the bean that is returned,
    // regardless of how those arguments are actually used.
    public static ExampleBean createInstance (
        AnotherBean anotherBean, YetAnotherBean yetAnotherBean, int i) {

        ExampleBean eb = new ExampleBean (...);
        // some other operations...
        return eb;
    }

}
```

静态工厂方法的参数，应该通过`constructor-arg`元素产生，就像是bean的构造函数一样.工厂方法返回的类的类型无需和工厂类类型相同，虽然本例中他们是相同的。实例工厂方法(非静态）和静态工厂方法本质相同（除了使用`facory-bean`属性替代`class`属性，其他都相同），因此细节就不讨论了。

<h4 id='beans-factory-properties-detailed'>依赖和配置详解</h4>
前面章节提到的，你可以定义的bean的属性和构造参数引用其他的Spring bean(合作者)，或者是使用value属性设置其值。Spring XML格式配置元数据至此`<property/>`和`<constructor-arg/>`子元素，用以实现构造注入和属性注入。

<h5 id="beans-value-element">直接赋值(原始类型、String等等)</h5>
`<property />`元素的`value`属性为对象域属性或者构造参数设置了一个可读的字串。Spring的会将其转换为实际的与属性或者参数的数据类型。
```xml	
<bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <!-- results in a setDriverClassName(String) call -->
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
    <property name="password" value="masterkaoli"/>
</bean>
```

下面的样例，是使用了XML配置中的[p命名空间](#beans-p-namespace)，他让XML更加简洁
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myDataSource" class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="root"
        p:password="masterkaoli"/>

</beans>
```

上面的XML更简洁；然而，错别字，要在运行期才能发现而不能再开发期发现，除非你使用IDE支持自动补全。这样的的IDE的助手真心推荐。

也可以这样配`java.unit.Properties`实例：
```xml
<bean id="mappings" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <!-- typed as a java.util.Properties -->
    <property name="properties">
        <value>
            jdbc.driver.className=com.mysql.jdbc.Driver
            jdbc.url=jdbc:mysql://localhost:3306/mydb
        </value>
    </property>
</bean>
```

Spring 容器通过JavaBean的`PropertyEditor`机制将`<value/>`元素内的值转换到`java.util.Properties`实例。这是非常棒的，Spring团队最喜欢的几处好用之处之一：用内嵌`<value/>`元素替代 值属性风格。


<h5 id='beans-idref-element'>元素<span class="scode">idref</span></h5>
`idref`元素用来将容器内其它bean的id传给`<constructor-arg/>` 或 `<property/>`元素，同时提供错误验证功能。
```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean" />
    </property>
</bean>
```
上面的bean定义在运行时等同于下面这一段定义：

```xml
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean" />
</bean>
```

第一种格式比第二种要跟可取 ，因为使用`idref`标签，在开发期将允许容器校验引用bean真是存在。在第二个中，对于client bean是属性 `targetName`的值则没有校验执行 .`client` bean真正的实例化时，错别字才会被发现（可能会导致致命错）。如果`client` bean是一个[原型bean](#beans-factory-scopes)，这个错字导致的异常也许会等到部署后才能被发现。

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 在4.0 beans xsd ，`idref`上的`local`属性不在支持。因此它能提供正规bean引用 。当你升级到4.0的语法时，记得清除已经存在于`idref`元素上的`local`属性。


一个老生常谈的问题(至少是2.0以前了)，`<idref/>`带来的好处，在使用`ProxyFactorybean`bean定义[AOP拦截器](#aop-pfb-1)时，当指定拦截器名字是使用`<idref/>`元素将，容器会校验拦截目标是否存在。

<h5 id='#beans-ref-element'>引用其他bean(协作类)</h5>
`ref`元素是`<constructor-arg/>`元素和`<property/>`元素内决定性元素。用它设置bean的属性以引用另一个容器管理的bean。引用的bean就是要设置属性的bean的依赖，在设置属性值之前它就要被初始化。(如果协作类是单例bean，它会在容器初始化时首先完成初始化)。差不多所有的bean都会引用其他对象。指定`id/name`的对象的作用域和依赖校验通过`bean`,`local` ,`parent`属性来配置。
指定引用bean通常使用`<ref/>`标签，它允许引用本容器或者父容器中任意的bean，无需配置在同一个xml文件中 。`<ref/>`标签中`bean`的属性值，使用的被引用bean的`id`或者`name`。
```xml
<ref bean="someBean"/>
```
通过指定目标bean的`parent`属性来引用当前容器的父容器中的bean。`parent`属性的值可以和引用bean的`id`或者`name`（引用bean的name之一）相同，引用的bean必须存在于当前容器的父容器中。若容器存在继承的情况，并且需要封装现有父容器中的某个bean到一个代理中，就可以用此种引用机制，一个与`parent` bean重名的bean。
```xml
<!-- 父容器中 -->
<bean id="accountService" class="com.foo.SimpleAccountService">
    <!-- 依赖 -->
</bean>
```
子容器中
```xml
<bean id="accountService" <!-- 和parent bean重名 -->
    class="org.springframework.aop.framework.ProxyFactoryBean">
    <property name="target">
        <ref parent="accountService"/> <!--注意如何引用 parent bean -->
    </property>
    <!-- 其他配置和依赖 -->
</bean>
```
![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 在4.0 beans xsd ，`ref `上的`local`属性不在支持。因次它不再支持正规bean的引用 。当你升级到到4.0时，记得清除已经存在于`ref`元素上的`local`属性。

<h5 id='beans-inner-beans'>内部bean</h5>
在`<property/>`元素或者`constructor-arg/>`元素内定义`<bean/>`元素，就是所谓的内部类。
```xml
<bean id="outer" class="...">
    <!-- 不是引用而是定义一个bean -->
    <property name="target">
        <bean class="com.example.Person"> <!-- 这就是内部类 -->
            <property name="name" value="Fiona Apple"/>
            <property name="age" value="25"/>
        </bean>
    </property>
</bean>
```

内部bean的定义无需`id`或`name`；容器会忽略这些属性。也会忽略`scope`标记。内部通常是匿名的,伴随着外部类（的创建）而创建 。不能引用内部bean(ref属性不能指向内部bean)，除非使用闭合`bean`标签。

*译者注，内部bean更直观*
fuck goods，上干活
```java
public class Customer {
	private Person person;
 
	public Customer(Person person) {
		this.person = person;
	}
 
	public void setPerson(Person person) {
		this.person = person;
	}
 
	@Override
	public String toString() {
		return "Customer [person=" + person + "]";
	}
}
```
再来一段
```java
public class Person {
	private String name;
	private String address;
	private int age;
 
	//getter and setter methods
 
	@Override
	public String toString() {
		return "Person [address=" + address + ", 
                               age=" + age + ", name=" + name + "]";
	}	
}
```

通常情况下，使用在`CustomerBean`bean内设置`ref`属性值为`Person`bean的标示符，即完成注入。
```xml	
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
 
	<bean id="CustomerBean" class="com.example.common.Customer">
		<property name="person" ref="PersonBean" />
	</bean>
 
	<bean id="PersonBean" class="com.example.common.Person">
		<property name="name" value="MrChen" />
		<property name="address" value="address1" />
		<property name="age" value="28" />
	</bean>
 
</beans>
```
In general, it’s fine to reference like this, but since the ‘MrChen’ person bean is only used for Customer bean only, it’s better to declare this ‘MrChen’ person as an inner bean as following :
一般情况下，这样的引用很好用。但是如果'MrChen'这个person bean只用于`Customer`。最好是使用内部bean来声明`Person`，看起来更加直观，更具有可读性.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">
 
	<bean id="CustomerBean" class="com.mkyong.common.Customer">
		<property name="person">
			<bean class="com.mkyong.common.Person">
				<property name="name" value="mkyong" />
				<property name="address" value="address1" />
				<property name="age" value="28" />
			</bean>
		</property>
	</bean>
</beans>
```

This inner bean also supported in constructor injection as following :
 内部bean也支持构造注入
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="CustomerBean" class="com.mkyong.common.Customer">
		<constructor-arg>
			<bean class="com.mkyong.common.Person">
				<property name="name" value="mkyong" />
				<property name="address" value="address1" />
				<property name="age" value="28" />
			</bean>
		</constructor-arg>
	</bean>
</beans>
```

<h5 id='beans-collection-elements'>集合</h5>
`<list/>`,`<set/>`,`<map/>`,`<props/>`元素，用来设置`Java Collection`属性和参数，分别对应`List`,`Set`,`Map`,`Properties`
```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!--调用setAdminEmails(java.util.Properties) -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
    <!-- 调用setSomeList(java.util.List) -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- 代用setSomeMap(java.util.Map) -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- 调用 setSomeSet(java.util.Set) -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
</bean>
```

map.key，map.value，或者set.value，以可以是以下元素

	bean | ref | idref | list | set | map | props | value | null

<h5 id="beans-collection-elements-merging">集合合并</h5>
集合合并
Spring容器也支持集合合并。应用开发者可以定义父集合`<list/>`,`<map/>`,`<set/>`或者`<propx/>`元素，该元素可以有子集合`<list/>`,`<map/>`,`<set/>`或者`<props/>`元素集成或者重写父集合中的值。也就是,子集合中的值是合并父子集合后的值，其中子集合中的值会覆盖父集合中的值。
*这一章节讨论父-子bean机制。不熟悉父子bean定义机制的，最好是先去补充下然后回来继续*
下例中展示了集合合并

```xml
<beans>
    <bean id="parent" abstract="true" class="example.ComplexObject">
        <property name="adminEmails">
            <props>
                <prop key="administrator">administrator@example.com</prop>
                <prop key="support">support@example.com</prop>
            </props>
        </property>
    </bean>
    <bean id="child" parent="parent">
        <property name="adminEmails">
            <!-- the merge is specified on the child collection definition -->
            <props merge="true">
                <prop key="sales">sales@example.com</prop>
                <prop key="support">support@example.co.uk</prop>
            </props>
        </property>
    </bean>
<beans>
```

注意，在bean `child`定义中，指定`property` `adminEmails`的`<props/>`元素中`merge=true`属性。当`child`bean被容器解析并且实例化时，实例有一个`adminEmails`的`Properties`集合，该集合包含了父子容器中`adminEmails`集合合并后的值。
```
administrator=administrator@example.com
sales=sales@example.com
support=support@example.co.uk
```

子`Properties`集合的将继承所有父集合中`<props/>`定义的值，并且重名属性值会覆盖父集合中的值.

这个合并行为，同样可以用在`<list/>`,`<map/>`,`<set/>`类型上。对于`<list/>`元素，spring合并行为与`List`类型的合并一样，也就是，spring合并行为维持集合有序；父集合中的元素索引位置比子集合元素索引位置靠前。对于`Map`,`Set`,`Properties`集合类型,不存在次序。因此，没有次序影响`Map`,`Set`,`Properties`，这涉及到容器内部使用的这些类型的所有实现类。
*译注：这里没有提到List会不会发生覆盖 ,既然没提到，那就是List没有覆盖行为。当然了，实践才是王道，动手实验才能验证推测，研读源码才能知道原理，下面上干货*

Java代码
```java
public class CollectionMerge {
	private List<String> list;

	public List<String> getList() {
		return list;
	}

	public void setList(List<String> list) {
		this.list = list;
	}
}
```

XML配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<bean id="parent" class="com.example.spring.bean.collection.CollectionMerge">
		<property name="list">
	        <list >
	            <value>1</value>
	            <value>2</value>
	        </list>
		</property>
	</bean>
	
	<bean id="child" parent="parent" >
		<property name="list">
	        <list merge="true">
	            <value>1</value>
	            <value>2</value>
	        </list>
		</property>
	</bean>
</beans>
```

测试代码
```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@ContextConfiguration
@RunWith(SpringJUnit4ClassRunner.class)
public class MapMergeTests {
	
	@Autowired
	@Qualifier("child")
	private CollectionMerge service;
	
	@Test
	public void testSimpleProperties() throws Exception {
		assertEquals(4, service.getList().size());
	}
}

```

经过动手实验，证明Spring合并对于List类型，并无覆盖。接下来，我们看看其源码实现  

**MutablePropertyValues.mergeIfRequired()方法**
```java
private PropertyValue mergeIfRequired(PropertyValue newPv, PropertyValue currentPv) {
	Object value = newPv.getValue();
	//属性值是否可合并类
	if (value instanceof Mergeable) {
		Mergeable mergeable = (Mergeable) value;
		//属性值是否设置了merge=true
		if (mergeable.isMergeEnabled()) {
			//合并当前属性
			Object merged = mergeable.merge(currentPv.getValue());
			return new PropertyValue(newPv.getName(), merged);
		}
	}
	return newPv;
}
```
上面代码中`Mergeable`接口共有5个实现类`ManagedList`,`ManagedArray`,`ManagedMap`,`ManagedSet`,`ManagedProperties`

**ManagedList.merge(Object parent)方法**
```java
public List<E> merge(Object parent) {
		//防御性抛异常
		if (!this.mergeEnabled) {
			throw new IllegalStateException("Not allowed to merge when the 'mergeEnabled' property is set to 'false'");
		}
		if (parent == null) {
			return this;
		}
		//不能合并非List类型集合
		if (!(parent instanceof List)) {
			throw new IllegalArgumentException("Cannot merge with object of type [" + parent.getClass() + "]");
		}
		List<E> merged = new ManagedList<E>();
		//注意顺序，先增加父bean中的value值，所以文档中说父集合元素索引位置靠前
		merged.addAll((List) parent);
		merged.addAll(this);
		return merged;
	}
```

*译注:我勒个去，为了找这段代码，洒家差点累吐血。由此可见，译者是非常用心的用生命去翻译文档。*

**合并限制**
不能合并不同类型的集合，比如合并`Map`和`List`(*译注：上面的源码中有这样的代码，不知聪明的小读者是否注意到了*)。如果你非得这么干，那么就会抛出个异常。`merge`属性必须指定给父-子继承结构bean中的子bean，如果指定给了父集合则无效，不会产生预期的合并结果。

**强类型集合**
Java5中出现了范型，所以可以给集合使用强类型限制。比如说，声明一个只含有`String`类型的`Collection`。若使用Spring 注入一个强类型`Collection`给一个bean，那么就可以利用Spring的类型转换特性 ，该特性能将给定的值转换成合适的类型值，然后赋值给你的强类型`Collection`。

```java
public class Foo {

    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
```
看，飞碟
```xml
<beans>
    <bean id="foo" class="x.y.Foo">
        <property name="accounts">
            <map>
                <entry key="one" value="9.99"/>
                <entry key="two" value="2.75"/>
                <entry key="six" value="3.99"/>
            </map>
        </property>
    </bean>
</beans>
```

在`foo`bean的`accounts`属性注入前，强类型集合`Map<String,Float>`的泛型信息通过反射获取。因此Spring的类型转换机制识别出元素的value类型将会转换为`Float`，`9.99,2.75,3.99`将会转换成`Float`类型。

<h5 id=`beans-null-element`>Null值和空字串</h5>
Spring treats empty arguments for properties and the like as empty Strings. The following XML-based configuration metadata snippet sets the email property to the empty String value ("").
Spring对于属性的空参数转换为空字串。下面的XML片段，设置值email属性为空格字串("")

```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```
上面的xml配置相当于这样的java代码

	exampleBean.setEmail("")

`<null/>`元素处理null值：
```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```
上面配置相当于

	exampleBean.setEmail(null)

<h5 id='beans-p-namespace'>XML简写p命名空间</h5>
p-命名空间能让你使用`bean`元素属性替代内嵌`property/>`元素，用来描述属性值或者协作类。

Spirng支持[命名空间](#xsd-config)扩展配置,命名空间基于XML Schema定义。本章讨论的`beans`配置格式定义在XML Schema文档中。然而，p命名空间并不是在XSD文件中，而是存在于Spring核心中。

下面XML片段解释了:1使用了标准XML，第2个使用p-命名空间 

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
</beans>
```

上例中解释了，在bean定义中使用p-namespace设置`email` 属性。它告诉Spring这里有一个`property`声明。前面提到过，p-namespace 并不存在schema定义，所以`p`可以修改为其他名字。
*译注,干活都是译者自己撰写用于验证，而非参考手册原版中的内容,之所以验证，是因为原版E文有看不懂的地方、或者翻译拿不准、或者就是闲来无事、或者就是为了凑篇幅，这些事儿得通过写代码验证搞定了*
up fuck goods上干货
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:ppp="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
        
    <bean name="p-namespace" class="com.example.spring.bean.p.PNamespaceBean"
        ppp:email="foo@email.com"/>
</beans>
```
注意p命名空间的用法`xmlns:ppp="http://www.springframework.org/schema/p"`和`ppp:email="foo@email.com"`

go on fuck goods继续干货

```java
public class PNamespaceBean {
	private String email;

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}
	
	
}
```

下面样例中，2个bean都引用了同一个bean
```xmxl
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!--传统-->
    <bean name="john-classic" class="com.example.Person">
        <property name="name" value="John Doe"/>
        <property name="spouse" ref="jane"/>
    </bean>

	<!--时髦-->
    <bean name="john-modern"
        class="com.example.Person"
        p:name="John Doe"
        p:spouse-ref="jane"/>

	<!---->
    <bean name="jane" class="com.example.Person">
        <property name="name" value="Jane Doe"/>
    </bean>
</beans>
```

样例中2个p-namespace设置属性值，出现了一种新的格式声明引用。第一个bean中，使用了` <property name="spouse" ref="jane"/>`应用bean jane.第二个bean中，使用了` p:spouse-ref="jane"`做了相同的好事儿,此时,`spouse`是属性名，然而`-ref`表示这不是直接量而是引用另一个bean。

>*译注* 好事儿，我小时候虽然做好事儿不留名，但是总能被发现，令我非常苦恼。我的妈妈常常揪着我的耳朵问：这又是你干的好事儿吧。

<h5 id="beans-c-namespace">c-namespace命名空间</h5>

和[p-namespace](#beans-p-namespace)相似,c-namespace，是Spring3.1中新出的,允许行内配置构造参数，而不需使用内嵌的`constructor-arg`元素

用`c`:namespace重构[构造注入](#beans-constructor-injection)
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="bar" class="x.y.Bar"/>
    <bean id="baz" class="x.y.Baz"/>

    <!-- traditional declaration -->
    <bean id="foo" class="x.y.Foo">
        <constructor-arg ref="bar"/>
        <constructor-arg ref="baz"/>
        <constructor-arg value="foo@bar.com"/>
    </bean>

    <!-- c-namespace declaration -->
    <bean id="foo" class="x.y.Foo" c:bar-ref="bar" c:baz-ref="baz" c:email="foo@bar.com"/>

</beans>
```

`c:`namespace和`p:`使用了相同机制(`ref`后缀表示引用)，通过names设置构造参数。因为它未定义在XSD schema中（但是存在于Spring内核中）,所以需要先声明。

有一些情况比较特殊，不能识别或者看到构造参数(比如无源码且编译时无调试信息)，此时可以求助于参数索引:

```xml
<!-- c-namespace index declaration -->
<bean id="foo" class="x.y.Foo" c:_0-ref="bar" c:_1-ref="baz"/>
```
![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 由于XML语法，索引标记需要`_`开头作为XML属性名称,而不能使用数字开头(尽管某些ID支持)

在实际中，构造注入(name匹配/类型匹配/索引匹配)是非常高效的，一般情况下,推荐使用 name匹配方式配置。


<h5 id='beans-compound-property-names'>复合属性</h5>
在设置bean属性时，可以使用复合或者内嵌属性,组件路径可以有多长写多长，除了最后一个属性，其他属性都不能为`null`。看下面的bean定义
```xml
<bean id="foo" class="foo.Bar">
    <property name="fred.bob.sammy" value="123" />
</bean>
```
bean `foo`有属性`fred`,`fred`有属性`bob`,`bob`有属性`sammy`,最后的`sammy`属性赋值`"123"`。在bean`foo`构造后，`fred`属性和`bob`属性都不能为`null`否则抛异常`NullPointerException`

<h4 id='beans-factory-dependson'>使用depends-on</h4>

若bean是另个bean的依赖，通常是指该bean是另个bean的属性。在XML中通过`<ref/>`[元素](#beans-ref-element)配置实现。然而，bean之间并不全是直接依赖。举个栗子,类中有个静态初始化需要出发,像注册数据库驱动这样的。`depends-on`属性能强制这些先决条件首先完成执行初始化，然后再去使用它（比如用于注入）。
下面的样例中，展示了使用`depends-on`来表达bean之间的依赖关系：

```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager"/>
<bean id="manager" class="ManagerBean" />
```

也可以依赖多个bean，为`depends-on`属性值提供一个bean name列表，用逗号，空白，分号分隔。

```xml
<bean id="beanOne" class="ExampleBean" depends-on="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>

<bean id="manager" class="ManagerBean" />
<bean id="accountDao" class="x.y.jdbc.JdbcAccountDao" />
```

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 在[单例](#beans-factory-scopes-singleton)bean中，`depends-on`属性既可以设定依赖的初始化时机，也可以相应的设定依赖的销毁时机。在bean被销毁之前,bean使用`depdnds-on`属性定义的依赖bean会首先被销毁。因此`depends-on`也能控制销毁顺序。

<h4 id='beans-factory-lazy-init'>延迟初始化</h4>
`ApplicationContext`的各种实现默认的初始化处理过程，都是尽早的创建、配置所有的单例bean。通常，这种预先实例化是非常好的，因为在配置的错误或者环境问题立刻就能暴露出来，而不是数小时甚至数天后才发现。若不需要此行为，可以通过设置`lazy-initialized`延迟加载来阻止预先初始化。`lazy-initialized`bean告诉Ioc容器，只有在第一次请求的时候采取初始化，而不是在启动容器时初始化。


在XML中，属性`lazy-init`控制`<bean/>`元素的初始化。
```XML
<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
<bean name="not.lazy" class="com.foo.AnotherBean"/>
```
`ApplicationContext`解析上面的配置，在启动时，不会预先初始化这个标记为lazy的bean，为标记lazy的bean则会立刻初始化。

如果一个非延迟的单例bean依赖了lazy延迟bean，`ApplicationContext`会在启动时就创建lazy延迟bean,因为它必须满足单例bean依赖。延迟bean注入给单例bean，就意味着，它不会延迟加载的。

通过设置`<beans/>`元素的`default-lazy-init`属性，可以设置容器级别的延迟加载。看样例：
```xml
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```
<h4 id='beans-factory-autowire'>自动装配</h4>
Spring容器提供自动装配，用于组织bean之间的依赖。可以让Spring，通过检查`ApplicationContext`内容完成自动注入。自动装配有以下优点:

* 自动装配能明显减少属性和构造参数的配置。（在这方面，还有其他的机制也能达到此目的,比如bean 模板，[在后面的章节里有详细的讲解](#beans-child-bean-definitions)）
* 自动装配可扩展性非常好。比如，给类增加了依赖,无需修改配置,依赖类就能自动注入。因此，自动装配在开发期非常有用，在代码不稳定时，无需修改编码即可完成切换。

在XML中，设置`<bean/>`元素的`autowire`属性来设定bean的自动装配模式。自动装配有5种模式。可以选择任意一种，来设置bean的装配模式。（*译注，这不是废话么，有5中模式，每种都能随便用，假设有一种不能用，那就是4种模式了么*）

**Table 5.2. 自动装配模式**

模式 | 解释
----- | -----
no | (默认的)非自动装配。必须使用`ref`元素定义bean引用。对于大规模系统，推荐使用，明确的指定依赖易于控制，清楚明了。它在某种程度上说明了系统的结构。
byName | 通过属性名称property name自动装配。Spring会查找与需要自动装配的属性同名bean。举个栗子，若在bean定义中设置了by name方式的自动装配，该bean有属性`master`(当然了，还得有个setMaster(..)写属性方法),Spring会查找一个名叫`master`的bean，并将它注入给`master`熟悉。
byType | 若在容器中存在一个bean，且bean类型与设置自动装配bean的属性相同，那么将bean注入给属性。若与属性同类型的bean多于1个，则会抛出我们期待已久的致命异常，也就意味着这个bean也许不适合自动注入。若不存在匹配的bean，啥都不会发生;属性也不会设置，然后就没有然后了。
constructor | Analogous to byType, but applies to constructor arguments. If there is not exactly one bean of the constructor argument type in the container, a fatal error is raised.和byType模式类似，但是应用于构造参数。若在容器中不存在与构造参数类型相同的bean，那么接下来呢，抛异常呗，还能干啥?

*byType*或者*constructor*自动装配模式,可以装配arrays数组和范型集合。 这种个情况
下，容器内匹配类型的bean才会注入给依赖。若key类型是`String`,你也能自动装配强类型`Maps`。一个自动装配的Map，所有 匹配value类型的bean都会注入`Map.value`，此时，Map的key就是相应的bean的name。

可以结合自动装配和依赖检查，依赖检查会在装配完成后执行。

<h5 id='beans-autowired-exceptions'>自动装配的局限和缺点</h5>
自动装配最好是贯穿整个项目。若不是全部或大部分使用自动装配，而仅仅自动装配一两个bean定义，可能会把开发者搞晕。(*译注，最可能的原因是，开发者对自动装配机制不熟悉，或者想不到项目中居然还使用了自动装配模式，当发生问题时，擦的，找都没地方找去，调试信息里只能看到经过横切织入事务代理的proxy*)

总结局限和缺点:
* 属性中和构造参数明确的依赖设置会覆盖自动装配。不能自动装配所谓的简单属性，比如原始类型，`Strings`和`Classes`(简单属性数组)。这是源于Spring的设计。
* 和明确装配相比，自动装配是不确切的。正如上面的列表中提到的，Spring谨慎避免匹配模糊，若真的匹配不正确，则导致错误发生，Spring 管理的对象之间的关系记录也变的不明确了。
* 对于根据Spring容器生成文档的工具，装配信息将变的无用。
* 容器内多个bean定义可能会匹配设置为自动装配的`setter`方法或者构造参数的类型。对于arrays，collections,或者maps,这不是个问题。然而对于期望单一值的依赖，这种歧义将不能随意的解决。如果发现多个类型匹配，将会抛出异常 .

在后面的场景中，给你几条建议:
* 放弃自动装配，使用明确装配
* 避免通过在bean定义中设置`autowire-candidate`属性为false的方式来设置自动装配，下一章节会讲
* 通过设置`<bean/>`袁术的`primary`属性为`true`来指定单个bean定义作为主候选bean。
* 使用基于注解的配置实现更细粒度的控制，参看[Section 5.9, “Annotation-based container configuration”](#beans-annotation-config).


<h5 id='beans-factory-autowire-candidate'>排除自动装配bean</h5>
在每个bean的设置中，你可以排除bean用于自动装配。XML配置中，设置`<bean/>`元素的`autowire-candidate`属性为`false`；容器将不使用该bean自动装配。（包括注解配置,像`@Autowired`）
* 使用对bean名字进行模式匹配来对自动装配进行限制。其做法是在<beans/>元素的'default-autowire-candidates'属性中进行设置。比如，将自动装配限制在名字以'Repository'结尾的bean，那么可以设置为"*Repository“。对于多个匹配模式则可以使用逗号进行分隔。注意，如果在bean定义中的'autowire-candidate'属性显式的设置为'true' 或 'false'，那么该容器在自动装配的时候优先采用该属性的设置，而模式匹配将不起作用。*译注这一段翻译是从网上copy过来的，我勒个擦，得赶紧睡觉去了*

这些设置非常有用。但是这些被排除出自动注入的bean是不会自动注入到其他bean，但是它本身是可以被自动注入的。


<h4 id='beans-factory-method-injection'>方法注入</h4>
一般情况，容器中的大部分的bean都是[单例的](#beans-factory-scopes-singleton)。当单例bean依赖另一个单例bean，或者一个非单例bean依赖另个非单例bean是，通常是将另一个bean定义成其他bean的属性。当bean的生命周期不同时，那么问题来了。假设单例bean A依赖非单例bean(prototype) B，也许会在每个方法里都需要B。容器之创建了一个单例bean A，因此只有一次将B注入的机会。A调用B，需要很多B的实例 ,但是容器不会这么干。

解决办法是放弃一些IoC控制反转。令A实现接口`ApplicationContextAware`，此时A能[够感知容器](#beans-factory-aware)，即获取`ApplicationContext `，每次当A调用B时，调用容器的[getBean("B")方法用以创建B](#beans-factory-client)的实例。看样例:
```java
// a class that uses a stateful Command-style class to perform some processing
package fiona.apple;

// Spring-API imports
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```
并不推荐上面的做法，因为业务代码耦合了Spring 框架。方法注入,是SpringIoc容器的高级特性，能够简洁的满足此场景。

想要了解更多的方法注入，参看此[博客](https://spring.io/blog/2004/08/06/method-injection/)



<h5 id='#beans-factory-lookup-method-injection'>查找式方法注入</h5>
查找式是指，容器为了覆盖它所管理的bean的方法，在容器范围内查找一个bean作为返回结果。通常是查找一个原型(prototype)bean，就像是上面章节中提到过的场景。Srping框架，使用`CGLIB`类库生成动态子类的字节码技术，覆盖方法。

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 为了能让动态子类能运行，其父类不能是`final`类,被覆盖的方法也不能是`final`。还有，你得自己测试父类是否含有`abstract`方法，如果有，需要你提供默认实现。最后，被方法注入的对象不能序列化。Spring 3.2以后，不需要`CGLIB`的类路径了，因为`CGLIB`被打包入了org.springframework 包，和Spring-core 这个jar包在一起了。既是为了方便也是为了避免`CGLIB`包与应用中用到的`CGLIB`包冲突。
 
来看看前面提到的`CommandManager`类的代码片段，Spring容器会动态的覆盖`createCommand()`方法的实现。这样`CommandManager`类就不会依赖任何Spring API了。下面是修改过后的
```java
package fiona.apple;

// 不再有 Spring imports!

public abstract class CommandManager {

    public Object process(Object commandState) {
        // grab a new instance of the appropriate Command interface
		//
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    // okay... but where is the implementation of this method?
	//okay....但是方法实现在哪里?
    protected abstract Command createCommand();
}
```

在含有被注入方法的类中（像`CmmandManager`类），被注入方法需要使用以下签名
```
<public|protected> [abstract] <return-type> theMethodName(no-arguments);
```
动态生成子类会实现抽象方法。若该方法不是抽象的，动态生成自来则会重写在源类中的方法。配置如下：
```xml
<!-- a stateful bean deployed as a prototype (non-singleton) -->
<bean id="command" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>

<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="createCommand" bean="command"/>
</bean>
```

在`commandManager`类调用`createCommand`方法时，动态代理类将会被识别为`commandManager`返回一个`command` bean的实例。将`command`bean设置成`prototype`,一定要小心处理。若被设置成了`singleton`，每次调用将返回同一个`command`bean。

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> 感兴趣的小读者也找找`ServiceLocatorFactoryBean`类(在`org.springframework.beans.factor.config`包)来玩玩。使用`ServiceLocatorFactoryBean`类的处理手法和另一个类`ObjectFactoryCreatingFactoryBean`类似，但是`ServiceLocatorFactoryBean`类与虚拟指定你自己的lookup接口(查找接口)，这与Spring指定lookup接口略有不同。详情参看这些类的javadocs

*译注，今天加班晚了点，到家时23:30了，可是今天还没翻。进了家门，打开电脑，翻一小节再说，要不估计睡不着觉了。对于我自己的毅力，我还是有相当的认识的，比如：无论咳的多么严重，都能坚持抽烟，由此可见一斑。以上是玩笑。我的意志力并不强，但是意志薄弱也有意志薄弱的积极的正面的意义，比如我养成了每天翻点东西的习惯，哪怕就是再困、再饿、再累，也得翻译一下，因为要是不翻译的话，我就得跟自己的习惯作斗争了，准确的说是和自己斗争，而我却又没有与自己斗争的念想，我根本打不过我自己，就这样，我又翻了一小节*

<h5 id="beans-factory-arbitrary-method-replacement">任意方法替换</h5>
还有一种方法注入方式，不如`lookup method`注入方式好用，可以用其他bean方法实现替换受管理的bean的任意方法。你可以跳过本节，当真的需要时再回来也是可以的。

在xml配置中,设置`replaced-method`元素，就可用其他实现来替换已经部署的bean中存在的方法实现。考虑下面的类，有一个我们要重写的方法`computeValue`
```java
public class MyValueCalculator {

    public String computeValue(String input) {
        // balbalba
    }

    // 其他方法...

}
```

有个类实现了`org.springframework.beans.factory.support.MethodReplacer`接口，类中有新的方法定义
```java
/**
 * 意味着用来重写MyValueCalculator类中computeValue(String)方法的实现
 */
public class ReplacementComputeValue implements MethodReplacer {

    public Object reimplement(Object o, Method m, Object[] args) throws Throwable {
        // get the input value, work with it, and return a computed result
        String input = (String) args[0];
        ...
        return ...;
    }
}
```

bean定义，用来部署的源类，要设置方法重写，大概这么搞：
```xml
<bean id="myValueCalculator" class="x.y.z.MyValueCalculator">
    <!-- arbitrary method replacement -->
    <replaced-method name="computeValue" replacer="replacementComputeValue">
        <arg-type>String</arg-type>
    </replaced-method>
</bean>

<bean id="replacementComputeValue" class="a.b.c.ReplacementComputeValue"/>
```

可以在`<replaced-method/>`元素内设置一个或多个`<arg-type/>`元素来指明被替换方法的参数类型。只有被覆盖的方法在类有重载，参数签名才是必要的。为了方便，`String`类型的参数只需要其完全限定类型名称的字串即可。比如，下面列出的均可匹配`java.lang.String`:
```
java.lang.String
String
Str
```
因为参数的数量基本就可以确定方法（重载的方法，基本上是参数数量有区别），此简写能大量减少打字,让你仅打几个字符就能匹配参数类型。
*译注，Spring是工业品质的框架，如此细微的人性化设计，值得学习*

<h3 id='beans-factory-scopes`>bean作用域</h5>
Spring bean定义时，实际上是创建类实例的配方。这个观点非常重要，因为她意味着，通过一个配方，即可创建很多类的对象。

对于依据bean定义产生的bean,不仅可以控制依赖、设置对象的值，还可以对象作用域。这个手法强大而灵活，因为在配置过程中就可以可以控制的bean的作用域，无需在代码层面去控制，用代码去控制简直就是煎熬。要部署的bean可有设置1个或多个作用域：开箱即用，Spring框架支持5中作用域，其中有三种只有用web-aware`ApplicationContext`才能使用。

下面了列出的作用域开箱即用，你也可以[自定义作用域](#beans-factory-scopes-custom)

**Table 5.3. Bean scopes**

**作用域**  | **描述**
---------  | --------
[单例singleton](#beans-factory-scopes-singleton) | 默认的。一个bean定义，在一个IoC容器内只会产生一个对象。
[prototype原型](#beans-factory-scopes-prototype) | 一个bean定义会产生多个对象实例
[request请求](#beans-factory-scopes-request) | 一个bean定义产生的bean生命周期为一个HTTP请求；也就是，每一个HTTP请求都会根据bean定义产生一个对象实例。该作用域只有在Spring web上下文环境中才有效。
[session会话](#beans-factory-scopes-session) | 产生的bean生命周期在HTTP 会话期间。该作用域只有在Spring web上下文环境中才有效
[gloabal session全局session](#beans-factory-scopes-global-session) | 声明周期为全局HTTP会话。通常使用portlet context时常用。该作用域只有在Spring web上下文环境中才有效。
[application应用](#beans-factory-scopes-application) | 生命周期与`ServletContext`一样。该作用域只有在Spring web上下文环境中才有效

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
> Spring3.0起 多了一个作用域-*thred*,但它默认是未注册的(不可用的意思?)。详情请参看文档去吧`SimpleThreadScope`。有关如何注册该作用域和注册自定义作用域，参看本章使用[自定义作用域](#beans-factory-scopes-custom-using)
 
<h4 id="#beans-factory-scopes-singleton">单例作用域</h4>
单例bean只会产生一个实例,对于所有的请求，Spring容器都只会返回一个实例。

换句话说,当定义了单例bean，Srping容器只会创建一个实例，这个实例存储在单例池中，单例池应该属于缓存，接下来所有对于该单例bean的请求和引用，都将返回缓存中的对象。

**Figure 5.2.**
![替换的文本可选的](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/singleton.png) 


Spring单例bean的概念，和四人帮GOF那本《设计模式》中定义的*单例模式*不同。GOF的单例是硬编码级的对象作用域，因此导致每一个类加载器内会产生单例类的一个实例。Spring的单例恰如其名，在容器范围内只会产生一个类实例。Spring中，bean默认的作用域都是单例作用域。使用xml 定义单例bean，像这样:
```xml
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- 和下面的写法相等，因为单例作用域是默认的，所以这么写有些画蛇添足，意思就是废话了 -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
``` 

<h4 id='beans-factory-scopes-prototype'>prototype原型作用域</h4>
设置bean作用域为`prototype`，就是非单例,对于每次请求都将返回一个该类的新实例。也就是说，原型bean注入另一个bean，或者是请求原型bean，都是通过在容器上调用`getBean()`方法产生的。一般来说 ，原型bean用于有状态bean，单例bean用于无状态bean。

下图示例了Srping原型作用域。一个数据访问对象(DAO)通常不会配置成原型作用域,因为通常DAO不会持有任何会话状态；因为作者偷懒，所以重用了上面单例示意图。
![替换的文本可选的](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/prototype.png)

接下来看看如何在XML中定义原型bean:
```xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```

和其他作用域相比，Srping并不管理原型bean的完整的生命周期：容器实例化，配置或者组装原型独享，注入给其他类，然后并未进一步记录那个原型bean。因此，尽管对象的初始化回调方法会调用，不受scope影响,但是对于原型bean,销毁回调不会被调用。客户端代码必须清理原型对象并且释放原型bean持有的资源。为了让Spring容器释放原型bean持有的资源，可以用自定义的bean`[post-processor](#beans-factory-extension-bpp)`,
它持有需要被清理bean的引用。

某种意义上，对于原型bean来说,Spring容器的角色就是替换了new 操作符。所有的生命周期管理，在经过实例化之后，都需要由客户端来处理。(Spring 容器中bean的生命周期详情，请参看本章[5.6.1生命周期回调](#beans-factory-lifecycle))


