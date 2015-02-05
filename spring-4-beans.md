<h1>作者简介</h1>  
翻译 石永明  
顾问 张丙天  

**石永明** 现任中科软科技股份信息系统事业群技术副总监，2008年加入中科软。擅长SOA、企业信息化架构，精通Java、Spring，在多线程、io、网络通信及支撑大型网站的领域有较多经验，对技术有浓厚的兴趣。现致力于无线、数据、业务平台、组件化方面取得突破

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

Spring configuration consists of at least one and typically more than one bean definition that the container must manage.Spring配置由Spring bean的定义组成，这些bean必须被容器管理，至少1个，通常会有多个。基于XML的配置元数据，大概这么配置，根节点`<beans>`中配置子节点`<bean>`。Java configuration使用是这样的，一个带有`@Configuration`类注解的类中，方法上使用`@Bean`方法注解。

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

If you are using Java-configuration, the @Bean annotation can be used to provide aliases see Section 5.12.3, “Using the @Bean annotation” for details.
如果你使用了`Java-configuration`，`@Bean`注解也提供了别名，详见[Section 5.12.3, “Using the @Bean annotation”](#beans-java-bean-annotation)










