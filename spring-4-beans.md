<style>
.sidebar {
line-height: 1.4;
padding: 0 20px;
background-color: #F8F8F8;
border: 1px solid #CCCCCC;
border-radius: 3px 3px 3px 3px;
}
.scode {
font-size: 16px;
font-family: Consolas,"Liberation Mono",Courier,monospace;
color: #6D180B;
background-color: #F2F2F2;
border: 1px solid #CCCCCC;
border-radius: 4px;
padding: 1px 3px 0;
text-shadow: none;
white-space: nowrap;
}
code {
font-size: 16px;
}
</style>

<h1>作者简介</h1>  
翻译 石永明  
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

Spring配置由Spring bean的定义组成，这些bean必须被容器管理，至少1个，通常会有多个。基于XML的配置元数据，大概这么配置，根节点`<beans>`中配置子节点`<bean>`。Java configuration使用是这样的，一个带有`@Configuration`类注解的类中，方法上使用`@Bean`方法注解。

bean的定义要与应用中实际的类相一致。可以定义service 层的对象、Dao对象、类似Struts的表现成的对象、像Hibernate SessionFactories这样的基础对象，JMS队列等等。通常不会去定义细粒度域对象，因为它们由DAO或者Service负责创建、加载。然而，通过集成AspectJ，可以配置非Srping容器创建的对象。参看[Using AspectJ to dependency-inject domain objects with Spring](#aop-atconfigurable)

下面的样例展示了基于XML配置元数据的基本格式:  

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

`id`属性是个字串，是bean的唯一标示符。`class`属性定义了bean的类型，要使用类的全限定类名（含有包路径）。`id`属性的值，可以作为合作bean的引用标示符。上面未展示如何引用其他对象；详情参看[Dependencies](#beans-dependencies)


<h4 id='beans-factory-instantiation'>容器实例化</h4>  
Spring IoC的实例化易如反掌。`ApplicationContext`构造函数支持定位路径，定位路径也可以是多个，它是标识实际资源的字串，容器使用该标识加载配置元数据，支持多种资源，比如：本地文件系统、CLASSPATH等等。  

	ApplicationContext context = 
		new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});  

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
在学习了Spring IoC容器之后，也许你想了解更多的Spring的资源，如前所述在第6章，资源使用URI语法定位输入流，Spring提供了方便的机制读取输入流。在第6.7章[“Application contexts and Resource paths”](#resources-app-ctx)，专门讲述5用 资源路径构造应用上下文，资源路径也是惯用手法。  
接下来的样例展示了配置service层对象:  

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

下面的样例展示了数据访问对象`dao.xml`配置:  

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

上述内容中service层由`PetStoreServiceImpl`类、2个dao对象`JpaAccountDao`和`JpaItemDao`(基于JPA ORM标准)。属性`name`元素引用了JavaBean的属性，*ref*元素引用了其他bean定义。这个引用表示实际对象之间的引用依赖。配置一个对象的依赖，详情请参看[Dependencies](#beans-dependencies)

<h5 id='#beans-factory-xml-import'>引入基于xml的元数据</h5>
多个配置文件共同定义bean非常有用。通常，每个XML配置文件在你的架构中代表一个逻辑层或者一个模块。

你可以使用应用上下文(applicationContext)的构造函数去加载所有xml中定义的bean。这个构造函数使用多个资源定位，就像前面中提到的。或者，也可以用一个或者多个资源引用，即使用`<import/>`标签加载其他文件定义的bean。举个栗子：
    
    <beans>
	    <import resource="services.xml"/>
	    <import resource="resources/messageSource.xml"/>
	    <import resource="/resources/themeSource.xml"/>
	    
	    <bean id="bean1" class="..."/>
	    <bean id="bean2" class="..."/>
    </beans>

上例中，从三个外部文件加载定义的bean:`services.xml`,`messageSource.xml`,`themeSource.xml` 。被引入的文件的路径对于引入配置文件来说都是相对路径，所以`service.xml`必须在引入配置文件的相同文件路径或者相同的类路径中。而`messageSource.xml`和`themeSource.xml`必须在引入配置文件所在的文件夹下的`resouce`文件夹下。正如你所看到的 `/`开头会被忽略掉，因为这些路径是相对路径，推荐不要使用`/`开头的格式。导入(imported)文件内容，包含根节点`<beans/>`，配置中XML bean定义 必须经过Spring语法校验通过。

![注意](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/note.png)  
>  
使用"../"表示父目录的相对路径是可以的，但是真心不推荐这样创建一个依赖应用外部文件的做法。尤其指出，使用*"classpath:"*资源类型的URLs(像这样："classpath:../services.xml")，也是不推荐的，因为运行时处理过程会选择"最近的"根路径然后引入他的父目录配置文件。Classpath配置的改变，会导致应用选择一个不同的、错误的目录。
你可以使用全路径限定资源定位取代相对路径，比如："file:C:/config/services.xml" 或者"classpath:/config/services.xml"。还有，你可以使用抽象路径来解耦应用和配置文件。使用一个逻辑定位更可取 ,比如：通过"${..}"占位符，使用JVM运行时计算出的路径。

<h4 id='beans-factory-client'>使用容器</h4>
`ApplicationContext`是一个高级工厂的接口，能维护各种bean以及他们之间依赖的注册。使用方法`T getBean(String name, Class<T> requiredType)`，就能从定义的bean中获取实例。
`ApplicationContext`能让你读取bean定义、访问他们，如下：  

    // create and configure beans
    ApplicationContext context =
    new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});
    
    // retrieve configured instance
    PetStoreService service = context.getBean("petStore", PetStoreService.class);
    
    // use configured instance
    List<String> userList = service.getUsernameList();  

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
**属性** :**详情**  
**class** :[Section 5.3.2, “Instantiating beans”](#beans-factory-class)  
**name** :[Section 5.3.1, “Naming beans”](#beans-beanname)  
**scope**:[Section 5.5, “Bean scopes”](#beans-factory-scopes)  
**constructor arguments**:[Section 5.4.1, “Dependency injection”](#beans-factory-collaborators)  
**properties**:[Section 5.4.1, “Dependency injection”](#beans-factory-collaborators)  
**autowiring mode**:[Section 5.4.5, “Autowiring collaborators”](#beans-factory-autowire)  
**lazy-initialization mode**:[Section 5.4.4, “Lazy-initialized beans”](#beans-factory-lazy-init)  
**initialization method**:[the section called “Initialization callbacks”](#beans-factory-lifecycle-initializingbean)  
**destruction method**:[the section called “Destruction callbacks”](#beans-factory-lifecycle-disposablebean)  

除了bean的信息以外，`BeanDefinition`也包含创建特殊bean的信息，`ApplicationContext`的实现也允许注册由用户创建而非IoC容器创建的对象。通过访问ApplicationContext’s BeanFactory的方法`getBeanFactory()`，该方法返回BeanFactory的实现`DefaultListableBeanFactory`。`DefaultListableBeanFactory`类支持这种注册，通过`registerSingleton(..)`和`registerBeanDefinition(..)`方法实现。然而，典型的应用只用元数据定义的bean就可以单独运行。


<h4 id='beans-beanname'>beans命名</h4>
bean有一个或者多个标示符。这些标示符必须是所在容器范围内必唯一的。通常情况一下，一个bean仅有一个标示符，如果有需求需要多个，多出来的将被当做别名。

在XML格式配置元数据中，使用 `id` 或者 `name` 属性来作为bean的标示符。`id`属性只能有1个。命名规范是字符数字混编（myBean,fooService,等等），但也支持特殊字符，可以包含。若想给bean起个别名，则可使用`name`属性来指定，可以是多个，用英文的逗号(`,`)分隔、分号(`;`)也行、空格也行。注意，在Spring3.1以前，`id`属性定义成了`xsd:ID`类型，该类型强制为字符*（译者心里说：估计字母+特殊字符，不支持数字的意思，有待验证，没工夫验证去了，翻译进度太慢了。再说了，现在都用4了，你再说3.0怎么着怎么着，那不跟孔乙己似的跟别人吹嘘茴香豆有四种写法）*。3.1版开始，它被定义为`xsd:string`类型。注意，bean `id`的唯一性约束依然被容器强制使用，尽管xml解析器不再支持了。*译者注：在spring3（含）以前，id是可以相同的，容器会替换相同id的bean，而在新版中，容器初始化过程中发现id相同抛出异常，停止实例化*

`id` 和`name`属性不是bean所必须的。若未明确指定`id`或者`name`属性，容器会给它生成一个唯一name属性。当然了，如果你想通过bean的`name`属性引用，使用`ref`元素方式，或者是类似于[Service Locator模式](#beans-servicelocator)方式检索bean(*译者想：应该是指调用ApplicationContext.getBean()方法获取bean，类似这种方式。Service Locator是一种设计模式，其实换个名字是不是更合适，DL（Dependency Lookup依赖查找）。虽然现在我也不明白，但是下面有专门的章节讲解，翻到时候再详细了解*)，就必须给bean指定	`name`了。之所以支持无name bean特性，是为了使内部类自动装配。

	Bean命名规范
	
	bean命名规范使用java命名规范中实例属性名(也称域，Field)规范。小写字母开头的驼峰式。像这样(不包括单引号)`accountManager`，`accountService`，`userDao`，
	`loginController`，等等

	规范的命名使配置易读易理解。若使用Spring AOP，通过名字增强(译注：大多数Spring AOP教材中的 通知)一坨bean时，规范的命名将带来极大的方便。
	
<h5 id='beans-beanname-alias'>bean定义之外设置别名</h5>
定义的bean内，可以给bean多个标识符，组合`id`属性值和任意数量的`name`属性值。这些标识符均可作为该bean的别名，对于有些场景中,别名机制非常有用，比如应用中组件对自身的引用。(*译注：一个类持有一个本类的实例作为属性，看起来应该是这样的，以下代码为推测，可以执行*)  
**Bean类**
	
	public class SomeBean {
		//注意看这个属性，就是本类
		private SomeBean someBean;
		
		public SomeBean(){}
		
		public void setSomeBean(SomeBean someBean) {
			this.someBean = someBean;
		}
	}
**配置元数据**  

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

**测试代码**  

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

在bean的定义处指定所有别名有时候并不合适，然而，在其他配置文件中给bean设置别名却更为恰当。此法通常应用在大型系统的场景中，配置文件分散在各个子系统中，每个子系统都有本系统的bean定义。XML格式配置元数据，提供`<alias/>`元素，可以搞定此用法。

	<alias name="fromName" alias="toName"/>

这种情况下，在同容器中有个叫`fromName`的bean，或者叫其他的`阿猫阿狗`之类的，再使用此别名定义之后，即可被当做`toName`来引用。

举个栗子，子系统A中的配置元数据也许引用了一个被命名为`subsystemA-dataSource`的bean。子系统B也许引用了一个`subsystemB-dataSource`。将这两个子系统整合到主应用中，而主应用使用了一个`myApp-dataSource`，为了使3个bean引用同一个对象，得在MyApp配置元数据中使用别名定义:

	<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
	<alias name="subsystemA-dataSource" alias="myApp-dataSource" />

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

    <bean id="exampleBean" class="examples.ExampleBean"/>
    
    <bean name="anotherExample" class="examples.ExampleBeanTwo"/>  

如何为构造函数指定参数？如何在对象实力话之后设置其属性？请参看[Injecting Dependencies](#beans-factory-collaborators)

<h5 id='beans-factory-class-static-factory-method'>使用静态工厂方法实例化</h4>
定义使用使用静态工厂方法创建的bean时，得指定工厂方法类的作为`class`属性值，并且还得指定工厂方法类中用于创建bean的方法名称，作为`factory-method`属性值。工厂方法可以有参数，调用该方法即可返回对象实例，就像通过构造函数创建对象实例一样。此种bean定义是为了兼容遗留系统中的静态工厂

下面的bean定义，是使用工厂方法创建bean的方式。定义中，无需指定返回对象的类型(class)，而是指定工厂方法类的`class`。下例中，`createInstance()`方法必须是一个`static`静态方法。

	<bean id="clientService"
	    class="examples.ClientService"
	    factory-method="createInstance"/>  
<br>  

    public class ClientService {
	    private static ClientService clientService = new ClientService();
	    private ClientService() {}
	    
	    public static ClientService createInstance() {
	    return clientService;
	    }
    }	

<h5 id='beans-factory-class-instance-factory-method'>使用实例工厂方法实例化</h5>
和[静态工厂方法](#beans-factory-class-static-factory-method)类似的还有实例工厂方法，使用实例工厂方法的方式实例化，是调用容器中已存在的bean的一个非静态方法来创建一个bean。用法是，1、`class`属性置空设置。 2、设置`factory-bean`属性，其值为当前容器(或者父容器)中bean的名字，该bean包含可供调用的创建对象的实例方法。3、设置`factory-method`属性，其值为工厂方法名。

    <!-- 工厂类, 包含一个方法createInstance() -->
    <bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
    </bean>
    
    <!-- the bean to be created via the factory bean -->
    <bean id="clientService"
	    factory-bean="serviceLocator"
	    factory-method="createClientServiceInstance"/>

工厂类如下

    public class DefaultServiceLocator {
    
	    private static ClientService clientService = new ClientServiceImpl();
	    private DefaultServiceLocator() {}
	    
	    public ClientService createClientServiceInstance() {
	    return clientService;
	    }
    }

工厂类可以有多个工厂方法:

	<bean id="serviceLocator" class="examples.DefaultServiceLocator">
	    <!-- inject any dependencies required by this locator bean -->
	</bean>
	
	<bean id="clientService"
	    factory-bean="serviceLocator"
	    factory-method="createClientServiceInstance"/>
	
	<bean id="accountService"
	    factory-bean="serviceLocator"
	    factory-method="createAccountServiceInstance"/>

工厂类如下:

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

	public class SimpleMovieLister {
	
	    // the SimpleMovieLister 依赖 a MovieFinder
	    private MovieFinder movieFinder;
	
	    //Spring容器能注入MovieFinder的构造函数
	    public SimpleMovieLister(MovieFinder movieFinder) {
	        this.movieFinder = movieFinder;
	    }
	
	    // 实际如何使用MovieFinder的业务逻辑省略了
	
	}

<h5 id='beans-factory-ctor-arguments-resolution'>构造函数参数解决方案</h5>
构造参数解决方案，会匹配所使用的参数类型。如果在bean的定义中，构造参数不存在歧义，那么，在bean定义中定义的构造参数的次序，在bean实例化时，就是提供给适合的构造参数的次序。看这个类：
	package x.y;
	
	public class Foo {
	
	    public Foo(Bar bar, Baz baz) {
	        // ...
	    }
	
	}

不存在歧义，假设`Bar`和`Baz`类没有集成关系，那么下面的配置是合法的，而且，不需要在`<constructor-arg/>`元素里指定构造参数的明确的`indexes`索引或者类型。

	<beans>
	    <bean id="foo" class="x.y.Foo">
	        <constructor-arg ref="bar"/>
	        <constructor-arg ref="baz"/>
	    </bean>
	
	    <bean id="bar" class="x.y.Bar"/>
	
	    <bean id="baz" class="x.y.Baz"/>
	</beans>

若需要引用另一个bean，类型已知，构造函数就可以匹配参数类型(像上面的示例)。使用简单类型时， 想`<value>true</true>`,Srping不能决定value类型情况，Spring就不能自己匹配类型。例如： 

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

上面的场景中，如果使用`type`属性明确指定构造参数的类型,容器就可以使用类型匹配。比如：

	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg type="int" value="7500000"/>
	    <constructor-arg type="java.lang.String" value="42"/>
	</bean>

使用`index`属性明确指定构造参数的次序。比如

	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg index="0" value="7500000"/>
	    <constructor-arg index="1" value="42"/>
	</bean>

当构造函数有2个相同类型的参数,指定次序可以解决此种情况。注意`index`是从0开始

	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg name="years" value="7500000"/>
	    <constructor-arg name="ultimateAnswer" value="42"/>
	</bean>

记住，若要使Spring能从构造函数查找参数名字,代码在编译时必须开启调试模式。若你没有开启调试模式（或者不想），可以使用`@ConstructorProperties` JDK 注解明确指定构造参数的`name`。样例程序：
	
	package examples;
	
	public class ExampleBean {
	
	    // Fields omitted
	
	    @ConstructorProperties({"years", "ultimateAnswer"})
	    public ExampleBean(int years, String ultimateAnswer) {
	        this.years = years;
	        this.ultimateAnswer = ultimateAnswer;
	    }
	
	}

<h5 id='beans-setter-injection'>setter注入</h5>
Setter注入是容器调用bean上的setter方法,bean是使用无参构造函数返回的实例，或者无参静态工厂方法返回的实例。
下面样例中展示了只能使用Setter注入的类。这个类是传统java类，就是个POJO，不依赖容器指定的接口、基类、注解。

	public class SimpleMovieLister {
	
	    // the SimpleMovieLister has a dependency on the MovieFinder
	    private MovieFinder movieFinder;
	
	    // a setter method so that the Spring container can inject a MovieFinder
	    public void setMovieFinder(MovieFinder movieFinder) {
	        this.movieFinder = movieFinder;
	    }
	
	    // business logic that actually uses the injected MovieFinder is omitted...
	
	}


`ApplicationContext`对它所管理的bean支持构造注入和setter注入。也支持先构造注入再setter注入。定义依赖，会转换成某种形式的<code class="scode">BeanDefinition</code>类，<code class="scode">BeanDefinition</code>类与<code class="scode">PropertyEditor</code>实例配合，即可将属性从一种格式转换成其他格式。然而，大多数程序员不会直接使用这些类（也就是编程式），更多的是使用XML、注解(也就是<code class="scode">@Component</code><code class="scode">@Controller</code>等等),或者<code class="scode">@Configuration</code>注解的类中的方法上使用 <code class="scode">@Bean</code>。这些配置数据，都会在容器内部转换成`BeanDefinition`，用于加载整个Spring Ioc 容器。

**构造注入对比setter注入**

何时使用构造注入，何时使用setter注入，经验法则是:强制依赖用构造，可选依赖用Setter。注意，在settter方法上使用<code class="scode">[@Required](#beans-required-annotation)</code>注解即可另属性强制依赖。

Spring 团队建议,构造注入的实例是不可变的，不为null的。此外，构造注入组件要将完全初始化后的实例返回给客户端代码。还有，大量参数的构造函数是非常烂的，它意味着该类有大量的职责，得重构。

setter注入主要用于可选依赖,类内部可以指定默认依赖。否则类内所有使用依赖的地方，都得进行非空校验。setter注入的有个好处就是，类可以重配置或者再注入。因此，使用`JMX MBeans`进行管理的场景中，就非常适合setter注入。

使用何种依赖注入方式，对于某些类，非常有意义。有时协同第三方类处理，没有源码，由你来决定使用何种方式。比如，第三方类未暴露任何setter方法，那么构造注入也许就是唯一的可行的注入方式了。

