<h1>作者简介</h1>  
翻译 铁柱  <wwwshiym@gmail.com>  
顾问 张丙天  

**铁柱** 曾任中科软科技股份有限公司应用系统事业群部技术副总监、首席架构师，2008年加入中科软。擅长SOA、企业信息化架构，精通Java、Spring，在多线程、io、虚拟机调优、网络通信及支撑大型网站的领域有较多经验，对技术有浓厚的兴趣。现致力于无线、数据、业务平台、组件化方面取得突破。

**张丙天** 

<h2 id='mvc'>Web MVC framework框架</h2>
<h3 id='mvc-introduction'>Spring Web MVC框架简介</h3>
Spring MVC的核心是`DispatcherServlet`，该类作用非常多，分发请求处理，配置处理器映射，处理视图view，本地化，时间区域和主题，也支持文件上传。默认的处理器依赖于`@Controller`和`RequestMapping`注解，提供了大量的灵活的处理方法。spring3.0中就介绍过了，`@Controller`机制，可通过SpringＭＶＣ提供的`@PathVariable`注解和其他功能,创建`RESTful`web网站和应用。
```text
在spring MVC中一个关键的设计就是“开闭原则”，即对扩展开放对修改闭合原则。Spring 一些核心类的方法
是`final`方法。开发者不能重写这些方法，来增加自己的行为。这些都不是随意决定，而是意在符合“开闭原则”。


预知此原则详情，参看 Seth Ladd(这应该就是马丁大叔)等人的
*Expert Spring Web MVC and Web Flow*，尤其是要看“A Look At Desig” 章节，在第一版的117页

* [Bob Martin, The Open-Closed Principle (PDF)](http://www.objectmentor.com/resources/articles/ocp.pdf)

使用Spring MVC时不能对final 方法织入增强。比如，你不能对`AbstractController.setSynchronizeOnSession()`方法织入增强。关于AOP代理和之所以不能对final方法织入增强，请参看[Section 9.6.1, “Understanding AOP proxies”](#aop-understanding-aop-proxies)
```

在Spring Web MVC中，可以使用任意对象作为请求命令或者是表单回写对象;无需实现框架指定接口或者是基类。spring数据绑定非常灵活：比如，它将类型不匹配处理为验证错误，该错误由应用抛出，而不是作为系统错误。因此，无需你复制你的业务对象属性、非常简单的就能处理form表单中未定义的字串，或者是转换Strings的类型。当然，最好是直接绑定你的业务对象。

Spring的视图解决方案非常灵活。一个`Controller`通常是负责将数据转换成model map，并选择一个view name，但是它也能直接向response 流中写入来完成request.View视图名字解决方案是可配置的，实现途径多种多样：通过文件扩展或者Accetp header content type，通过bean名字，一个properties属性文件,甚至是自定义的`ViewResolver`实现。model模型（MVC中的M）是一个`Map`接口，它是视图技术的基础。可以直接集成基于渲染技术的模板，像JSP,Velocity和Freemarker,或者直接生成XML,JSON,Atom和许多其他类型的内容。model Map将会进行简单的转换为合适的格式，像JSP中的reqeust attributes,Velocity模板的model。

<h4 id='mvc-features'>Spring Web MVC的功能</h4>
```text
Spring Web Flow

Spring Web Flow (SWF)意在管理web应用页面流程的。 

SWF支持现有框架集成，比如Spring MVC和JSF，Servelt环境和Port了环境都行。如果你有也业务，需要将model模型转换为纯粹的request model，那么SWF也许是最好的解决方案 。

SWF允许你捕获逻辑页面流程作为字包含模块，该模块将会在不同的场景中重用，比如，用户向导就是通过控制导航用来驱动业务处理。

For more information about SWF, consult the Spring Web Flow website.
有关SWF更多的信息，参阅[Spring Web Flow网站 ](http://projects.spring.io/spring-webflow/)
```

Spring的 web模块包含很多特有的web 支持功能：
* 清晰的角色分离：controller, validator, command object, form object, model object, DispatcherServlet, handler mapping, view resolver等等，都存在相关的专用对象
* 强大的、简单的配置。配置能力包括易于跨context引用，比如在web controller中引用业务对象。
* 适应能力强、非侵入，非常灵活。controller的方法签名随意定义，参数可用注解用来解决给定的场景（比如@RequestPram,@RequestHeader,@PathVariable等等）
* 重用业务代码，无需重复。使用已经存在的业务对象或者form 对象，无需复制或者继承指定的框架基类。
* 自定义数据绑定和验证。类型不匹配作为应用级别验证错误并保持现其值，本地时间和数字绑定等等用来替代现有的转换机制，现有转换机制是指：仅有String的form对象和业务对象之间互相转换
* 自定义handler mapping处理映射和视图解决方案。Handler mapping和视图解决方案策略，从简单的到复杂的，以及特定的解决策略，都行。Spring和其他mvc框架相比，更灵活。
* Flexible model transfer. Model transfer with a name/value Map supports easy integration with any view technology.
* Customizable locale, time zone and theme resolution, support for JSPs with or without Spring tag library, support for JSTL, support for * * * Velocity without the need for extra bridges, and so on.
* A simple yet powerful JSP tag library known as the Spring tag library that provides support for features such as data binding and themes. The custom tags allow for maximum flexibility in terms of markup code. For information on the tag library descriptor, see the appendix entitled Chapter 39, spring.tld
* A JSP form tag library, introduced in Spring 2.0, that makes writing forms in JSP pages much easier. For information on the tag library descriptor, see the appendix entitled Chapter 40, spring-form.tld
* Beans whose lifecycle is scoped to the current HTTP request or HTTP Session. This is not a specific feature of Spring MVC itself, but rather of the WebApplicationContext container(s) that Spring MVC uses. These bean scopes are described in Section 5.5.4, “Request, session, and global session scopes”


<h3 id='mvc-introduction-pluggability'>其他MVC实现的可拔插集成</h3>
在有些项目中，非SPring的 MVC实现是可取的。很多团队希望利用已经存在的技术和工具，比如JSF.
如果不想使用Spring’s Web MVC，但是想使用Spring其他的东西，那么就可以使用Spring集成你选择的MVC框架，非常容易。通过`ContextLoaderListener`启动Spring root application Context （Spring上下文），在任意的action对象中通过`ServletContext`属性访问上下文环境。无插件，无集成。在web层的view中，像使用类库一样使用Spring,root application context应用上下文作为Spring的访问入口。
反正就是一句话，不用Spring MVC，照样可以使用Spring管理bean ,注册Service

<h3 id='mvc-servlet>DispatcherServlet</h3>
Spring’s web MVC framework像许多其他的web MVC框架一样，request驱动，以一个Servlet为中心，该Servlet分发请求给controller ，并提供web引用开发相关的工具。Spring的`DispatcherServlet`不仅仅是只干这些。它和Spring IoC容器完全无缝集成，因此可以使用spring所有的功能。 
下图展示Spring Web MVC `DispatcherServlet`处理request的流程。细心的读者会看到，`DispatcherServlet`就是*表示层设计模式*(这个模式是Spring MVC共享给其他主流web框架的)
![DispatchServlet](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/mvc.png)

Spring MVC中处理request的流程（架构示意图）

`DispatcherServlet`是一个`Servlet`，它继承自`HttpServlet`，像下面这样的在`web.xml`中的声明。还得映射需要交由`DispatcherServlet`处理的requests,同样也是在`web.xml`中使用URL映射。这是一个标准的Java EE Servlet配置；下面的样例展示了`DispatcherServlet`声明和映射:
```xml
<web-app>
    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>example</servlet-name>
        <url-pattern>/example/*</url-pattern>
    </servlet-mapping>

</web-app>
```

在上面的样例中，所有的以`/example`开头的request将会由一个叫`example`的`DispatcherServlet`实例处理。在Servlet3.0+的环境中，还可以通过编程式的方式配置DispatcherServlet。下面的代码好上面的xml配置是等效的:
```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet());
        registration.setLoadOnStartup(1);
        registration.addMapping("/example/*");
    }

}
```

`WebApplicationInitializer `是由Spring MVC提供的一个接口，Spring MVC还可以确保你基于代码的配置可以被探测并自动初始化一个Servlet 3容器。有个抽象基类实现类此接口，叫`AbstractDispatcherServletInitializer`,可以更容易的注册`DispatcherServlet `，只需要简单的指定映射即可，详情请参看[Code-based Servlet container initialization](#mvc-container-config)。

上面只是配置Spring Web MVC的第一步。还需要通过使用Spring Web MVC framework配置各种beans。

就像[Section 5.15, “Additional Capabilities of the ApplicationContext”](#context-introduction)所讲的,Spring中的`ApplicationContext `实例是有作用域的。在 Web MVC框架中，每一个`DispatcherServlet`都有自己的`WebApplicationContext`，该`WebApplicationContext`继承了根`WebApplicationContext`，因此，子`WebApplicationContext`可以访问父容器中定义的所有的bean。TODO。These inherited beans can be overridden in the servlet-specific scope, and you can define new scope-specific beans local to a given Servlet instance.这些集成来的bean可以在servlet-specific作用域内被覆盖，也可以为Servlet实例指定新的作用域bean。

**Figure 20.1. Context hierarchy in Spring Web MVC**
![](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/mvc-contexts.gif)

`DispatcherServlet`初始化时，Spring MVC会查找并加载名为[servlet-name]-servlet.xml的文件，默认查找的目录是web应用的`WEB-INF`目录，加载完成后就创建xml中定义的beans，会覆盖容器中所有的重名bean。

考虑下面`DispatcherServlet`Servlet配置（在web.xml文件中）
```
<web-app>
    <servlet>
        <servlet-name>golfing</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>golfing</servlet-name>
        <url-pattern>/golfing/*</url-pattern>
    </servlet-mapping>
</web-app>
```
根据上面的配置，必须得在应用中存在`/WEB-INF/golfing-servlet.xml`文件。这个文件中包含了所有Spring Web MVC指定的组件（beans）。配置文件的位置也是可以修改的，通过Servlet初始化参数，下面详细讲解。

若只有一个`DispatcherServlet`，且只有一个配置文件，那么就可以不用设置Servlet 初始化参数`contextConfigLocation`。
像下面这样讲解如何通过Servlet参数设置来修改配置文件位置:
```xml
<web-app>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```

`WebApplicationContext`是简单的`ApplicationContext`的扩展，`WebApplicationContext`有一些web应用所必须的方法。它和`ApplicationContext `的不同之处就是对于主题处理的能力[(see Section 20.9, “Using themes”)](#mvc-themeresolver),和并且它知道它和哪一个Servlet项关联(有一个ServletContext的引用)。`WebApplicationContext`被`ServletContext`绑定，如果你需要访问它，使用`RequestContextUtils`类的静态方法可以检索到`WebApplicationContext`

<h4 id='mvc-servlet-special-bean-types'>WebApplicationContext中的特殊bean</h4>
`DispatcherServlet `使用特殊的bean处理request和相应的view。这些bean是Spring  MVC的一部分。可以通过简单的配置选择`WebApplicationContext`中的特殊bean。如果你啥都不配置，也没关系，Spring都为这些bean指定了默认的实现。接下来看看这些特殊bean。

Bean type	| Explanation
----------- | -------------
HandlerMapping | Maps incoming requests to handlers and a list of pre- and post-processors (handler interceptors) based on some criteria the details of which vary by HandlerMapping implementation. The most popular implementation supports annotated controllers but other implementations exists as well.
HandlerAdapter | Helps the DispatcherServlet to invoke a handler mapped to a request regardless of the handler is actually invoked. For example, invoking an annotated controller requires resolving various annotations. Thus the main purpose of a HandlerAdapter is to shield the DispatcherServlet from such details.
HandlerExceptionResolver | Maps exceptions to views also allowing for more complex exception handling code.
ViewResolver | Resolves logical String-based view names to actual View types.
LocaleResolver & LocaleContextResolver
Resolves the locale a client is using and possibly their time zone, in order to be able to offer internationalized views
ThemeResolver | Resolves themes your web application can use, for example, to offer personalized layouts
MultipartResolver | Parses multi-part requests for example to support processing file uploads from HTML forms.
FlashMapManager | Stores and retrieves the "input" and the "output" FlashMap that can be used to pass attributes from one request to another, usually across a redirect.

<h4 id='mvc-servlet-config'>DispatcherServlet默认配置</h4>
`DispatcherServlet `中使用的特殊bean的默认实现，其信息配置在`org.springframework.web.servlet`包中的`DispatcherServlet.properties`。
特殊bean默认实现的存在都是有道理的。很快你就会指定这些bean的自定义实现。比如，有个非常常用的配置，修改`InternalResourceViewResolver `类的`prefix `来设置view 文件的目录。

Regardless of the details, the important concept to understand here is that once you	configure a special bean such as an InternalResourceViewResolver in your WebApplicationContext, you effectively override the list of default implementations that would have been used otherwise for that special bean type. For example if you configure an InternalResourceViewResolver, the default list of ViewResolver implementations is ignored.

In Section 20.16, “Configuring Spring MVC” you’ll learn about other options for configuring Spring MVC including MVC Java config and the MVC XML namespace both of which provide a simple starting point and assume little knowledge of how Spring MVC works. Regardless of how you choose to configure your application, the concepts explained in this section are fundamental should be of help to you.

<h4 id='mvc-servlet-sequence'>DispatcherServlet 处理顺序</h4>
After you set up a DispatcherServlet, and a request comes in for that specific DispatcherServlet, the DispatcherServlet starts processing the request as follows:

* The WebApplicationContext is searched for and bound in the request as an attribute that the controller and other elements in the process can use. It is bound by default under the key DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE.
* The locale resolver is bound to the request to enable elements in the process to resolve the locale to use when processing the request (rendering the view, preparing data, and so on). If you do not need locale resolving, you do not need it.
* The theme resolver is bound to the request to let elements such as views determine which theme to use. If you do not use themes, you can ignore it.
* If you specify a multipart file resolver, the request is inspected for multiparts; if multiparts are found, the request is wrapped in a MultipartHttpServletRequest for further processing by other elements in the process. See Section 20.10, “Spring’s multipart (file upload) support” for further information about multipart handling.
* An appropriate handler is searched for. If a handler is found, the execution chain associated with the handler (preprocessors, postprocessors, and controllers) is executed in order to prepare a model or rendering.
* If a model is returned, the view is rendered. If no model is returned, (may be due to a preprocessor or postprocessor intercepting the request, perhaps for security reasons), no view is rendered, because the request could already have been fulfilled.
* Handler exception resolvers that are declared in the WebApplicationContext pick up exceptions that are thrown during processing of the request. Using these exception resolvers allows you to define custom behaviors to address exceptions.

The Spring DispatcherServlet also supports the return of the last-modification-date, as specified by the Servlet API. The process of determining the last modification date for a specific request is straightforward: the DispatcherServlet looks up an appropriate handler mapping and tests whether the handler that is found implements the LastModified interface. If so, the value of the long getLastModified(request) method of the LastModified interface is returned to the client.

You can customize individual DispatcherServlet instances by adding Servlet initialization parameters ( init-param elements) to the Servlet declaration in the web.xml file. See the following table for the list of supported parameters.

**Table 20.2. DispatcherServlet initialization parameters**

Parameter | Explanation
--------- | -----------
contextClass | Class that implements WebApplicationContext, which instantiates the context used by this Servlet. By default, the XmlWebApplicationContext is used.
contextConfigLocation | String that is passed to the context instance (specified by contextClass) to indicate where context(s) can be found. The string consists potentially of multiple strings (using a comma as a delimiter) to support multiple contexts. In case of multiple context locations with beans that are defined twice, the latest location takes precedence.
namespace | Namespace of the WebApplicationContext. Defaults to [servlet-name]-servlet.

<h3 id='mvc-controller'>实现Controller</h3>
Controllers提供了访问应用的入口。Controllers解析request并转换为model模型，模型向view视图提供数据。Spring高度抽象了controller，这样开发者可通过各种方式创建controller。
Spring 2.5开始，可以使用注解创建controller，比如`@RequestMapping, @RequestParam, @ModelAttribute`等等。这些注解既可用于Spring MVC也可用于 Portlet MVC。这种方式无需继承指定基类或者实现指定接口。此外，无需依赖`Servlet `API或者`Portlet `API，但是可以非常方便的访问他们。

![](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/tip.png)
> 大量的web都是使用注解的，比如* MvcShowcase, MvcAjax, MvcBasic, PetClinic, PetCare*等等，不信你看[https://github.com/spring-projects/](https://github.com/spring-projects/)

```java
@Controller
public class HelloWorldController {

    @RequestMapping("/helloWorld")
    public String helloWorld(Model model) {
        model.addAttribute("message", "Hello World!");
        return "helloWorld";
    }
}
```

`@Controller`和`@RequestMapping`注解不对方法的名和签名做限制，你可以随意，呃，注意要符合java规范。示例中的方法接受了一个`Model`，返回了一个字串view，除此之外Spring还提供了很多的方法参数、返回值，预知详情，请关注文档更新进度。`@Controller`和`@RequestMapping`还有很多注解构成了Spring MVC实现，本章将详细讲解他们在Servlet环境中的用法。

<h4 id='mvc-ann-controller'>使用@Controller定义一个controller</h4>
`@Controller`注解的类意味着该类是MVC中的C角色,无需继承C角色积累，也无需引用ServletAPI*译注，比如HttpServletReqeust,HttpServletResponse*，若有需要，也能非常容易的引用ServletAPI。

` @Controller`注解表示类作为conroler控制器代码层，dispatcher扫描@Controller注解类并探测`@RequestMapping` 注解然后做映射。

可以在 dispatcher’s上下文环境中，明确的定义注解了`@Controller`类的Spring bean。然而，使用`@Controller`注解的类，可自动探测并自动注册。

为了开启自动探测，得在配置中增加组件扫描功能。在XML 中使用`spring-context`schema:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="org.springframework.samples.petclinic.web"/>

    <!-- ... -->

</beans>
```

<h4 id='mvc-ann-requestmapping'>使用@RequestMapping做映射</h4>
`@RequestMapping`注解可以将URL映射为类入口或者方法入口，比如URL `/appointments`由`AppointmentsController `类处理。通常，类级别注解`@RequestMapping`映射reqeust路径，方法级别`@RequestMapping`用来指明要处理reqeust 路径下的哪些HTTP方法，比如POST/GET/PUT/DELETE等等，或者是指明要处理哪些参数条件。
看样例:
```java
@Controller
//所有"/appointments"路径请求由本controller处理
@RequestMapping("/appointments")
public class AppointmentsController {

    private final AppointmentBook appointmentBook;

    @Autowired
    public AppointmentsController(AppointmentBook appointmentBook) {
        this.appointmentBook = appointmentBook;
    }

    @RequestMapping(method = RequestMethod.GET)
	// HTTP GET方法请求的"/appointments"由此方法处理
    public Map<String, Appointment> get() {
        return appointmentBook.getAppointmentsForToday();
    }

    @RequestMapping(value="/{day}", method = RequestMethod.GET)
	//URI模板模式
    public Map<String, Appointment> getForDay(@PathVariable @DateTimeFormat(iso=ISO.DATE) Date day, Model model) {
        return appointmentBook.getAppointmentsForDay(day);
    }

    @RequestMapping(value="/new", method = RequestMethod.GET)
	//处理HTTP GET方法请求的"/appointments/new"
    public AppointmentForm getNewForm() {
        return new AppointmentForm();
    }

    @RequestMapping(method = RequestMethod.POST)
	// HTTP POST方法请求的"/appointments"由此方法处理
    public String add(@Valid AppointmentForm appointment, BindingResult result) {
        if (result.hasErrors()) {
            return "appointments/new";
        }
        appointmentBook.addAppointment(appointment);
        return "redirect:/appointments";
    }
}
```
上例中多处使用了`@RequestMapping`。第一处是类注解,不翻了，全写注释里了。

`getForDay()`方法展示了`@RequestMapping`另一种用法:URI模板，详情参看[mvc-ann-requestmapping-uri-templates](#mvc-ann-requestmapping-uri-templates)

`@RequestMapping`类注解不是必须的。若不写的话，不利于路径规划。看样例:
```java
@Controller
public class ClinicController {

    private final Clinic clinic;

    @Autowired
    public ClinicController(Clinic clinic) {
        this.clinic = clinic;
    }

    @RequestMapping("/")
    public void welcomeHandler() {
    }

    @RequestMapping("/vets")
    public ModelMap vetsHandler() {
        return new ModelMap(this.clinic.getVets());
    }

}
```
上例中未指定GET vs. PUT, POST等方法，因为默认情况下，`@RequestMapping`将会处理相关路径下的所有的HTTP方法。使用这种`@RequestMapping(method=GET)`方式才能精准的映射。

<h5 id='mvc-ann-requestmapping-proxying'>@Controller和AOP代理</h5>
有些情况下，controller也许会有AOP代理装饰。比如，在controller上直接定义`@Transactional`注解。这种情况，推荐使用类注解。然而，如果controller需要实现一个非Spring 回调接口(也就是`InitializingBean, *Aware`等等),则需要明确的配置基于类的代理。比如，使用了`<tx:annotation-driven />`就得改为`<tx:annotation-driven proxy-target-class="true" />`。

<h5 id='mvc-ann-requestmapping-31-vs-30'>Spring 3.1中为@RequestMapping方法新增的支持类</h5>
Spring 3.1 introduced a new set of support classes for @RequestMapping methods called RequestMappingHandlerMapping and RequestMappingHandlerAdapter respectively. They are recommended for use and even required to take advantage of new features in Spring MVC 3.1 and going forward. The new support classes are enabled by default by the MVC namespace and the MVC Java config but must be configured explicitly if using neither. This section describes a few important differences between the old and the new support classes.

Prior to Spring 3.1, type and method-level request mappings were examined in two separate stages — a controller was selected first by the DefaultAnnotationHandlerMapping and the actual method to invoke was narrowed down second by the AnnotationMethodHandlerAdapter.

With the new support classes in Spring 3.1, the RequestMappingHandlerMapping is the only place where a decision is made about which method should process the request. Think of controller methods as a collection of unique endpoints with mappings for each method derived from type and method-level @RequestMapping information.

This enables some new possibilities. For once a HandlerInterceptor or a HandlerExceptionResolver can now expect the Object-based handler to be a HandlerMethod, which allows them to examine the exact method, its parameters and associated annotations. The processing for a URL no longer needs to be split across different controllers.

There are also several things no longer possible:

Select a controller first with a SimpleUrlHandlerMapping or BeanNameUrlHandlerMapping and then narrow the method based on @RequestMapping annotations.
Rely on method names as a fall-back mechanism to disambiguate between two @RequestMapping methods that don’t have an explicit path mapping URL path but otherwise match equally, e.g. by HTTP method. In the new support classes @RequestMapping methods have to be mapped uniquely.
Have a single default method (without an explicit path mapping) with which requests are processed if no other controller method matches more concretely. In the new support classes if a matching method is not found a 404 error is raised.
The above features are still supported with the existing support classes. However to take advantage of new Spring MVC 3.1 features you’ll need to use the new support classes.

<h5 id='mvc-ann-requestmapping-uri-templates'>URI模板模式</h5>
*URI模板*大大的方便了@RequestMapping方法中URL配置。
URI 模板是类URI字串，包含一个或多个变量名，为变量设置值时，它就成了URI。在[proposed RFC](http://bitworking.org/projects/URI-Templates/)中定义了是如何参数化的。比如，URI模板`http://www.example.com/users/{userId}`包含一个变量userId,设置userId变量的值为*fred*，`http://www.example.com/users/fred`。

在方法参数上使用 `@PathVariable`注解，将会绑定URI中变量的值到参数上:
```java
@RequestMapping(value="/owners/{ownerId}", method=RequestMethod.GET)
public String findOwner(@PathVariable String ownerId, Model model) {
    Owner owner = ownerService.findOwner(ownerId);
    model.addAttribute("owner", owner);
    return "displayOwner";
}
```

URI模板`/owners/{ownerId}`声明了变量`ownerId`。当ctroller处理该request时候，Spring MVC将会从请求路径中取出ownerId变量值，将变量值绑定到参数上。比如，请求`/owners/fred`时，变量ownerId就是`fred`。

![DispatchServlet](http://docs.spring.io/spring/docs/4.2.0.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/images/tip.png)
在处理`@PathVariable`过程中，Spring MVC 以 by name方式从URI模板中匹配变量，`@PathVariable`可以指定name:
```java
@RequestMapping(value="/owners/{ownerId}", method=RequestMethod.GET)
public String findOwner(@PathVariable("ownerId") String theOwner, Model model) {
    // implementation omitted
}
```

如果URI模板中变量name和方法参数name相同，则无需配置@PathVariable的name。只要是编译时未去除调试信息，Spring MVC就能匹配与参数重名的URI模板变量:
```java
@RequestMapping(value="/owners/{ownerId}", method=RequestMethod.GET)
public String findOwner(@PathVariable String ownerId, Model model) {
    // implementation omitted
}
```

一个方法可以有多个`@PathVariable`
```java
@RequestMapping(value="/owners/{ownerId}/pets/{petId}", method=RequestMethod.GET)
public String findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
    Owner owner = ownerService.findOwner(ownerId);
    Pet pet = owner.getPet(petId);
    model.addAttribute("pet", pet);
    return "displayPet";
}
```

当`@PathVariable`注解用于`Map<String, String>`参数，所有的URI 模板中的变量都会置入map。

URI模板可以是`@RequestMapping`类注解和方法注解的合集，比如URL `/owners/42/pets/21`将会调用`findPet()`方法
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @RequestMapping("/pets/{petId}")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

`@PathVariable`参数可以是任何简单类型，`int, long, Date`等等,Spring 自动转换合适的类型，转换不成功时，抛异常`TypeMismatchException `。不过，可以注册自定义转换器。详情参看[the section called “Method Parameters And Type Conversion”](#mvc-ann-typeconversion) and [the section called “Customizing WebDataBinder initialization”](#mvc-ann-webdatabinder).

<h5 id='mvc-ann-requestmapping-uri-templates-regex'>URI 模板模式与正则表达式</h5>
有时需要对URI模板变量进行精准的控制，比如`"/spring-web/spring-web-3.0.5.jar"`如何定义各个部分变量
` @RequestMapping`支持URI模板变量中正则表达式，`{varName:regex}`语法，前面是变量名字，后面是正则表达式。
```java
@RequestMapping("/spring-web/{symbolicName:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{extension:\\.[a-z]+}")
    public void handle(@PathVariable String version, @PathVariable String extension) {
        // ...
    }
}
```

<h5 id='mvc-ann-requestmapping-patterns'>路径模式</h5>
`@RequestMapping`也支持ant风格路径，比如`/myPath/*.do`。URI模板变量和ant风格路径也可以组合使用，`/owners/*/pets/{petId}`。

<h5 id='mvc-ann-requestmapping-pattern-comparison'>路径模式比较</h5>
当URL匹配了多种模式，各种模式会有一个匹配度的排序，也就是说哪些模式更精准的匹配，就会选用哪些模式。

具有较低计数量的模板变量和通配符更特殊、更具体。比如，`/hotels/{hotel}/*`比`/hotels/{hotel}/**`更具体、更优先。

如果两个模式具有相同的计数量，俺么更短的胜出。比如，`/foo/bar*`比`/foo/*`更长，因此更具体

当两个模式具有相同的计数和长度，具有更少的通配符的胜出。`/hotels/{hotel}`比`/hotels/*`更优先。

还有一些其他的规则
* **默认的映射模式**`/**`比任何其他模式都具有更低的优先级，比如，`/api/{a)}/{b}/{c}`与之相比更具体更优先
* **前缀模式**`/public/**`比其他任何没有双通配符的模式具有更低的优先级，比如，`/public/path3/{a}/{b}/{c}`比它更具体更优先

更多详情参看`AntPathMatcher`中的`AntPatternComparator`。注意PathMatcher 可以自定义，参看[ Section 20.16.9, “Path Matching”](#mvc-config-path-matching)

<h5 id='mvc-ann-requestmapping-placeholders'>路径模式和占位符</h5>
`@RequestMapping`注解支持${...}占位符，占位符读取的是本地properties、系统properties、环境变量。若是需要根据配置文件来改变controller映射路径，此办法就可以大显身手了。占位符的更多细节，请参看`PropertyPlaceholderConfigurer`类的javadocs。

<h5 id='mvc-ann-requestmapping-suffix-pattern-match'>前缀匹配路径模式</h5>
Spring MVC默认执行`".*"`前缀模式匹配，因此`/person`也会匹配`/persion.*`。这样就可以通过文件扩展名指明内容类型，比如`/pserson.pdf`,`/person.xml`等等。
由此产生一个让人困惑的地方，就是当路径最后的部分是一个URI变量，比如`/persion/{id}`。当request请求`/psersion/1.json`，既能匹配路径变量 id=1 ，也能匹配扩展名".json"，当id包含一个点的时候，比如`/person/joe@email.com`，spring将不会认为`joe@email.com`是id，但是，`com`不是文件扩展名。

要解决此问题，得配置Spring MVC 的前缀模式匹配和注册的文件扩展名协商处理 。For more on this, first see [Section 20.16.4, “Content Negotiation” ](#mvc-config-content-negotiation)and then [Section 20.16.9, “Path Matching” ](#mvc-config-path-matching)展示了如何开启前缀匹配和如何只用注册的前缀匹配。

<h5 id='mvc-ann-matrix-variables'>Matrix Variables矩阵变量</h5>
URI规范，是在路径中可能含有键值对。在规范中并未包含特殊项。SpringMVC就能搞这些特殊项。
矩阵变量可以出现在任意路径中，每一个矩阵变量有";"分号分隔。比如:
`"/cars;color=red;year=2012"`，多个值的话使用","逗号分隔，`"color=red,green,blue"`，或者使用重复的变量名`"color=red;color=green;color=blue"`。 

If a URL is expected to contain matrix variables, the request mapping pattern must represent them with a URI template. This ensures the request can be matched correctly regardless of whether matrix variables are present or not and in what order they are provided.

下例演示解析矩阵变量"q":
```java
// GET /pets/42;q=11;r=22

@RequestMapping(value = "/pets/{petId}", method = RequestMethod.GET)
public void findPet(@PathVariable String petId, @MatrixVariable int q) {

    // petId == 42
    // q == 11

}
```

因为路径中的任意段都可能会包含矩阵变量，有些场景下你需要更特殊的用法去识别变量:
```java
// GET /owners/42;q=11/pets/21;q=22

@RequestMapping(value = "/owners/{ownerId}/pets/{petId}", method = RequestMethod.GET)
public void findPet(
        @MatrixVariable(value="q", pathVar="ownerId") int q1,
        @MatrixVariable(value="q", pathVar="petId") int q2) {

    // q1 == 11
    // q2 == 22

}
```
矩阵变量也可以定义为可选，并设置默认值
```java
// GET /pets/42

@RequestMapping(value = "/pets/{petId}", method = RequestMethod.GET)
public void findPet(@MatrixVariable(required=false, defaultValue="1") int q) {

    // q == 1

}
```

矩阵变量可以置入一个Map:
```java
// GET /owners/42;q=11;r=12/pets/21;q=22;s=23

@RequestMapping(value = "/owners/{ownerId}/pets/{petId}", method = RequestMethod.GET)
public void findPet(
        @MatrixVariable Map<String, String> matrixVars,
        @MatrixVariable(pathVar="petId"") Map<String, String> petMatrixVars) {

    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 11, "s" : 23]

}
```

注意，若要开启矩阵变量功能，必须设置`RequestMappingHandlerMapping`的属性`removeSemicolonContent `为`false`。该值默认为`true`。

MVC Java config and the MVC namespace都提供了开启矩阵变量的选项。

若是Java config，[Advanced Customizations with MVC Java Config](#mvc-config-advanced-java)章节讲解了如何设置`RequestMappingHandlerMapping `.

若是MVC namespace命名空间，`<mvc:annotation-driven>`元素的`enable-matrix-variables`属性则应该设置为`true`。默认他是`false`。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <mvc:annotation-driven enable-matrix-variables="true"/>

</beans>
```

<h5 id='mvc-ann-requestmapping-consumes'>消费媒体类型</h5>
可以设置映射的消费媒体类型，类型可以指定多个。那么，只有reqeust的*Content-Type*请求头匹配映射中设置的消费类型，才会由mapping映射处理该request。
```
@Controller
@RequestMapping(value = "/pets", method = RequestMethod.POST, consumes="application/json")
public void addPet(@RequestBody Pet pet, Model model) {
    // implementation omitted
}
```

消费类型可以使用非!运算表达式，*!text/plain*，意思是除了text/plain类型，其他所有的类型都可以匹配。

消费类型条件支持方法映射中配置，也支持类映射中配置。一般情况下，在类注解和方法注解中配置了条件，方法注解中的条件将会覆盖类注解中的条件，但是，消费类型条件是继承、扩展。

<h5 id='mvc-ann-requestmapping-produces'>生产媒体类型</h5>
和消费媒体类型差不多。如果*Accept *reqeust header匹配了配置的生产媒体类型，则@RequestMapping处理request。
```java
@Controller
@RequestMapping(value = "/pets/{petId}", method = RequestMethod.GET, produces="application/json")
@ResponseBody
public Pet getPet(@PathVariable String petId, Model model) {
    // implementation omitted
}
```
非运算符也支持。
方法注解中的生产媒体类型配置也是I扩展类注解中生产媒体配置。 

<h5 id='mvc-ann-requestmapping-params-and-headers'>Reqeust参数和Header Value</h5>
使用参数条件，可reqeust参数匹配更精准，比如`"myParam","!myParam",或者"myParam=myValue`,第一二个是检查存在/不存在，第三个是为了检查是否为指定值。参看样例：
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @RequestMapping(value = "/pets/{petId}", method = RequestMethod.GET, params="myParam=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

对于request header的存在/不存在，或者匹配是否为指定的值也可使用类似的方法:
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @RequestMapping(value = "/pets", method = RequestMethod.GET, headers="myHeader=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
> 虽然可以用request header检查*Content-Type*和*Accept header*的值（比如，"content-type=text/*"匹配"text/plain"和"text/html"），但是推荐使用*consumes*和*produces*条件检查媒体类型相关header value，媒体类型条件检查就是为了干这个用的。

<h4 id='mvc-ann-methods'>定义@RequestMapping 处理方法</h4>
`@RequestMapping`方法非常灵活，几乎不受任何限制。支持的方法参数和返回值类型，在下面详述。除`BindingResult `类型参数外，大多数参数次序随意。
![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
> Spring 3.1引入了一组`@RequestMapping`方法的支持类，分别是`RequestMappingHandlerMapping`和`RequestMappingHandlerAdapter`。

<h5 id='mvc-ann-arguments'>支持的方法参数类型</h5>
系列是支持的方法参数
* ServletAPI中的Rquest或者Response对象。比如`ServletRequest`或者`HttpServletReqeust`。
* Session 对象：比如`HttpSession`。此类型的参数将会注入响应的session，因此，此参数永远不为null。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
> Session访问也许不是线程安全的，尤其是在Servlet花逆境中。加入允许多个Request可并发的访问session,可考虑使用`RequestMappingHandlerAdapter`的"synchronizeOnSession"属性为"true"

* `org.springframework.web.context.request.WebRequest`或者`org.springframework.web.context.request.NativeWebRequest`
* org.springframework.web.context.request.WebRequest or org.springframework.web.context.request.NativeWebRequest. Allows for generic request parameter access as well as request/session attribute access, without ties to the native Servlet/Portlet API.
* java.util.Locale for the current request locale, determined by the most specific locale resolver available, in effect, the configured LocaleResolver / LocaleContextResolver in an MVC environment.
* java.util.TimeZone (Java 6+) / java.time.ZoneId (on Java 8) for the time zone associated with the current request, as determined by a LocaleContextResolver.
* java.io.InputStream / java.io.Reader for access to the request’s content. This value is the raw InputStream/Reader as exposed by the Servlet API.
* java.io.OutputStream / java.io.Writer for generating the response’s content. This value is the raw OutputStream/Writer as exposed by the Servlet API.
* org.springframework.http.HttpMethod for the HTTP request method.
* java.security.Principal containing the currently authenticated user.
* @PathVariable annotated parameters for access to URI template variables. See the section called “URI Template Patterns”.
* @MatrixVariable annotated parameters for access to name-value pairs located in URI path segments. See the section called “Matrix Variables”.
* @RequestParam annotated parameters for access to specific Servlet request parameters. Parameter values are converted to the declared method argument type. See the section called “Binding request parameters to method parameters with @RequestParam”.
* @RequestHeader annotated parameters for access to specific Servlet request HTTP headers. Parameter values are converted to the declared method argument type. See the section called “Mapping request header attributes with the @RequestHeader annotation”.
* @RequestBody annotated parameters for access to the HTTP request body. Parameter values are converted to the declared method argument type using HttpMessageConverters. See the section called “Mapping the request body with the @RequestBody annotation”.
* @RequestPart annotated parameters for access to the content of a "multipart/form-data" request part. See Section 17.10.5, “Handling a file upload request from programmatic clients” and Section 17.10, “Spring’s multipart (file upload) support”.
* HttpEntity<?> parameters for access to the Servlet request HTTP headers and contents. The request stream will be converted to the entity body using HttpMessageConverters. See the section called “Using HttpEntity”.
* java.util.Map / org.springframework.ui.Model / org.springframework.ui.ModelMap for enriching the implicit model that is exposed to the web view.
* org.springframework.web.servlet.mvc.support.RedirectAttributes to specify the exact set of attributes to use in case of a redirect and also to add flash attributes (attributes stored temporarily on the server-side to make them available to the request after the redirect). RedirectAttributes is used instead of the implicit model if the method returns a "redirect:" prefixed view name or RedirectView.
* Command or form objects to bind request parameters to bean properties (via setters) or directly to fields, with customizable type conversion, depending on @InitBinder methods and/or the HandlerAdapter configuration. See the webBindingInitializer property on RequestMappingHandlerAdapter. Such command objects along with their validation results will be exposed as model attributes by default, using the command class class name - e.g. model attribute "orderAddress" for a command object of type "some.package.OrderAddress". The ModelAttribute annotation can be used on a method argument to customize the model attribute name used.
* org.springframework.validation.Errors / org.springframework.validation.BindingResult validation results for a preceding command or form object (the immediately preceding method argument).
* org.springframework.web.bind.support.SessionStatus status handle for marking form processing as complete, which triggers the cleanup of session attributes that have been indicated by the @SessionAttributes annotation at the handler type level.
* org.springframework.web.util.UriComponentsBuilder a builder for preparing a URL relative to the current request’s host, port, scheme, context path, and the literal part of the servlet mapping.


The Errors or BindingResult parameters have to follow the model object that is being bound immediately as the method signature might have more that one model object and Spring will create a separate BindingResult instance for each of them so the following sample won’t work:

**Invalid ordering of BindingResult and @ModelAttribute. **
```java
@RequestMapping(method = RequestMethod.POST)
public String processSubmit(@ModelAttribute("pet") Pet pet, Model model, BindingResult result) { ... }
```

Note, that there is a Model parameter in between Pet and BindingResult. To get this working you have to reorder the parameters as follows:
```java
@RequestMapping(method = RequestMethod.POST)
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result, Model model) { ... }
```

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
>JDK 1.8’s java.util.Optional is supported as a method parameter type with annotations that have a required attribute (e.g. @RequestParam, @RequestHeader, etc. The use of java.util.Optional in those cases is equivalent to having required=false.

<h5 id='mvc-ann-return-types'>支持的返回值类型</h5>
支持下列返回值:
* A ModelAndView object, with the model implicitly enriched with command objects and the results of @ModelAttribute annotated reference data accessor methods.
* A Model object, with the view name implicitly determined through a RequestToViewNameTranslator and the model implicitly enriched with command objects and the results of @ModelAttribute annotated reference data accessor methods.
* A Map object for exposing a model, with the view name implicitly determined through a RequestToViewNameTranslator and the model implicitly enriched with command objects and the results of @ModelAttribute annotated reference data accessor methods.
* A View object, with the model implicitly determined through command objects and @ModelAttribute annotated reference data accessor methods. The handler method may also programmatically enrich the model by declaring a Model argument (see above).
* A String value that is interpreted as the logical view name, with the model implicitly determined through command objects and @ModelAttribute annotated reference data accessor methods. The handler method may also programmatically enrich the model by declaring a Model argument (see above).
* void if the method handles the response itself (by writing the response content directly, declaring an argument of type ServletResponse / HttpServletResponse for that purpose) or if the view name is supposed to be implicitly determined through a RequestToViewNameTranslator (not declaring a response argument in the handler method signature).
* If the method is annotated with @ResponseBody, the return type is written to the response HTTP body. The return value will be converted to the declared method argument type using HttpMessageConverters. See the section called “Mapping the response body with the @ResponseBody annotation”.
* An HttpEntity<?> or ResponseEntity<?> object to provide access to the Servlet response HTTP headers and contents. The entity body will be converted to the response stream using HttpMessageConverters. See the section called “Using HttpEntity”.
* An HttpHeaders object to return a response with no body.
* A Callable<?> can be returned when the application wants to produce the return value asynchronously in a thread managed by Spring MVC.
* A DeferredResult<?> can be returned when the application wants to produce the return value from a thread of its own choosing.
* A ListenableFuture<?> can be returned when the application wants to produce the return value from a thread of its own choosing.
* Any other return type is considered to be a single model attribute to be exposed to the view, using the attribute name specified through @ModelAttribute at the method level (or the default attribute name based on the return type class name). The model is implicitly enriched with command objects and the results of @ModelAttribute annotated reference data accessor methods.

<h5 id='mvc-ann-requestparam'>绑定request参数到方法参数上</h5>
`@RequestParam`注解可以绑定request参数到方法参数上。
看样例:
```java
@Controller
@RequestMapping("/pets")
@SessionAttributes("pet")
public class EditPetForm {

    // ...

    @RequestMapping(method = RequestMethod.GET)
    public String setupForm(@RequestParam("petId") int petId, ModelMap model) {
        Pet pet = this.clinic.loadPet(petId);
        model.addAttribute("pet", pet);
        return "petForm";
    }

    // ...

}
```
使用次注解的参数，默认是必须的，但是可以设置参数为可选，设置`@RequestParam`的`required`属性为`false`（比如：`@RequestParam(value="id",required=false)`）。

如果方法参数类型不是`String`，那么Spring将会自动进行类型转换。详情参看[the section called “Method Parameters And Type Conversion”](#mvc-ann-typeconversion).

当`@RequestParam`注解用在`Map<String,String>`或者`MultiValueMap<String,Strng`参数上，那么所有的reqeust 参数都将会绑定到map中。

<h5 id='mvc-ann-requestbody'>使用注解@RequestBody映射request body</h5>
`@RequestBody`方法参数注解表名，HTTP request body绑定到方法参数值上，看样例:
```java
@RequestMapping(value = "/something", method = RequestMethod.PUT)
public void handle(@RequestBody String body, Writer writer) throws IOException {
    writer.write(body);
}
```

可以使用`HttpMessageConverter`转换request body到方法参数上，`HttpMessageConverter `负责将HTTP reqeust消息转换成为对象，也负责将对象转换成HTTP response body。`RequestMappingHandlerAdapter `支持`@RequestBody`注解和下列默认的`HttpMessageConverters`

* `ByteArrayHttpMessageConverter `转换字节数组
* `StringHttpMessageConverter `转换字串
* `FormHttpMessageConverter`负责form表单数据与MultiValueMap<String,String>之间的转换
* `SourceHttpMessageConverter`负责XML与Source之间的互相转换

详情请参看[Message Converters](#rest-message-conversion)。同事也得注意，如果使用了MVC命名空间或者MVC Java config，默认会注册很多message converter。详情参看[Section 20.16.1, “Enabling the MVC Java Config or the MVC XML Namespace”](#mvc-config-enable) 

若需要读写XML，得使用`MarshallingHttpMessageConverter `配置指定的`Marshaller `和`Unmarshaller`实现，详情参看`org.springframework.oxm`包。 看样例代码
```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <util:list id="beanList">
            <ref bean="stringHttpMessageConverter"/>
            <ref bean="marshallingHttpMessageConverter"/>
        </util:list>
    </property
</bean>

<bean id="stringHttpMessageConverter"
        class="org.springframework.http.converter.StringHttpMessageConverter"/>

<bean id="marshallingHttpMessageConverter"
        class="org.springframework.http.converter.xml.MarshallingHttpMessageConverter">
    <property name="marshaller" ref="castorMarshaller" />
    <property name="unmarshaller" ref="castorMarshaller" />
</bean>

<bean id="castorMarshaller" class="org.springframework.oxm.castor.CastorMarshaller"/>
```

`@RequestBody`方法参数也能被注解为`@Valid`，这样，他就会被配置的`Validator `实例校验。Spring MVC会自动配置一个JSR-303 validator。

就像`@ModielAttribute`参数，`Errors`参数可用于校验errors。如果未声明该参数，则会抛出`MethodArgumentNotValidException `异常。异常有`DefaultHandlerExceptionResolver`处理，它会发送`400`error给客户端。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 通过MVC命名空间或者MVC java config配置message转换和配置validator，详情请参看[Section 17.16.1, “Enabling the MVC Java Config or the MVC XML Namespace”](#mvc-config-enable) 

<h5 id='mvc-ann-responsebody'>通过@ResponseBody注解映射response body</h5>
`@ResponseBody`注解与`@RequestBody`相似。该注解是一个方法注解，作用是将返回值直接写入HTTP response流。(既不是Model,也不是视图),比如:
```java
@RequestMapping(value = "/something", method = RequestMethod.PUT)
@ResponseBody
public String helloWorld() {
    return "Hello World";
}
```

上例中，将`Hello World`字串直接写入到response stream响应流中。

使用`@RequestBody`时，Spring使用`HttpMessageConverter`转换对象为response body响应体。详情参看[Message Converters](#rest-message-conversion)

<h5 id='#mvc-ann-restcontroller'>使用@RestController注解创建REST Controller</h5>
Controller实现REST API非常常用，REST 风格Ctroller仅能提供JSON、XML、自定义MediaType content媒体类型。使用`RESTController`类注解，可以非常方便的实现RESTful风格API，用来替代`@RequestMapping`和`@Responsebody`的配合。

`@RestController`是`@ResponseBody`和`@Controller`注解的组合，该注解具有扩展性，也许在将来的发布版中会增加额外的功能。

作为常规`@Controller`，`@RestController`通常配合`@ControllerAdvice`使用。详情请参看 [the section called “Advising controllers with the @ControllerAdvice annotation”](#mvc-ann-controller-advice)

<h5 id='#mvc-ann-httpentity'>使用HttpEntity</h5>
`HttpEntity`与`@RequestBody`、`@Resonsebody`非常像。除了访问request和response body，`HttpEntity`（和response专用子类`ResponseEntity`）也可以访问request和response的headers,像这样:
```java
@RequestMapping("/something")
public ResponseEntity<String> handle(HttpEntity<byte[]> requestEntity) throws UnsupportedEncodingException {
    String requestHeader = requestEntity.getHeaders().getFirst("MyRequestHeader"));
    byte[] requestBody = requestEntity.getBody();

    // 用request header和 body干一些事儿

    HttpHeaders responseHeaders = new HttpHeaders();
    responseHeaders.set("MyResponseHeader", "MyValue");
    return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED);
}
```

上面的样例中获取了request header中`MyRequestHeader`的值，读取body置入一个byte array数组。将`MyResponseHeader `设置如了response，将字串`Hello World`写入的response流中，并设置了response的status code为201(Created)

如同`@RequestBody`和`@ResponseBody`，Spring使用`HttpMessageConverter `进行request和response流之间转换。详情请参看[Message Converters.](#rest-message-conversion)

<h5 id='mvc-ann-modelattrib-methods'>在方法上使用@ModelAttribute</h5>
`@ModelAttribute`注解可以用在方法上或者在方法参数上。本章将方法上注解的用法，下章讲解方法参数上注解的用法。
方法上注解`@ModelAttribute`则表示方法的目的是增加一个或者多个model attributes。所有`@RequestMapping`注解方法支持的参数类型，`@ModelAttribute`注解的方法都支持，但是`@ModelAttribute`方法不能直接从request映射。在同一controller中`ModelAttribute`方法会在`@RequestMapping`方法之前调用。看看它的用法
```java
// 增加单个 attribute
//方法的返回值将添加到 model中，key键是"account"
//也可以自定义key键，如此@ModelAttribute("myAccount")

@ModelAttribute
public Account addAccount(@RequestParam String number) {
    return accountManager.findAccount(number);
}

// 添加多个 attributes

@ModelAttribute
public void populateModel(@RequestParam String number, Model model) {
    model.addAttribute(accountManager.findAccount(number));
    // add more ...
}
```

`@ModelAttribute`方法主要的应用场景，将常用的attribute属性填充到model模型中，比如填充下拉菜单项、宠物类型、或者检索常用对象（像Account）用于在HTML表单中描述数据。后面的用法在下章中讨论。

`@ModelAttribute`注解方法有2中写法，第一种，方法通过返回值给model增加了一个attribute，,第二种，方法接收一个`Model`并且增加了多个model attribute。根据实际情况选择使用。

单个Controller可以有多个`@ModelAttribute`方法。在同一controller中的`@RequestMapping`方法调用之前，会执行所有的`@ModelAtrribute`方法。

`@ModelAttribute`方法也可以定义在`@ControllerAdvice`注解的类中，这样的方法则会应用于多个controller。详情才看 [the section called “Advising controllers with the `@ControllerAdvice` annotation”](#mvc-ann-controller-advice) 

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
> 若未明确指定model attribute name时候，Spring会怎么干？这种情况下，默认会基于其类型指定其name。比如，如果方法返回的对象类型为`Account`，默认名字是`account`。可以通过指定`@ModelAttribute`注解的value来为其指定name。如果直接给`Model`添加attributes，得使用合适的重载方法`addAttribute(..)`，也就是有或者没有指定attribute name。

`@ModelAtribute`注解也可以在`@RequestMapping`方法上使用。这种情况下，`@RequestMapping`方法返回值将会作为model attribute，而不是作为视图name。视图name来自于视图name命名惯例，就像是void方法。详情参看[Section 17.13.3, “The View - RequestToViewNameTranslator”.](#mvc-coc-r2vnt)

<h5 id='mvc-ann-modelattrib-method-args'>在方法参数上使用@ModelAttribute</h5>
前面提到`@ModelAttribute`可用于方法，也可用于方法参数上。本章讲解在方法参数上的用法。

在方法参数上注解`@ModelAttribute`则表明参数应该从model中检索出。 若model中不存在，参数将会被实例化并增加到model中。若存在于model中，Spring将会给参数的field域赋值，值来自于从request中解析出相匹配的值。这就是Spring MVC 中的数据绑定，它是非常有用的机制，将使你免于手动从form field表单域中逐一的取值。
```java
@RequestMapping(value="/owners/{ownerId}/pets/{petId}/edit", method = RequestMethod.POST)
public String processSubmit(@ModelAttribute Pet pet) { }
```
上例中Pet的实例从哪儿来?有以下来源:
* 或许使用了`@SessionAttributes`，则Pet实例已经在model中了。详情参看[the section called “Using @SessionAttributes to store model attributes in the HTTP session between requests”](#mvc-ann-sessionattrib)
* 或许在同一个controller中使用了`@ModelAttribute`注解的方法，所以Pet实例已经存在于model中了，详情参看上一章
* 来自于Spring数据绑定，使用URI模板变量配合转换器方式获取，下面详细讲述。
* 通过Pet空构造获取实例

`@ModelAttribute`注解方法是从数据库检索attribute的常用手段，也可以使用`@SessionAttribute`将数据存储于session中。在某些场景中，可以非常方便的通过URI template variable模板变量配合类型转换器的方式来检索attribute。看样例:
```java
@RequestMapping(value="/accounts/{account}", method = RequestMethod.PUT)
public String save(@ModelAttribute("account") Account account) {

}
```
model attribute的name(也就是"account")匹配URI template variable模板变量。如果你注册了`Converter<String, Account>`，该转换器的实现能把`String字串`accout变成`Account`实例（*译注，比如接收一个主键，从数据库中根据主键查出数据置入Account类实例*），那么上例中的方法将不需要`@ModelAttribute`注解也可以工作。

接下来是数据绑定。`WebDataBinder`类解析request中参数的名字(包括query字串参数和form field表单域)，并通过name匹配model attribute的field域。经过必要的类型转换之后(将String转换成目标域类型)，匹配的域将会赋值。数据绑定和验证在[Chapter 7, Validation, Data Binding, and Type Conversion](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/validation.html)讲解。controller级别的自定义数据绑定处理在[the section called “Customizing WebDataBinder initialization”.](#mvc-ann-webdatabinder)讲解。

数据绑定处理时候，也许会报错，比如必须的field域未找到、类型转换错误等。若要检查这些错误，可在`@ModelAttribute`参数后紧接着增加一个`BindingResult`参数。
```java
@RequestMapping(value="/owners/{ownerId}/pets/{petId}/edit", method = RequestMethod.POST)
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```

使用`BindingResult `时，可以探测到error，当发现error时，通常会跳转到统一的视图，即异常页，Spring的`<errors>`form 标签可以渲染此类信息。

在数据绑定处理时，可以使用同一个`BindingResult`调用自定义的validator校验器,`BindingResult `用于记录数据绑定error。数据绑定和校验errors能够在同一个地方累计然后一并返回给user:
```java
@RequestMapping(value="/owners/{ownerId}/pets/{petId}/edit", method = RequestMethod.POST)
public String processSubmit(@ModelAttribute("pet") Pet pet, BindingResult result) {

    new PetValidator().validate(pet, result);
    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```
或者，还可以使用JSR-303`@Valid`注解自动校验:
```java
@RequestMapping(value="/owners/{ownerId}/pets/{petId}/edit", method = RequestMethod.POST)
public String processSubmit(@Valid @ModelAttribute("pet") Pet pet, BindingResult result) {

    if (result.hasErrors()) {
        return "petForm";
    }

    // ...

}
```

详情参看[See Section 7.8, “Spring Validation”](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/validation.html#validation-beanvalidation) and [Chapter 7, Validation, Data Binding, and Type Conversion](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/validation.html)

<h5 id='mvc-ann-sessionattrib'>使用@SessionAttribute在HTTP会话中存储model attribute</h5>
类注解`@SessionAttributes`声明用于指定handler的session attributes。一般情况下，声明用于存储在session中的model attribute的name或type，用于在接下来的request中传递数据。

看样例，指定一个model attribute name:
```java
@Controller
@RequestMapping("/editPet.do")
@SessionAttributes("pet")
public class EditPetForm {
    // ...
}
```

<h5 id='mvc-ann-redirect-attributes'>指定redirect和flash属性</h5>
默认情况下，在redirect URL中，所有的model attribute都应该作为URI template variable模板变量暴露。上下的属性，包括原始类型、原始类型的集合/数组自动拼接到query 参数中。 

在controller中，也许会包含额外的attribute，主要是用于渲染的目的（比如下拉菜单）。在redirect重定向场景中，为了精准控制，可通过在`@RequestMapping`方法中声明一个`RedirectAttribute`类型的参数，使用该参数在`RedirectView`中增加attribute。此时，若controller方法发生redirect重定向，`RedirectAttributes`的内容就会被使用，否则，将使用`Model`的内容。

`RequestMappingHandlerAdapter `提供一个标志位`"ignoreDefaultModelOnRedirect"`，表示默认的`Model`内容在controller 方法redirect重定向时不会被使用。同时，controller方法应该声明一个`RedirectAttributes`类型的attribute，否则，将没有attribute传递给`RedirecView`。MVC xml配置和MVC Java config中，该值默认都是`false`，这是为了向后兼容。当然了，新的应用中还是推荐设置为`true`。

`RedirectAttributes `接口也可用于flash attribute。不像其他redirect属性那样，拼接到redirect URL后，flash attribute会保存在HTTP session中（因此无需再URL中出现）。当flash attribute从session中移除时候，controller的model模型将为目标redirect URL自动接收这些flash attribute。详情参看 [Section 17.6, “Using flash attributes” ](#mvc-flash-attributes)

<h5 id='mvc-ann-form-urlencoded-data'>使用"application/x-www-form-urlencoded"数据</h5>
之前讲解了`@ModelAttribute`，用于支持从浏览器客户端发出表单提交request请求。它也支持非浏览器提交的request。当使用HTTP PUT request请求，情况则大不相同。浏览器可通过HTTP GET或者HTTP POST提交表单数据。非浏览器客户端则可通过HTTP PUT提交表单数据。因为Servlet规范要求`ServletRequest.getParameter*()`系列方法只能通过HTTP POST访问表单数据，而不是HTTP PUT，呃，处理起来有点困难。

为了支持HTTP PUT和PATCH request请求，`spring-web`模块提供了`HttpPutFormContentFilter`过滤器，在`web.xml`中配置
```xml
<filter>
    <filter-name>httpPutFormFilter</filter-name>
    <filter-class>org.springframework.web.filter.HttpPutFormContentFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>httpPutFormFilter</filter-name>
    <servlet-name>dispatcherServlet</servlet-name>
</filter-mapping>

<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
```

上面的filter拦截了内容类型为`application/x-form-urlencoded`的HTTP/PUT/PATCH的request，从request body读取form数据，并且封装`ServletRequest`，这样可以通过`ServletRequest.getParameter*()`系列方法解析form数据。

[http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 由于`HttpPutFormContentFilter `消费了request body，所以不应该在给PUT和PATCH的URL配置处理器，比如依赖消费媒体类型`application/x-www-form-urlencoded`配置的转换器。它包括了`@RequestBody MultiValueMap<String, String> and HttpEntity<MultiValueMap<String, String>>`

<h5 id='mvc-ann-cookievalue'>使用@CookieValue注解映射cookie值</h5>
`@CookieValue`注解作用是将HTTP cookie值绑定到方法参数上。
假设，有一HTTP request携带了以下cookie
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
如何使用该注解绑定cookie值呢:
```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@CookieValue("JSESSIONID") String cookie) {
    //...
}
```
若方法参数类型不是`String`，则会自动进行类型转换。 See[ the section called “Method Parameters And Type Conversion”.
](#mvc-ann-typeconversion)

在Servlet和Portlet环境中的注解handler处理器方法上支持此注解

<h5 id='mvc-ann-requestheader'>@RequestHeader注解绑定header属性</h5>
`@RequestHeader`注解作用是将request header绑定到方法参数上。

现在有一个request header:
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300

接下来看看如何获取`Accept-Encoding`和`Keep-Alive` headers:
```java
@RequestMapping("/displayHeaderInfo.do")
public void displayHeaderInfo(@RequestHeader("Accept-Encoding") String encoding,
        @RequestHeader("Keep-Alive") long keepAlive) {
    //...
}
```
若方法参数类型不是`String`，则会自动进行类型转换。 See[ the section called “Method Parameters And Type Conversion”.
](#mvc-ann-typeconversion)
![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
>Spring MVC 类型转换支持逗号分隔的字串转为array/List。 比如使用`@RequestHeader("Accept")`注解的方法参数不仅可以`String`类型，也可以是`String[]`或者`List<String`


在Servlet和Portlet环境中的注解handler处理器方法上支持此注解

<h5 id='mvc-ann-typeconversion'>方法参数和类型转换</h5>
从request parameters参数,path variable路径变量，request header头，cookie value饼干值中解析出的字串值，也许需要类型转换，转换相对应的绑定的方法参数的类型、或者是域类型(比如，将request parameter构造为`@ModelAttribute`)。如果需要绑定的对象类型不是	`String`，Spring会自动进行类型转换。支持所有的简单类型的转换，比如int,long,Date等等。可以通过`WebDataBinder`更进一步的自定义转换过程(see the [section called “Customizing WebDataBinder initialization](#mvc-ann-webdatabinder)”)，或者是通过使用`FormattingConversionService `注册`Formatters`（详情参看"[Spring Field Formatting](#format)"）

<h5 id='mvc-ann-webdatabinder'>自定义WebDataBinder 初始化</h5>
使用Spring的`WebDataBinder`自定义request parameter参数绑定到PropertyEditors,可以在controller内方法上使用`@InitBinder`注解，或者是在`@ControllerAdvice`注解的类上使用`@InitBinder`注解方法,或者是提供自定义`WebBindingInitializer`。详情参看 [“Advising controllers with the `@ControllerAdvice` annotation”](#mvc-ann-controller-advice) section for more details.

<h5 id='mvc-ann-initbinder'>使用@InitBinder自定义数据绑定</h5>
使用`@InitBinder`注解controller的方法是一种在controller类内直接配置数据绑定的方式。`@InitBinder`注解的方法会初始化`WebDataBinder`，该`WebDataBinder`用于数据绑定，将从request解析出来的值填充到相应的绑定对象上。这种init-binder方法不支持command/form 命令/表单对象和相应的验证结果对象，其他的`@Requestmapping`方法的所有的参数都支持。init-binder方法一定不能有返回值，即void方法。通常，参数包括`WebDataBinder`，该参数可以组合`WebRequest`或者`Java.util.Locale`，允许注册context-specific editors上下文编辑器。

下例中使用`@InitBinder`为所有的`Java.util.Date`表单属性配置了`CustomeDateEditor`:
```java
@Controller
public class MyFormController {

    @InitBinder
    public void initBinder(WebDataBinder binder) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        dateFormat.setLenient(false);
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
    }

    // ...

}
```

<h5 id='mvc-ann-webbindinginitializer'>配置自定义WebBindingInitializer</h5>
为了更具体的控制数据绑定初始化，可以提供一个自定义的`WebBindingInitializer`接口实现，该实现设置给一个自定义bean`AnnotationMethodHandlerAdapter`，覆盖其默认配置。

下面的样例摘子PetClinic 应用，讲解如何使用自定义的`WebBindingInitializer`接口实现做配置，`org.springframework.samples.petclinic.web.ClinicBindingInitializer`类配置了一些PetClinic controller需要使用的PropertyEditors

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="cacheSeconds" value="0" />
    <property name="webBindingInitializer">
        <bean class="org.springframework.samples.petclinic.web.ClinicBindingInitializer" />
    </property>
</bean>
```

`@InitBinder`方法也可以在`@ControllerAdvice`注解的类中定义，在这种情况下，`@InitBinder`会应用于所有匹配上的controller。这是`WebBindingInitializer`的另一个应用场景。详情参看 [the section called “Advising controllers with the @ControllerAdvice annotation” section for more details](#mvc-ann-controller-advice).

<h5 id='mvc-ann-lastmodified'>支持Response Header Last-Modified以实现缓存</h5>
`@RequestMapping`也许是想要支持HTTP Request的头`Last-Modified`，因为在Servlet API中定义了`getLastModified`方法，以便缓存内容。它会计算request的latModified最后编辑的`long`值，与Request Header属性`If-Modified-Since`做对比，如果为发生变化，则会返回304。参看代码:
```java
@RequestMapping
public String myHandleMethod(WebRequest webRequest, Model model) {

    long lastModified = // 1. 应用指定的最后编辑时间

    if (request.checkNotModified(lastModified)) {
        // 2. 如果在request在有效期内，则直接返回，无需处理
        return null;
    }

    // 3. 显然已经过期了，此时执行真正的业务逻辑，为视图准备数据
    model.addAttribute(...);
    return "myViewName";
}

```

有两点要注意:`request.checkNotModified(lastModified)`和返回`null`。`request.checkNotModified(lastModified)`在返回`true`之前将response状态设置为304。后者，返回`null`值，配合前者，会使Spring MVC不对request做进一步处理。

<h5 id='mvc-ann-controller-advice'>使用`@ControllerAdvice`注解增强controllers</h5>
`@ControllerAdvice`注解允许实现类通过classpath扫描方式自动探测。当使用MVC命名空间（即XML配置）或者MVC Java config时候，它会自动开启。
`@ControllerAdvice`注解的类，能包含`@ExceptionHandler, @InitBinder, and @ModelAttribute`注解的方法，这些方法将会应用于所有匹配上的controller.

`@ControllerAdvic`也可以指定声明应用范围
```java
// 应用于所有使用了 @RestController注解的controller
@ControllerAdvice(annotations = RestController.class)
public class AnnotationAdvice {}

//应用于指定包内的所有controller
@ControllerAdvice("org.example.controllers")
public class BasePackageAdvice {}

// 应用于指定的类
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class})
public class AssignableTypesAdvice {}
```
更多详情请参看[java doc文档](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)

<h5 id='mvc-ann-jsonview'>Jackson 序列化视图的支持</h5>
有时需要将对象序列化成HTTP response body。Spring支持此功能，内置了JSON视图[Jackson's Serialization Views](http://wiki.fasterxml.com/JacksonJsonViews)就是干这个用的。

JSON视图需要在controler的方法上增加`@ResponseBody`注解或者令该方法返回`ResponseEntity`类型值，`@JsonView`用法非常简单，只需设置该注解的一个参数用于指定view class或者view 接口即可。
```java
@RestController
public class UserController {

    @RequestMapping(value = "/user", method = RequestMethod.GET)
    @JsonView(User.WithoutPasswordView.class)
    public User getUser() {
        return new User("eric", "7!jd#h23");
    }
}

public class User {

    public interface WithoutPasswordView {};
    public interface WithPasswordView extends WithoutPasswordView {};

    private String username;
    private String password;

    public User() {
    }

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    @JsonView(WithoutPasswordView.class)
    public String getUsername() {
        return this.username;
    }

    @JsonView(WithPasswordView.class)
    public String getPassword() {
        return this.password;
    }
}
```

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 虽然`@JsonView`可以指定多个类，但是在controller方法上的`@JsonView`只支持指定一个类。要是非得指定多个类，可通过组合接口实现。

若方法只能返回视图解决方案？？，则只需在model中的指定视图序列化类:
```java
@Controller
public class UserController extends AbstractController {

    @RequestMapping(value = "/user", method = RequestMethod.GET)
    public String getUser(Model model) {
        model.addAttribute("user", new User("eric", "7!jd#h23"));
        model.addAttribute(JsonView.class.getName(), User.WithoutPasswordView.class);
        return "userView";
    }
}
```

<h5 id='mvc-ann-jsonp'>Jackson JSONP的支持</h5>
为了让`ResponseBody`和`ResponseEntity`方法支持[JSONP](http://en.wikipedia.org/wiki/JSONP)，需要声明一个`@ControllerAdvice`bean，该bean继承自`AbstractJsonpResponseBodyAdvice`，需要在构造参数中指明JSONP query 参数名:
```java
@ControllerAdvice
public class JsonpAdvice extends AbstractJsonpResponseBodyAdvice {

    public JsonpAdvice() {
        super("callback");
    }
}
```

若是依赖视图解决方案，当request query参数名中含有`jsonp`或者`callback`参数时，JSONP将自动开启， query参数名也可以通过`jsonpParameterNames`属性自定义。

<h4 id='mvc-ann-async'>异步请求处理</h4>
Spring MVC 3.2增加了基于Servlet 3的异步请求处理。可启动单独线程，并返回`java.util.concurrent.Callable`类型值，此时，Servlet容器主线程可以处理其他request。Spring MVC在单独的线程内使用`TaskExecutor`调用`Callable`，当`Callable`返回时，Servlet容器将该值返回给相应的request并恢复该request进行处理。看样例:
```java
@RequestMapping(method=RequestMethod.POST)
public Callable<String> processUpload(final MultipartFile file) {

    return new Callable<String>() {
        public String call() throws Exception {
            // ...
            return "someView";
        }
    };

}
```
//TODO，查看Spring MVC主Servlet容器的线程处理方式

还有一个选择，使方法返回`DeferredResult`实例。在这种情况下，return的值将会产生一个单独的线程。当然了，线程并不能被Spring MVC感知。比如，返回值将会产生一个
外部事件，像JMS message，scheduled task等等。看样例代码:
```java
@RequestMapping("/quotes")
@ResponseBody
public DeferredResult<String> quotes() {
    DeferredResult<String> deferredResult = new DeferredResult<String>();
    // Save the deferredResult in in-memory queue ...
    return deferredResult;
}

// In some other thread...
deferredResult.setResult(data);
```

当然了，如果没有Servlet 3 异步处理相关支持，理解这些有些困难。看下面的知识点，有助于立即以上功能:
* `ServletRequest`通过`request.startAsync()`方法切换为异步模式。如此做的主要影响，就是Servlet和Filters，退出的情况下保持response open，允许其他线程完成处理。
* 调用`request.startAsync() `返回一个`AsyncContext`，该实例可以进一步控制异步处理。比如，他能为方法提供`dispatch`方法，应用线程中调用该方法则会将request dispatch分发回Servlet容器。异步dispatch 和 forward差不多，区别就是异步是应用线程分发给一个Servlet容器线程，forward是同一个容器线程中同步分发。
* `ServletRequest`提供了当前`DispatcherType`的访问，`DispatcherType`能用来区别在request处理线程初始化时和处理异步dispatch时候，正在处理request的`Servlet`还是`Filter`

下面讲解使用`Callable`异步request处理的事件次序：（1）Controller返回一个Callabel，(2)Spring MVC启动启动异步处理线程，并给`TaskExecutor`提交一个`Callable` (3)`DispatcherServlet`和所有的Filter退出request处理线程，但是response保持打开，(4)`Callable`产生一个result，Spring MVC分发request返回给Servlet容器，(5)`DispatcherServlet`再次调用并处理由`Callable`产生的异步结果。(2), (3), and (4)的执行速度依赖于当前的线程。

使用`DeferredResult `进行异步request处理的事件的次序上上面的次序差不多。不过有以下不同之处：(1)Controller返回`DeferredResult `并将其保存在可访问的内存队列中。（2）Spring MVC开始异步处理。（3）`DispatcherServlet `和所有配置的Filter完成request处理线程但是response保持打开，（4）应用从一些线程中设置`DiferredResult`，Spring MVC分派request回Servlet 容器(5)`DispatcherServlet`再次调用并处理异步产生的result。

本章讲解了异步request处理的主要机制，如何使用和为何使用不在本章讲解范围。详情参看[这些博客](https://spring.io/blog/2012/05/07/spring-mvc-3-2-preview-introducing-servlet-3-async-support)

<h5 id='mvc-ann-async-exceptions'>异步处理的异常处理</h5>
如果controller方法中返回`Callable`时抛出异常，将会发生什么？效果与任意controller方法抛异常差不多。交由同controller中`@ExceptionHandler`注解方法处理，或者是配置的`HandlerExceptionResolver`实例处理

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 在服务器端，`Callable`抛异常时，SPring MVC依旧会将异常分派给Servlet 容器处理。唯一不同的地方就是执行`Callable `的结果是一个`Exception`，该异常必须使用配置好的`HandlerExceptionResolver`实例处理。

当使用`DeferredResult`时，还有一个选择，通过调用`setErrorResult(Object)`并且提供`Exception`或者其他任意对象作为返回结果。如果返回结果是一个`Exception`，他将会使用同controller中注解`@ExceptionHandler`的方法处理，或者是配置的`HandlerExceptionResolver`实例处理。

<h5 id='mvc-ann-async-interception'>异步request 拦截</h5>
一个已存在的`HandlerInterceptor `能实现`AsyncHandlerInterceptor`，该接口提供了额外的方法`afterConcurrentHandlingStarted`。在异步处理开始之后和request处理线程初始化完毕之前调用。详细信息参看`AsyncHandlerInterceptor `javadoc文档

Further options for async request lifecycle callbacks are provided directly on DeferredResult, which has the methods onTimeout(Runnable) and onCompletion(Runnable). Those are called when the async request is about to time out or has completed respectively. The timeout event can be handled by setting the DeferredResult to some value. The completion callback however is final and the result can no longer be set.

Similar callbacks are also available with a Callable. However, you will need to wrap the Callable in an instance of WebAsyncTask and then use that to register the timeout and completion callbacks. Just like with DeferredResult, the timeout event can be handled and a value can be returned while the completion event is final.

You can also register a CallableProcessingInterceptor or a DeferredResultProcessingInterceptor globally through the MVC Java config or the MVC namespace. Those interceptors provide a full set of callbacks and apply every time a Callable or a DeferredResult is used.

<h5 id='mvc-ann-async-configuration'>Configuration for Async Request Processing</h5>

<h5 id='mvc-ann-async-configuration-servlet3'>Servlet 3 Async Config</h5>

To use Servlet 3 async request processing, you need to update web.xml to version 3.0:

<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">

    ...

</web-app>
The DispatcherServlet and any Filter configuration need to have the <async-supported>true</async-supported> sub-element. Additionally, any Filter that also needs to get involved in async dispatches should also be configured to support the ASYNC dispatcher type. Note that it is safe to enable the ASYNC dispatcher type for all filters provided with the Spring Framework since they will not get involved in async dispatches unless needed.

[Warning]
Note that for some Filters it is absolutely critical to ensure they are mapped to be invoked during asynchronous dispatches. For example if a filter such as the OpenEntityManagerInViewFilter is responsible for releasing database connection resources and must be invoked at the end of an async request.
Below is an example of a propertly configured filter:
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
            http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    version="3.0">

    <filter>
        <filter-name>Spring OpenEntityManagerInViewFilter</filter-name>
        <filter-class>org.springframework.~.OpenEntityManagerInViewFilter</filter-class>
        <async-supported>true</async-supported>
    </filter>

    <filter-mapping>
        <filter-name>Spring OpenEntityManagerInViewFilter</filter-name>
        <url-pattern>/*</url-pattern>
        <dispatcher>REQUEST</dispatcher>
        <dispatcher>ASYNC</dispatcher>
    </filter-mapping>

</web-app>

If using Servlet 3, Java based configuration, e.g. via WebApplicationInitializer, you’ll also need to set the "asyncSupported" flag as well as the ASYNC dispatcher type just like with web.xml. To simplify all this configuration, consider extending AbstractDispatcherServletInitializer or AbstractAnnotationConfigDispatcherServletInitializer, which automatically set those options and make it very easy to register Filter instances.

<h5 id='mvc-ann-async-configuration-spring-mvc'>Spring MVC Async Config</h5>

The MVC Java config and the MVC namespace both provide options for configuring async request processing. WebMvcConfigurer has the method configureAsyncSupport while <mvc:annotation-driven> has an <async-support> sub-element.

Those allow you to configure the default timeout value to use for async requests, which if not set depends on the underlying Servlet container (e.g. 10 seconds on Tomcat). You can also configure an AsyncTaskExecutor to use for executing Callable instances returned from controller methods. It is highly recommended to configure this property since by default Spring MVC uses SimpleAsyncTaskExecutor. The MVC Java config and the MVC namespace also allow you to register CallableProcessingInterceptor and DeferredResultProcessingInterceptor instances.

If you need to override the default timeout value for a specific DeferredResult, you can do so by using the appropriate class constructor. Similarly, for a Callable, you can wrap it in a WebAsyncTask and use the appropriate class constructor to customize the timeout value. The class constructor of WebAsyncTask also allows providing an AsyncTaskExecutor.

<h3 id='mvc-ann-tests'>测试Controllers</h3>
`Spring-test`模块为注解Controller提供了一流的支持，[详情参看section 11.3.6,"Spring MVC Test Framework"](#spring-mvc-test-framework)

<h3 id='mvc-handlermapping'>Handler mappings处理映射</h3>
Spring老版本中，用户需要配置一个或者多个`HandlerMapping`beans，配置在应用上下文中用于分派request到合适的处理器中。使用注解controller以后，基本不用如此麻烦了，因为`RequestMappingHandlerMapping`自动查找在`@Controller`注解的bean 中的`@RequestMapping`注解。当然了，要记得，所有的`Handlermapping`类继承自`AbstractHandlerMapping`具有以下属性，可以自定义其行为：
* `interceptors` 拦截器列表。拦截器在以下章节中讨论[Section 17.4.1, “Intercepting requests with a HandlerInterceptor”](#mvc-handlermapping-interceptor)
* `defaultHandler`默认处理器。若没有匹配的处理器，则使用默认处理器处理
* `order`order属性值（详情参看`org.springframework.core.Ordered`接口），Spring将上下文中可用的处理器handler mapping排序，使用排序次序第一的处理器
* `alwaysUseFullPath`。若是`true`，Spring 在当前Servlet上下文中使用全路径查找合适的handler处理器。如果`false`（默认），则使用当前的Servlet mapping的相对路径。比如，如果Servlet做了映射`/testing/*`和设置了`alwaysUseFullPath`属性为`true`，将会使用`/test/viewPage.html`，如果该属性设置为`false`，则会使用`/viewPage.html`
* `urlDecode`默认是`true`，Spring 2.5以后。如果你需要比较编码后的路径，则设置其为`false`。然而，`HttpServletRequest`总是会暴露解码后的Servlet 路径。注意，编码后的路径将不能匹配Servlet路径

下面样例展示了如何配置拦截器:

```xml
<beans>
    <bean id="handlerMapping" class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
        <property name="interceptors">
            <bean class="example.MyInterceptor"/>
        </property>
    </bean>
<beans>
```

<h5 id='mvc-handlermapping-interceptor'>使用HandlerInterceptor拦截request</h5>

Spring的处理器映射机制包含拦截器处理,拦截器可以针对某些request做特殊处理，比如校验。

映射处理器中的拦截器得实现`org.springframework.web.servlet`包中的`HandlerIntercepter`接口。该接口定义了3个方法：`preHandle(...)`方法在相应的处理器执行之前调用。`postHandler(..)`在handler执行之后调用。`afterCompletion(..)`方法在request完成之后执行。这三个方法应该为所有类型的预处理和后处理提供了足够的弹性。

`preHandle(..)`方法返回一个boolean值。该值将决定中断或者继续request处理。·`true`时，handler执行链继续；`false`时，`DispatcherServlet`将认为拦截器本身已经处理好了request(比如，渲染合适的视图),不会继续执行其他拦截器和handler。

使用`intercepters`属性配置拦截器，将会应用于所有继承于`AbstractHandlerMapping`的`HandlerMapping`。看样例:

```xml
<beans>
    <bean id="handlerMapping"
            class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping">
        <property name="interceptors">
            <list>
                <ref bean="officeHoursInterceptor"/>
            </list>
        </property>
    </bean>

    <bean id="officeHoursInterceptor"
            class="samples.TimeBasedAccessInterceptor">
        <property name="openingTime" value="9"/>
        <property name="closingTime" value="18"/>
    </bean>
<beans>
```

```java
package samples;

public class TimeBasedAccessInterceptor extends HandlerInterceptorAdapter {

    private int openingTime;
    private int closingTime;

    public void setOpeningTime(int openingTime) {
        this.openingTime = openingTime;
    }

    public void setClosingTime(int closingTime) {
        this.closingTime = closingTime;
    }

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
            Object handler) throws Exception {
        Calendar cal = Calendar.getInstance();
        int hour = cal.get(HOUR_OF_DAY);
        if (openingTime <= hour && hour < closingTime) {
            return true;
        }
        response.sendRedirect("http://host.com/outsideOfficeHours.html");
        return false;
    }
}
```
所有经由此映射处理的request都将被`TimeBaseAccessInterceptor`拦截。如果当前时间不是工作时间，用户奖杯重定向到一个静态页面。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
>当使用`RequestMappingHandlerMapping`时，实际的handler是一个`HandlerMethod`实例，`HandlerMethod`定义了指定的controller方法用于处理request。

如你所见，Spring适配器类`HandlerIntercepttorAdapter`可以更容易的集成`HandlerInterceptor`接口

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
>上例中，配置的拦截器将会引用于所有的request，这些request使用注解的controller方法处理。如果你要使用拦截器精准拦截URL路径，可以使用MVC命名空间(xml)或者MVC Java config,或者生命`MappedInterceptor `类型的Spring bean。详情参看[Section 17.16.1, “Enabling the MVC Java Config or the MVC XML Namespace”](#mvc-config-enable).

注意，`postHandle`方法并不适用于`@ResponseBody`和`ResponseEntity`方法。这种情况下`HttpMessageConverter`将会在`postHandle`方法之前对response进行写操作并提交response，这令拦截器将不能再改变response，比如，增加一个header。此时应该使用`ResponseBodyAdvice`接口实现类，或者将其声明为`@ControllerAdvice`bean，或者将其配置在`RequestMappingHandlerAdapter`上。

<h3 id='#mvc-viewresolver'>解析视图</h3>
所有的MVC框架都有视图处理,Spring 也不例外，Spring视图技术可以在浏览器中渲染一个模型而不捆绑任何指定的视图技术。开箱即用，Spring支持JSPs,Velocity和XSLT，详情参看[Cahpter18,View technologies](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/view.html)，该章讨论了如何继承和使用大量不同的视图技术。 

Spring处理视图，有两个核心接口`ViewResolver`和`View`。`ViewResolver `提供了视图逻辑名和物理名之间的映射。`View`接口主要用于request的准备工作和使用多种视图技术处理request。

<h4 id='mvc-viewresolver-resolver'>使用ViewResolver接口解析视图</h4>
上一章[Section 17.3, “Implementing Controllers”](#mvc-controller)讨论的，所有的controller中handler方法必须解析一个逻辑试图名,要么指定(返回`String`,`View`或者`ModelAndView`)，或者使用约定俗成。Spring中的Views通过一个逻辑视图名定位，通过视图解析器解析.Spring 提供了大量的视图解析器。看清单:

**Table 17.3. View resolvers**

解析器 | 描述
-----  | -----
`AbstractCachingViewResolver` | 缓存views。通常，views在使用之前需要提前准备；继承此解析器将获得缓存
`XmlViewResolver` | `ViewResolver`的实现，接受一个xml配置，xml的DTD和Spring的bean的相同。默认配置文件是`/WEB-INF/views.xml`
`ResourceBundleViewResolver` | `ViewResolver`的实现，在`ResourceBundle`中使用bean定义，通过绑定base name指定。通常在properties文件中定义bundle，位于classpath中。默认的文件是`views.properties`。
`UrlBasedViewResolver` |  直接将逻辑名转换为URL，无需明确映射定义。如果逻辑视图名与视图资源相同，则无需mapping映射。
`InternalResourceViewResolver` | `UrlBasedViewResolver `的子类，支持`InternalResourceView`（说白了，就是Servlets和JSPs）和`JstlView`的子类和`TilesView`的子类。通过该解析器的`setViewClass(..)`方法为所有的视图指定试图类。详情参看`UrlBasedViewResolver`的javadocs
`VelocityViewResolver / FreeMarkerViewResolver` | `UrlBasedViewResolver `的子类，支持`VelocityView `（就是Velocity templates）或者`FreemarkerView`，也分别支持他们的子类
`ContentNegotiatingViewResolver` | 基于request额文件名或者`Accept`header解析视图。[详情参看See Section 17.5.4, “ContentNegotiatingViewResolver”.](#mvc-multiple-representations)


As an example, with JSP as a view technology, you can use the UrlBasedViewResolver. This view resolver translates a view name to a URL and hands the request over to the RequestDispatcher to render the view.

比如，使用JSP这种视图技术时，可以使用`UrlBasedViewResolver`，视图解析器将视图名转换为一个URL并将request传给RequestDispatcher 用来渲染视图。
```xml
<bean id="viewResolver"
        class="org.springframework.web.servlet.view.UrlBasedViewResolver">
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

如果返回字串`test`作为逻辑视图名，那么视图解析器会将request携带跳转到`/WEB-INF/jsp/test.jsp`，跳转过程中经过`RequestDispatcher`

如果组合了不同的视图技术，则可以使用`ResourceBundleViewResolver`:
```xml
<bean id="viewResolver"
        class="org.springframework.web.servlet.view.ResourceBundleViewResolver">
    <property name="basename" value="views"/>
    <property name="defaultParentView" value="parentView"/>
</bean>
```

`ResourceBundleViewResolver `通过basename 检查`ResouceBundle`，会尝试解析每一个视图，使用propertiy值`[viewname].(class)`作为视图类，property值`[viewname].url`作为视图url。下一张中将会讲解覆盖视图技术，那一张有讲,定衣服父视图，properties中定义的所有视图都会“继承”它。这种方式则可以指定默认视图类。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
>`AbstractCachingViewResolver `的子类缓存他们解析过的视图实例。在某些视图技术中，缓存能提高性能。可通过设置`cache`属性值为`false`关闭缓存。此外，有时必须得刷新缓存，比如Velocity模板编辑过了，使用方法`removeFromCache(String viewName,Local loc)`


<h4 id='#mvc-viewresolver-chaining'>视图解析链</h4>
Spring支持多视图解析器。因此可以链式使用解析器，比如，某些环境下覆盖某些视图。在应用上下文中增加一个或者多个视图解析器就构成了视图解析链,还可以设置`order`属性指定顺序,在解析链中，`order`值越大，则位置越靠后。

下面栗子中，解析链由2个解析器组成，其一是`InternalResourceViewResolver`，该解析器默认是处于解析链末端，另一个是`XmlViewResolver`，用于Excel视图。`InternalResourceViewResolver`不支持Excel视图
```xml
<bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>

<bean id="excelViewResolver" class="org.springframework.web.servlet.view.XmlViewResolver">
    <property name="order" value="1"/>
    <property name="location" value="/WEB-INF/views.xml"/>
</bean>

<!-- in views.xml -->

<beans>
    <bean name="report" class="org.springframework.example.ReportExcelView"/>
</beans>
```

如果指定的视图解析器不能产生视图，Spring将会尝试上下文中其他的解析器，知道解析出一个视图位置。如果所有的计息期都不能返回视图，Spring抛出异常`ServletException`。

视图解析器规范：视图解析器返回null则表明视图未找到。也有一些特殊情，解析器不会这么做，因为在某些情况下，视图解析器不能明确视图是否存在。比如，`InternalResourceViewResolver `内部使用`RequestDispatcher `，分发完才能确定JSP是否存在，但是视图解析去在分发完成之后不能再次执行。`VelocityViewResolver `也有同样的问题。预知哪些视图解析器会报告视图不存在请参看javadocs。因此，将`InternalResourceViewResolver `不放置到解析链末尾的话会造成检测不完全，因为`InternalResourceViewResolver `总是会返回一个视图。

<h4 id='mvc-redirecting'>重定向</h4>
之前提到的，Controller大多数会返回一个逻辑视图名， 由解析器使用相应的视图技术解析该逻辑视图名。比如，JSP是通过Servlet或者JSP引擎处理，这个工作是由`InternalResourceViewResolver`和`InternalResourceView`共同完成，他们通过Servlet API的`RequestDispatcher.forward(..)`方法或者`RequestDispatcher.include()`方法完成内部跳转或者包含。其他的视图技术，比如Velocity、XSLT等都是将视图直接写入到response流中。

有时需要在视图渲染之前，向客户端发个HTTP重定向。比如，向cotroller发出`POST`请求数据，将response委派给了另一个controller（比如成功提交数据），此时，内部跳转forward意味着`POST`来的数据也一并发给了另一个controller,这些数据可能会与其本身所期望接收的数据相冲突。在展示数据之前执行一个重定向，还有一个原因，就是消除用户多次提交的数据。这种情况下，浏览器贤惠发起一个初始`POST`;然后，接到一个response，重定向到另一个URL；最后，浏览器在重定向中发起`GET`请求。因此，对于浏览器而言，当前页面不是POST结果页，而是`GET`结果。最终的效果是，用户不能通过刷新，重新`POST`数据。刷新时，将会得到一个`GET`结果页，而不是重新发送初始`POST`数据。

<h5 id='mvc-redirecting-redirect-view'>重定向视图RedirectView</h5>
强制重定向的方式之一，是返回`RedirectView`实例。此时，`DispatcherServlet`不会使用常规视图解析机制。因为已经指定了(重定向）视图。

`RedirectView `导致`HttpServletResponse.sendRedirect()`的调用，返回客户端一个重定向。默认情况下，推荐将模型属性增加到重定向的URI模板变量中，其余的属性，比如原始类型或者是原始类型集合/数组并自动添加到query parameter中 。

将原始类型数据作为query parameter，也许对于重定向的URL来说，非常有用。当然了，模型也可以包含额外的属性用于渲染视图，比如下拉菜单项，若要在重定向时不将这些额外的属性添加到URL中，可以声明`RedirectAttributes`类型的controller方法参数，`RedirectAttributes`中指定需要的属性，当Controller重定向时，会使用该参数中的指定属性，若没有该参数则会使用model中的属性。

注意，request中的URI模板变量，在重定向时，可以直接使用，不需要通过`Model`或者`RedirectAttributes`明确的增加的URL中，比如:
```java
@RequestMapping(value = "/files/{path}", method = RequestMethod.POST)
public String upload(...) {
    // ...
    return "redirect:files/{path}";
}
```

如果你使用`RedirectView `重定向到本conroller中，推荐将重定向URL注入到controller中，这样让重定向和视图名在一起，而不是硬编码到controller中，下一章详细讨论。 

<h5 id='mvc-redirecting-redirect-prefix'>重定向前缀redirect:</h5>
While the use of RedirectView works fine, if the controller itself creates the RedirectView, there is no avoiding the fact that the controller is aware that a redirection is happening. This is really suboptimal and couples things too tightly. The controller should not really care about how the response gets handled. In general it should operate only in terms of view names that have been injected it。

使用`RedidrectView`造成紧耦合，controller最好是不知道响应如何处理。总之，controller应该进处理注入的逻辑视图名。 

`redirect:`前缀就可以完成解耦工作。若返回的视图名包含前缀`redirect:`，`UrlBasedViewResolver`会解析出，此处需要重定向。前缀冒号后面的部分将会作为重定向URL。

前缀重定向和使用`RedirectView`效果是一样的，但是controller只需关注逻辑视图名。`redirect:/maapp/some/resource`返回的是当前Servlet context 的相对路径，`redirect:http://myhost.com/some/arbitray/path`将会重定向到一个绝对路径。

<h5 id='mvc-redirecting-forward-prefix'>forward:前缀</h5>
Spring MVC也支持`forward:`前缀视图名,它最终由`UrlBaseViewResolver`及其子类解析。它会根据冒号后面的部分的URL创建`InternalResourceView `（最终会运行`RequestDispatcher.forward()`）视图。因此，对于`InternalResourceViewResolver`和`InternalResourceView`(比如JSP)来说，他没什么用处。若是使用其他视图技术时，但是需要强制跳转到Servlet/JSP引擎处理的情况下，就是有用的了。（注意，可以用视图链替代此种方案）

如果视图名含有`forward:`前缀，Spring MVC将不会再处理response的过程中做任何特殊处理。*译注，大概意思就是spring 默认就是使用forward解析视图的，由此可见forward前缀基本就是没啥用了*


<h4 id='mvc-multiple-representations'>ContentNegotiatingViewResolver</h4>
`ContentNegotiatingViewResolver`本身不会解析视图，但是可以委托给其他解析器，就像是客户端指定响应那样选择该视图。2种途径:
* 使用不同的URI指定资源类型，通常是将文件扩展名附加到URI中，比如，`http://www.example.com/users/fred.pdf`将会请求PDF的响应，`http://www.example.com/users/fred.xml`请求XML的响应
* 使用相同的URI和`Accept`HTTP request头请求不同的资源类型，支持的媒体类型参看[media types](http://en.wikipedia.org/wiki/Internet_media_type)。比如，现有HTTP request，`http://www.example.com/users/fred`加`application/pdf`请求头，将会请求pdf响应，`http://www.example.com/users/fred`加`text/xml `请求头将会请求XML响应。这个策略也被称为[content negotiation](http://en.wikipedia.org/wiki/Content_negotiation)

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 使用相同的URI和`Accept` 这个策略有个问题，在浏览器内是否可使用HTML设置该头.比如，在Firefox zhong ,它被固定位为`Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`。因此，通常使用`不同的URI指定资源类型`策略。

Spring 提供了`ContentNegotiatingViewResolver `用于解析基于文件扩展名、`Accept`header的rquest。`ContentNegotiatingViewResolver `本身不会解析视图，但是可以通过设置bean的`ViewResolvers`指定于视图解析链中。

`ContentNegotiatingViewResolver`通过比较request media type(又叫`content-type`)去选择一个合适的视图去处理request，request media type是该视图所关联的所有的`ViewResolvers`能处理的media type。视图列表中第一个与响应类型相容的视图将会返回给客户端。如果`ViewResolver`视图解析链不能提供想用的视图，视图将会通过`DefaultViews`中查找。后者将会忽略逻辑视图名。`Accept `header可以包含通配符，比如`text/*`,这种情况下，具有`text/xml`的Content-Type的`View`视图是相容的。

接下来看看，如何配置`ContentNegotiatingViewResolver `
```xml
<bean class="org.springframework.web.servlet.view.ContentNegotiatingViewResolver">
    <property name="mediaTypes">
        <map>
            <entry key="atom" value="application/atom+xml"/>
            <entry key="html" value="text/html"/>
            <entry key="json" value="application/json"/>
        </map>
    </property>
    <property name="viewResolvers">
        <list>
            <bean class="org.springframework.web.servlet.view.BeanNameViewResolver"/>
            <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
                <property name="prefix" value="/WEB-INF/jsp/"/>
                <property name="suffix" value=".jsp"/>
            </bean>
        </list>
    </property>
    <property name="defaultViews">
        <list>
            <bean class="org.springframework.web.servlet.view.json.MappingJackson2JsonView" />
        </list>
    </property>
</bean>

<bean id="content" class="com.foo.samples.rest.SampleContentAtomView"/>
```

`BeanNameViewResolver `基于bean的name返回视图，`InternalResourceViewResolver `处理视图到jsp page的转换（spring 如何查找并实例化一个视图详情参看"[Resolving views with the ViewResolver interface](#mvc-viewresolver-resolver)"）。`content `是一个`AbstractAtomFeedView`类型bean,返回Atom RSS种子。有关 Atom Feed representation更多信息，参看Atom Views章节。

上面的配置中，如果请求以`.html`结尾，视图解析器将会查抄一个带有`text/html`媒体类型media type的视图。`InternalResourceViewResolver `提供支持`text/html`类型的视图。如果请求以`.atom`结尾，视图解析器将会查找带有`application/atom+xml`media type媒体类型的视图。如果视图名是`content`，`BeanNameViewResolver `将会返回`SampleContentAtomView`。如果request以`.json`结尾，`DefaultViews `将会返回`MappingJackson2JsonView `实例而忽略视图名。除此之外，客户端request可以不用扩展名，而是使用`Accept`header设置media-type,也会产生同样的结果。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/note.png)
> 如果ViewResolvers list没有明确配置`ContentNegotiatingViewResolver`，那么Spring MVC将会使用应用上下文中配置的ViewResolvers 

下面展示，使用URI `http://localhost/content.atom`或者`http://localhost/content`加application/atom+xml的`Accept`header，来返回Atom RSS feed种子的controller 代码
```java
@Controller
public class ContentController {

    private List<SampleContent> contentList = new ArrayList<SampleContent>();

    @RequestMapping(value="/content", method=RequestMethod.GET)
    public ModelAndView getContent() {
        ModelAndView mav = new ModelAndView();
        mav.setViewName("content");
        mav.addObject("sampleContentList", contentList);
        return mav;
    }

}
```

<h3 id='mvc-flash-attributes'>使用flash attributes</h3>
为了在另一个controller中使用本controller中的attributes ，得使用Flash attributes存储`attributes `。常用于redirecting 重定向，比如，* Post/Redirect/Get *模式。 Flash attributes在重定向之前临时保存（通常是在session中），在重定向之后可用，然后直接删掉。

Spring MVC 的flash attributes有两个核心类`FlashMap `用于持有 flash attributes，`FlashMapManager `用于存储、检索、管理`FlashMap`实例。

Flash attribute 默认是开启的，无需明确开启，它不会引起session 创建。每一个request都会有一个从上个 request传来的"input"`FlashMap`，同时作为"output"`FlashMap`传给下一个request。在Spring MVC中任何地方，通过`RequestContextUtils`类中的静态方法，`FlashMap`实例都是可以访问的。

注解controller通常不直接使用`FlashMap`*译注，擦，你在不早说，早说我就不翻这一段了*，而是在`@RequestMapping`方法接受一个`RedirectAttributes `类型参数，使用该参数解决重定向场景中的falsh attribute。Flash attributes added via RedirectAttributes are automatically propagated to the "output" FlashMap. Similarly after the redirect attributes from the "input" FlashMap are automatically added to the Model of the controller serving the target URL.

```text
**Matching requests to flash attributes**

The concept of flash attributes exists in many other Web frameworks and has proven to be exposed sometimes to concurrency issues. This is because by definition flash attributes are to be stored until the next request. However the very "next" request may not be the intended recipient but another asynchronous request (e.g. polling or resource requests) in which case the flash attributes are removed too early.

To reduce the possibility of such issues, RedirectView automatically "stamps" FlashMap instances with the path and query parameters of the target redirect URL. In turn the default FlashMapManager matches that information to incoming requests when looking up the "input" FlashMap.

This does not eliminate the possibility of a concurrency issue entirely but nevertheless reduces it greatly with information that is already available in the redirect URL. Therefore the use of flash attributes is recommended mainly for redirect scenarios .
```

<h3 id='mvc-uri-building'>构建URIs</h3>
Spring MVC provides a mechanism for building and encoding a URI using UriComponentsBuilder and UriComponents.
Spring MVC提供了两个类,`UriComponentsBuilder`和`UriComponents`，用于构建和编码。

看样例如何构建URI template String
```java
UriComponents uriComponents = UriComponentsBuilder.fromUriString(
        "http://example.com/hotels/{hotel}/bookings/{booking}").build();

URI uri = uriComponents.expand("42", "21").encode().toUri();
```
注意`UriComponents `是不可变类，如果需要,`expand()` 和`encode()`会返回新的实例。
也可以使用URI 组件完成展开和编码的工作
```java
UriComponents uriComponents = UriComponentsBuilder.newInstance()
        .scheme("http").host("example.com").path("/hotels/{hotel}/bookings/{booking}").build()
        .expand("42", "21")
        .encode();
```

在Servlet环境中，`ServletUriComponentsBuilder `子类提供静态工厂方法用于从Servlet request中拷贝出可用的URL信息。

```java
HttpServletRequest request = ...

// 重用 host, scheme, port, path and query string
// 替换 the "accountId" query param

ServletUriComponentsBuilder ucb = ServletUriComponentsBuilder.fromRequest(request)
        .replaceQueryParam("accountId", "{id}").build()
        .expand("123")
        .encode();
```

或者，也可以从RUL中拷贝一组可用信息，包括上下文路径
```java
// 重用 host, port and context path
// 附加 "/accounts" 到path中

ServletUriComponentsBuilder ucb = ServletUriComponentsBuilder.fromContextPath(request)
        .path("/accounts").build()
```

如果`DispatcherServlet `是通过name映射（比如，`/main/*`)，也可获取映射的字面值。
```java
// Re-use host, port, context path
// Append the literal part of the servlet mapping to the path
// Append "/accounts" to the path

ServletUriComponentsBuilder ucb = ServletUriComponentsBuilder.fromServletMapping(request)
        .path("/accounts").build()
```

<h4 id="mvc-links-to-controllers">为Controller和方法构建URI</h4>
Spring MVC提供了另外一种机制用于构建和编码URI，在应用内定义用于链接到Controller 和方法。

给定Controller
```java
@Controller
@RequestMapping("/hotels/{hotel}")
public class BookingController {

    @RequestMapping("/bookings/{booking}")
    public String getBooking(@PathVariable Long booking) {

    // ...

}
```
使用`MvcUriComponentsBuilder`，前面的样例就变成了现在这个样子
```java
UriComponents uriComponents = MvcUriComponentsBuilder
    .fromMethodName(BookingController.class, "getBooking",21).buildAndExpand(42);

URI uri = uriComponents.encode().toUri();
```
`MvcUriComponentsBuilder `也可以创建 "mock Controllers"模拟controller，因此相对于实际的ControllerAPI来说，可以通过编码来创建URI
```java
UriComponents uriComponents = MvcUriComponentsBuilder
    .fromMethodCall(on(BookingController.class).getBooking(21)).buildAndExpand(42);

URI uri = uriComponents.encode().toUri()
```

<h4 id='mvc-links-to-controllers-from-views'>根据视图构建URI访问 controller和方法</h4>
It is also useful to build links to annotated controllers from views (e.g. JSP). This can be done through a method on MvcUriComponentsBuilder which refers to mappings by name called fromMappingName.

As of 4.1 every @RequestMapping is assigned a default name based on the capital letters of the class and the full method name. For example, the method getFoo in class FooController is assigned the name "FC#getFoo". This naming strategy is pluggable by implementing HandlerMethodMappingNamingStrategy and configuring it on your RequestMappingHandlerMapping. Furthermore the @RequestMapping annotation includes a name attribute that can be used to override the default strategy.

[Note]
The assigned request mapping names are logged at TRACE level on startup.
The Spring JSP tag library provides a function called mvcUrl that can be used to prepare links to controller methods based on this mechanism.

For example given:

@RequestMapping("/people/{id}/addresses")
public class MyController {

    @RequestMapping("/{country}")
    public HttpEntity getAddress(@PathVariable String country) { ... }
}
The following JSP code can prepare a link:

<%@ taglib uri="http://www.springframework.org/tags" prefix="s" %>
...
<a href="${s:mvcUrl('PC#getPerson').arg(0,'US')

<h3 id='mvc-localeresolver'>Using locales</h3>
Most parts of Spring’s architecture support internationalization, just as the Spring web MVC framework does. DispatcherServlet enables you to automatically resolve messages using the client’s locale. This is done with LocaleResolver objects.

When a request comes in, the DispatcherServlet looks for a locale resolver, and if it finds one it tries to use it to set the locale. Using the RequestContext.getLocale() method, you can always retrieve the locale that was resolved by the locale resolver.

In addition to automatic locale resolution, you can also attach an interceptor to the handler mapping (see Section 17.4.1, “Intercepting requests with a HandlerInterceptor” for more information on handler mapping interceptors) to change the locale under specific circumstances, for example, based on a parameter in the request.

Locale resolvers and interceptors are defined in the org.springframework.web.servlet.i18n package and are configured in your application context in the normal way. Here is a selection of the locale resolvers included in Spring.


<h4 id='mvc-timezone>Obtaining Time Zone Information</h4>
In addition to obtaining the client’s locale, it is often useful to know their time zone. The LocaleContextResolver interface offers an extension to LocaleResolver that allows resolvers to provide a richer LocaleContext, which may include time zone information.

When available, the user’s TimeZone can be obtained using the RequestContext.getTimeZone() method. Time zone information will automatically be used by Date/Time Converter and Formatter objects registered with Spring’s ConversionService.

<h4 id='mvc-localeresolver-acceptheader'>AcceptHeaderLocaleResolver</h4>
This locale resolver inspects the accept-language header in the request that was sent by the client (e.g., a web browser). Usually this header field contains the locale of the client’s operating system. Note that this resolver does not support time zone information.

<h4 id='mvc-localeresolver-cookie'>CookieLocaleResolver</h4>
This locale resolver inspects a Cookie that might exist on the client to see if a Locale or TimeZone is specified. If so, it uses the specified details. Using the properties of this locale resolver, you can specify the name of the cookie as well as the maximum age. Find below an example of defining a CookieLocaleResolver.

Table 17.4. CookieLocaleResolver properties

Property | Default | Description
-------- | ------- | -------------
cookieName | classname + LOCALE | The name of the cookie
cookieMaxAge | Integer.MAX_INT | The maximum time a cookie will stay persistent on the client. If -1 is specified, the cookie will not be persisted; it will only be available until the client shuts down their browser.
cookiePath | / | Limits the visibility of the cookie to a certain part of your site. When cookiePath is specified, the cookie will only be visible to that path and the paths below it.

<h4 id='mvc-localeresolver-session'>SessionLocaleResolver</h4>
The SessionLocaleResolver allows you to retrieve Locale and TimeZone from the session that might be associated with the user’s request.

<h4 id='mvc-localeresolver-interceptor'>LocaleChangeInterceptor</h4>
You can enable changing of locales by adding the LocaleChangeInterceptor to one of the handler mappings (see Section 17.4, “Handler mappings”). It will detect a parameter in the request and change the locale. It calls setLocale() on the LocaleResolver that also exists in the context. The following example shows that calls to all *.view resources containing a parameter named siteLanguage will now change the locale. So, for example, a request for the following URL, http://www.sf.net/home.view?siteLanguage=nl will change the site language to Dutch.
```xml
<bean id="localeChangeInterceptor"
        class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor">
    <property name="paramName" value="siteLanguage"/>
</bean>

<bean id="localeResolver"
        class="org.springframework.web.servlet.i18n.CookieLocaleResolver"/>

<bean id="urlMapping"
        class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="localeChangeInterceptor"/>
        </list>
    </property>
    <property name="mappings">
        <value>/**/*.view=someController</value>
    </property>
</bean>
```

<h3 id='mvc-themeresolver'>Using themes</h3>

<h4 id='mvc-themeresolver-introduction'>主题概述</h4>
spring MVC可以设置主题，因此增强用户体验。主题是一组静态资源，是css和image，他们会影响应用的视觉效果。

<h4 id='mvc-themeresolver-defining'>定义themes</h4>
To use themes in your web application, you must set up an implementation of the org.springframework.ui.context.ThemeSource interface. The WebApplicationContext interface extends ThemeSource but delegates its responsibilities to a dedicated implementation. By default the delegate will be an org.springframework.ui.context.support.ResourceBundleThemeSource implementation that loads properties files from the root of the classpath. To use a custom ThemeSource implementation or to configure the base name prefix of the ResourceBundleThemeSource, you can register a bean in the application context with the reserved name themeSource. The web application context automatically detects a bean with that name and uses it.

When using the ResourceBundleThemeSource, a theme is defined in a simple properties file. The properties file lists the resources that make up the theme. Here is an example:

styleSheet=/themes/cool/style.css
background=/themes/cool/img/coolBg.jpg
The keys of the properties are the names that refer to the themed elements from view code. For a JSP, you typically do this using the spring:theme custom tag, which is very similar to the spring:message tag. The following JSP fragment uses the theme defined in the previous example to customize the look and feel:
```xml
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<html>
    <head>
        <link rel="stylesheet" href="<spring:theme code='styleSheet'/>" type="text/css"/>
    </head>
    <body style="background=<spring:theme code='background'/>">
        ...
    </body>
</html>
```
By default, the ResourceBundleThemeSource uses an empty base name prefix. As a result, the properties files are loaded from the root of the classpath. Thus you would put the cool.properties theme definition in a directory at the root of the classpath, for example, in /WEB-INF/classes. The ResourceBundleThemeSource uses the standard Java resource bundle loading mechanism, allowing for full internationalization of themes. For example, we could have a /WEB-INF/classes/cool_nl.properties that references a special background image with Dutch text on it.

<h4 id='mvc-themeresolver-resolving'>Theme resolvers</h4>
After you define themes, as in the preceding section, you decide which theme to use. The DispatcherServlet will look for a bean named themeResolver to find out which ThemeResolver implementation to use. A theme resolver works in much the same way as a LocaleResolver. It detects the theme to use for a particular request and can also alter the request’s theme. The following theme resolvers are provided by Spring:

Table 17.5. ThemeResolver implementations
Class | 描述
------ | ------
`FixedThemeResolver` | Selects a fixed theme, set using the defaultThemeName property.
`SessionThemeResolver` | The theme is maintained in the user’s HTTP session. It only needs to be set once for each session, but is not persisted between sessions.
`CookieThemeResolver` |  The selected theme is stored in a cookie on the client.

Spring also provides a ThemeChangeInterceptor that allows theme changes on every request with a simple request parameter.

<h3 id='mvc-multipart'>Spring对multipart支持(文件上传)</h3>

<h4 id='mvc-multipart-introduction'>简介</h4>
spring内置支持multipart，在应用中支持处理文件上传。使用插件`MultipartResolver`对象开启对multipart支持，他们定义在`org.springframework.web.multipart`包。Spring 提供了一个基于`Commons FileUpload`的`MultipartResolver `实现，也提供了一个基于Sevlet3.0multipart request 解析。

默认情况下，Spring 没有multipart 处理，因为一些开发者要自己处理multiparts。若要在Spring中开启multipart处理，得在应用上下文中增加一个multipart resolver解析器。每个request都会被检查是否包含multipart。如果为发现 multipart，request继续。如果发现了multipart,在应用上下文中声明的`MultipartResolver `就会工作。然后，request中multipart的属性处理和其他的request的属性处理就相同了。

<h4 id='mvc-multipart-resolver-commons'>使用基于 Commons FileUpload的MultipartResolver </h4>
下面展示如何使用`CommonsMultipartResolver`:
```java

<bean id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">

    <!-- one of the properties available; the maximum file size in bytes -->
    <property name="maxUploadSize" value="100000"/>

</bean>
```
CommonsMultipartResolver, you need to use commons-fileupload.jar.
当然了，这是需要第三方jar包的，上例中的`CommonsMultipartResolver`，依赖`commons-fileupload.jar`。

When the Spring DispatcherServlet detects a multi-part request, it activates the resolver that has been declared in your context and hands over the request. The resolver then wraps the current HttpServletRequest into a MultipartHttpServletRequest that supports multipart file uploads. Using the MultipartHttpServletRequest, you can get information about the multiparts contained by this request and actually get access to the multipart files themselves in your controllers.  
Spring `DispatcherServlet`解析出有一个`multi-part`request, Spring会激活已经在上下文中声明的resolver解析器，并处理request。resolver则会包裹当前`HttpServletRequest`为`MultipartHttpServletRequest`，`MultipartHttpServletRequest`支持multipart 文件上传。使用`MultipartHttpServletRequest`，可以在controller中获取关于multiparts的源信息和访问multipart文件本身。

<h4 id='mvc-multipart-resolver-standard'>在Servlet3.0中使用MultipartResolver </h4>
为了基于multipart解析 使用Servlet3.0，得在`web.xml`中的`DispatcherServlet`中配置`multipart-config`部分，或者编程式的使用`javax.servlet.MultipartConfigElement`，或者在自定义Servlet类中使用`javax.servlet.annotation.MultipartConfig`注解。可以配置最大文件尺寸、存储位置，该配置是应用于Serlvet类级别，因为Servlet3.0不允许这些设置有MultipartResolver设置。

一旦Servlet 3.0 multipart解析通过上述任一中方式开启，就可以使用`StandartServletMultipartResolver`。
```xml
<bean id="multipartResolver"
        class="org.springframework.web.multipart.support.StandardServletMultipartResolver">
</bean>
```

<h4 id='mvc-multipart-forms'>在form中处理文件上传</h4>
request处理就像其他的request一样，首先创建一个form，具有file input的form是允许用户上传文件的form。设置form属性`enctype="multipart/form-data"` ，让浏览器知道如何对multipart request进行编码处理：
```html
<html>
    <head>
        <title>Upload a file please</title>
    </head>
    <body>
        <h1>Please upload a file</h1>
        <form method="post" action="/form" enctype="multipart/form-data">
            <input type="text" name="name"/>
            <input type="file" name="file"/>
            <input type="submit"/>
        </form>
    </body>
</html>
``` 

接下来创建一个controller，它可以处理文件上传，该controller和常规注解`@Controller`非常像，区别就是在方法参数中使用`MultipartHttpServletRequest`或者`MultipartFile`:
```java
@Controller
public class FileUploadController {

    @RequestMapping(value = "/form", method = RequestMethod.POST)
    public String handleFormUpload(@RequestParam("name") String name,
            @RequestParam("file") MultipartFile file) {

        if (!file.isEmpty()) {
            byte[] bytes = file.getBytes();
            // store the bytes somewhere
            return "redirect:uploadSuccess";
        }

        return "redirect:uploadFailure";
    }

}
``` 

注意`@RequestParam`方法参数是如何与form表单中的input元素映射的。本例中，没有使用`byte[]`，但是在实际使用中可以将其存储于数据库、存储于文件系统等等。

若是使用Servlet 3.0 multipart解析，则可以使用 `javax.servlet.http.Part`作为方法参数:
```java
@Controller
public class FileUploadController {

    @RequestMapping(value = "/form", method = RequestMethod.POST)
    public String handleFormUpload(@RequestParam("name") String name,
            @RequestParam("file") Part file) {

        InputStream inputStream = file.getInputStream();
        // store bytes from uploaded file somewhere

        return "redirect:uploadSuccess";
    }

}
```

<h4 id='mvc-multipart-forms-non-browsers'>处理来自于脚本客户端的文件上传request</h4>
也可以在非浏览器客户端提交Multipart request。上面样例中的配置页同样适用。当然了，不想浏览器那样提交files和简单的form域，脚本客户端也可以发送指定content type的更复杂的数据，比如multipart request中包含一个file和一部分JSON格式的数据:
```text
POST /someUrl
Content-Type: multipart/mixed

--edt7Tfrdusa7r3lNQc79vXuhIIMlatb7PQg7Vp
Content-Disposition: form-data; name="meta-data"
Content-Type: application/json; charset=UTF-8
Content-Transfer-Encoding: 8bit

{
	"name": "value"
}
--edt7Tfrdusa7r3lNQc79vXuhIIMlatb7PQg7Vp
Content-Disposition: form-data; name="file-data"; filename="file.properties"
Content-Type: text/xml
Content-Transfer-Encoding: 8bit
... File Data ...
``` 
在Controller方法中使用`@RequestParam("meta-data") String metadata`访问名为"meta-data"部分的数据。当然了，你可能更喜欢从request部分的中的JSON格式数据中解析出强类型对象，非常简单，使用`HttpMessageConverter` 将`@RequestBody`转换非multipart request为目标对象即可。

为了达到此目的，得使用`@RequestPart`注解替代`@RequestParam`注解。它允许你通过`HttpMessageConverter`将指定的multipart解析为该multipart request的header中的`Content-type`类型数据。
```java
@RequestMapping(value="/someUrl", method = RequestMethod.POST)
public String onSubmit(@RequestPart("meta-data") MetaData metadata,
        @RequestPart("file-data") MultipartFile file) {

    // ...

}
``` 
注意`MultipartFile `方法参数可以通过`@requestParam`或者`@RequestPart`访问。然而，`@RequestPart("meta-data") MetaData`方法参数是根据`'Content-Type'`将会读取为JSON，并且通过`MappingJackson2HttpMessageConverter`完成转换。

<h3 id='mvc-exceptionhandlers'>处理异常</h3>
<h4 id='mvc-exceptionhandlers-resolver'>HandlerExceptionResolver</h4>
Spring 的`HandlerExceptionResolver `实现是处理controller执行期间发生的异常。`HandlerExceptionResolver `和exception mapping异常映射有些像，异常映射可以在`web.xml`中定义。当然了，spring也提供了更灵活的方式完成映射。比如，在抛出异常时提供执行handler的信息。因此，使用编程式的异常处理，在request forward跳转之前，会有更多的选项可供操作（与Servlet 设置异常映射相比，具有相同的执行结果）。`HandlerExceptionResolver`接口中只有一个方法,`ModelAndView resolveException( HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)`，该方法返回一个`ModelAndView`，除了提供该接口以外，Spring还提供了`SimpleMappingExceptionResolver `，还有`@ExceptionHandler`注解，他们都可用于异常处理。`SimpleMappingExceptionResolver `可以将制定异常映射到一个view 视图名上，通过异常的class name映射。这个方式等价于Servlet API中的异常映射功能,但是Spring的异常映射可以实现更细粒度的来自不同的handler的异常映射。`@ExceptionHandler`注解可用于可能抛出异常的方法上。`@ExceptionHandler`方法，可能是在`@Controller`类内定义，应用于本类的方法，也可以在`@ControllerAdvice`类内定义，应用于多个`@Controller`类。下面详细讲述。

<h4 id='mvc-ann-exceptionhandler'>@ExceptionHandler</h4>
`HandlerExceptionResolver `接口和`SimpleMappingExceptionResolver `实现类允许request跳转之前根据异常跳转到指定视图，同时提供一些配置可选项来完成一些逻辑。有些情况下，特别是基于`@ResponseBody`注解的方法，这种异常映射机制，是非常方便的操作响应：设置response的status,将错误内容写到response中。 

`@ExceptionHandler`若是声明在`Controller`类中，则会处理本类及子类`@RequestMapping`方法爆出的异常。也可以在`ControllerAdvice`类中声明`@ExceptionHandler`方法，该方法将会处理多个`Controller`类中的的`@RequestMapping`。下例展示 controller本地`@ExceptionHandler`方法
```java
@Controller
public class SimpleController {

    // 此处省略10k个@RequestMapping方法 ...

    @ExceptionHandler(IOException.class)
    public ResponseEntity<String> handleIOException(IOException ex) {
        // 构造responseEntity
        return responseEntity;
    }

}
```

`@ExceptionHandler`的value也可以设置为一个异常类型数组。如果抛出的异常，匹配数组元素之一，`@ExceptionHandler`注解的方法则被调用。如果注解为设置value，则会使用参数列表中的异常类型作为匹配源。
和`@RequestMapping`方法很像，`@ExceptionHandler`的参数和返回值都是非常灵活的。比如，方法中可直接访问`HttpServletRequest `和`PortletRequest `，当然他们得在相应的环境中才行，`HttpServletRequest `要在Servlet 环境中，`PortletRequest `得在Portlet 环境中。返回值可以是`String`，被解析为视图名;可以是`ModelAndView`对象；可以是`ResponseEntity`，或者可以使用`@ResponseBody`注解，通过message converter消息转换器将返回值转换后写入到response流中。

<h4 id='mvc-ann-rest-spring-mvc-exceptions'>处理标准的Spring MVC 异常</h4>
Spring MVC可抛出非常丰富的异常。`SimpleMappingExceptionResolver `可以非常容易的将异常映射到默认错误视图。当然了，有些客户端需要的不是错误视图，而是解析response中的错误code编码。Spring异常可以被解析为客户端错误(4xx)或者服务器端错误(5xx)。
`DefaultHandlerExceptionResolver `将Spring MVC 异常翻译成指定的错误编码。该类是默认注册的。下面列出有此类解析的异常与错误码对应关系

Exception | HTTP Status Code
---------- | -------------------
`BindException` |  400 (错误请求)
`ConversionNotSupportedException` | 500 (服务器内部错误)
`HttpMediaTypeNotAcceptableException` | 406 (Not Acceptable)
`HttpMediaTypeNotSupportedException` | 415 (Unsupported Media Type)
`HttpMessageNotReadableException` | 400 (Bad Request)
`HttpMessageNotWritableException` | 500 (Internal Server Error)
`HttpRequestMethodNotSupportedException` | 405 (Method Not Allowed)
`MethodArgumentNotValidException` | 400 (Bad Request)
`MissingServletRequestParameterException` | 400 (Bad Request)
`MissingServletRequestPartException` | 400 (Bad Request)
`NoHandlerFoundException` | 404 (Not Found)
`NoSuchRequestHandlingMethodException` | 404 (Not Found)
`TypeMismatchException` | 400 (Bad Request)

`DefaultHandlerExceptionResolver `透明的设置response的status，用户无需关心。当然了，它会终止向response body写入的错误信息。可以提前准备ModelAndView，然后到视图中去渲染错误信息，通过配置`ContentNegotiatingViewResolver`,`MappingJackson2JsonView`等等。还可以用`@ExceptionHandler`替代。

若是喜欢通过`@ExceptionHandler`向response写入错误内容则可继承`ResponseEntityExceptionHandler `。通过`@ControllerAdvice`提供`@ExceptionHandler`方法可以非常方便去处理标准Spring MVC异常，并返回`ResponseEntity`。这样就允许自定义response，并将错误信息写入response。详情参看`ResponseEntityExceptionHandler `javadocs

<h4 id='mvc-ann-annotated-exceptions'>使用@Response注解业务异常</h4>
对于业务异常，可以使用`@ResponseStatus`注解。当有异常抛出时，`ResponseStatusExceptionResolver `会根据response的status去处理。`DispatcherServlet `默认会注册`ResponseStatusExceptionResolver`使其可用。

<h4 id='mvc-ann-customer-servlet-container-error-page'>自定义默认的Servlet容器Error页面</h4> 
当response的status被设置为错误码，并且response的body为空，通常Servlet 容器会渲染一个HTML将错误内容格式化输出。在`web.xml`中声明一个`<error-page>`，就是给容器配置了默认的错误页。直到Servlet 3以前,该元素必须被映射到一个指定状态码或者是异常类型。Servlet 3以后则不需要映射。
```xml
<error-page>
    <location>/error</location>
</error-page>
```

新机器测试
Note that the actual location for the error page can be a JSP page or some other URL within the container including one handled through an @Controller method: 
注意，错误页可能是个JSP，也可能是其他URL路径，比如`@Controller`的方法: 在controller中，错误码和错误信息，通过request attribute在`HttpServletResponse`中读写。
```java
@Controller
public class ErrorController {

    @RequestMapping(value="/error", produces="application/json")
    @ResponseBody
    public Map<String, Object> handle(HttpServletRequest request) {

        Map<String, Object> map = new HashMap<String, Object>();
        map.put("status", request.getAttribute("javax.servlet.error.status_code"));
        map.put("reason", request.getAttribute("javax.servlet.error.message"));

        return map;
    }

}
```    
或者是在JSP中
```JSP
<%@ page contentType="application/json" pageEncoding="UTF-8"%>
{
    status:<%=request.getAttribute("javax.servlet.error.status_code") %>,
    reason:<%=request.getAttribute("javax.servlet.error.message") %>
}
```    

<h3 id='mvc-web-security'>WEB Secuity</h3>
The Spring Security project provides features to protect web applications from malicious exploits. Check out the reference documentation in the sections on "CSRF protection", "Security Response Headers", and also "Spring MVC Integration". Note that using Spring Security to secure the application is not necessarily required for all features. For example CSRF protection can be added simply by adding the CsrfFilter and CsrfRequestDataValueProcessor to your configuration. See the Spring MVC Showcase for an example.

Another option is to use a framework dedicated to Web Security. HDIV is one such framework and integrates with Spring MVC.

<h3 id='mvc-coc'>惯例优于配置</h3>
开发过程中，有很多规范和惯例，他们都有各自的成因。Spring MVC支持“惯例优于配置”。“惯例优于配置”是指，如果你遵守命名规范，那么将省去大量的配置文件，比如：handler mapping,view resolvers,`ModelAndView`实例等等。 对于快速开发原型非常有利，也有利于编码规范。

<h4 id='mvc-coc-ccnhm'>Controller命名规范ControllerClassNameHandlerMapping </h4>
`ControllerClassNameHandlerMapping `类是一个`HandlerMapping`实现， 用于处理request url和`Controller`实例之间的映射处理。

考虑下面的`Controller`类，注意类名
```java
public class ViewShoppingCartController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) {
        // 实现不重要
    }

}
```
Here is a snippet from the corresponding Spring Web MVC configuration file:
下面是相应的配置文件
```xml
<bean class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping"/>

<bean id="viewShoppingCart" class="x.y.z.ViewShoppingCartController">
    <!-- 注入依赖... -->
</bean>
```

`ControllerClassNameHandlerMapping`在应用上下文中发现`Controller`bean,将其类名中的 `Controller`解掉，剩余的部分作为其能处理的映射的路径配置，因此 `ViewShoppingCartController`将处理`/viewshoppingcart*`路径的请求。

接下来看看更多的例子，然后总结一下主要内容和中心思想（主义，URL中都是小写的，`Controller`类是驼峰式的）

* `WelcomController` 处理 `/welcome*` URL
* `HomeController`处理 ·`/home*` URL
* `IndexController`处理`/index*` URL
* `RegisterController` 处理 `/register*` URL

`MultiActionController`处理类的情况中，映射生成规则略复杂些。假设，下列`Controller`是`MultiActionController`。

* `AdminController` 映射`/admin/*` URL
* `CatalogController`映射`/catalog/*` URL

由此看出，若是按照管理命名`Controller`实现为`xxxController`,` ControllerClassNameHandlerMapping`将使你省去大部分配置。

`ControllerClassNameHandlerMapping`继承自`AbstractHandlermapping`，因此你可定义`HandlerIntercepter`实例等各种handlerMapping组件。

<h4 id='mvc-coc-modelmap'>The Model ModelMap (ModelAndView)</h4>  
`ModelMap`类是一个`Map`,可携带Objects,这些对象用于在`View`中展示。假如现在有一个`Controller`实现，注意附加到`ModelAndView`中的对象并为设置相关联的名字。

```java
public class DisplayShoppingCartController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) {

        List cartItems = // get a List of CartItem objects
        User user = // get the User doing the shopping

        ModelAndView mav = new ModelAndView("displayShoppingCart"); <-- the logical view name

        mav.addObject(cartItems); <-- look ma看么, no name没有name, just the object只有对象
        mav.addObject(user); <-- and again ma又来啦啦!

        return mav;
    }
}
```   

`ModelAndView `使用了一个`ModelMap`类，`ModelMap`类是一个自定义`Map`实现，该类在增加对象时会自动为对象生成一个key。生成的key由增加的对象决定，比如`User`类的对象，将使用短类名作为对象的key。参考以下生成规则：

* `x.y.User`实例，`user`
* `x.y.Registration`实例，`registration`
* `x.y.Foo`实例，`foo`
* `java.util.HashMap`,`hashMap`。`hashMap`不太直观，也许你需要明确指定其name
* `null`将导致`IllegalArgumentException`。如果要增加的对象可能是`null`，那么应该为其指定一个name(也就是key)

```text
**没有自动复杂数据处理**

Spring Web MVC的惯例优于配置不支持复杂数据处理。也就是说，当你给`ModelAndView`增加一个`Person`的`List`的时候，不会生成`people`的name

这是根据“最少意外原则”决定的。
```

*  `x.y.User[]`数组，包含0个或者多个`x.y.User`元素，产生的key为`UserList`
*  `x.y.Foo[]`数组，0个或者多个， `fooList`
*  `java.util.ArrayList`类型List包含1个以上元素，产生`userList`
*  `java.util.HashSet`,0个或者多个元素，产生`fooList`
*  空`java.Util.Arraylist`(0个元素)，调用`addObject(...)`时候，将不会有任何操作，也就是说该空List不能增加到模型中。

<h4 id='mvc-coc-r2vnt'>视图 - RequestToViewNameTranslator</h4> 
`RequestToViewNameTranslator `接口的作用是，在没有逻辑视图名被明确指定时，它将提供一个逻辑视图名。它有一个实现，`DefaultRequestToViewNameTranslator`类。

`DefaultRequestToViewNameTranslator` 使用方法如下

```java
public class RegistrationController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) {
        // 处理request...
        ModelAndView mav = new ModelAndView();
        // 如果需要将数据写入model...
        return mav;
        // 注意，没有设置View ，也没设置逻辑视图名
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- this bean with the well known name generates view names for us -->
    <bean id="viewNameTranslator"
            class="org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator"/>

    <bean class="x.y.RegistrationController">
        <!-- inject dependencies as necessary -->
    </bean>

    <!-- maps request URLs to Controller names -->
    <bean class="org.springframework.web.servlet.mvc.support.ControllerClassNameHandlerMapping"/>

    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```
`handleRequest(..)`方法未设置View以及逻辑视图名，然后就返回了`ModelAndView`。`DefaultRequestToViewNameTranslator ` 将会根据request的URL生成一个逻辑视图名。上例中，如果`RegistrationController`联结了`ControllerClassNameHandlerMapping`，那么`http://localhost/registration.html`的请求将会生成一个`registration`的逻辑视图名，该视图名将会通过`InternalResourceViewResolver`被解析到视图`/WEB-INF/jsp/registration.jsp`。

![](http://docs.spring.io/autorepo/docs/spring/current/spring-framework-reference/html/images/tip.png)
> 不需要明确定义`DefaultRequestToViewNameTranslator `，可通过Spring Web MVC `DispatcherServlet`去实例化`DefaultRequestToViewNameTranslator`。

更多详情参看 javadocs

<h3 id='mvc-etag'> ETag support</h3>
An ETag (entity tag) is an HTTP response header returned by an HTTP/1.1 compliant web server used to determine change in content at a given URL. It can be considered to be the more sophisticated successor to the Last-Modified header. When a server returns a representation with an ETag header, the client can use this header in subsequent GETs, in an If-None-Match header. If the content has not changed, the server returns 304: Not Modified.

Support for ETags is provided by the Servlet filter ShallowEtagHeaderFilter. It is a plain Servlet Filter, and thus can be used in combination with any web framework. The ShallowEtagHeaderFilter filter creates so-called shallow ETags (as opposed to deep ETags, more about that later).The filter caches the content of the rendered JSP (or other content), generates an MD5 hash over that, and returns that as an ETag header in the response. The next time a client sends a request for the same resource, it uses that hash as the If-None-Match value. The filter detects this, renders the view again, and compares the two hashes. If they are equal, a 304 is returned. This filter will not save processing power, as the view is still rendered. The only thing it saves is bandwidth, as the rendered response is not sent back over the wire.

You configure the ShallowEtagHeaderFilter in web.xml:

```xml
<filter>
    <filter-name>etagFilter</filter-name>
    <filter-class>org.springframework.web.filter.ShallowEtagHeaderFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>etagFilter</filter-name>
    <servlet-name>petclinic</servlet-name>
</filter-mapping>
```

<h3 id='mvc-container-config'>使用代码实例化容器</h3>
In a Servlet 3.0+ environment, you have the option of configuring the Servlet container programmatically as an alternative or in combination with a web.xml file. Below is an example of registering a DispatcherServlet:

在Servlet3.0环境中，还可以编程式的配置Servlet容器，或者是与`web.xml`结合使用。下面看样例，如何注册`DispatcherServlet`。

```java
import org.springframework.web.WebApplicationInitializer;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        XmlWebApplicationContext appContext = new XmlWebApplicationContext();
        appContext.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");

        ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet(appContext));
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }

}
```

`WebApplicationInitializer `是Spring MVC提供的一个接口，用于确保你的实现探测并且自动初始化Servlet 3容器。`WebApplicationInitializer `有一个抽象基类实现`AbstractDispatcherServletInitializer `,实现该抽象类非常简单，继承它，实现servlet映射方法和`DispatcherServlet`配置的定位方法即可

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return null;
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] { MyWebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }

}
```

上面的样例中使用了基于java的Spring配置。若是使用基于xml的spring配置， 则是继承`AbstractDispatcherServletInitializer`   

```java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }

    @Override
    protected WebApplicationContext createServletApplicationContext() {
        XmlWebApplicationContext cxt = new XmlWebApplicationContext();
        cxt.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");
        return cxt;
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }

}
```
`AbstractDispatcherServletInitializer ` 也提供了非常方便的方法用于增加 `Filter`实例并将其自动映射到`DispatcherServlet`

```java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    // ...

    @Override
    protected Filter[] getServletFilters() {
        return new Filter[] { new HiddenHttpMethodFilter(), new CharacterEncodingFilter() };
    }

}
```
`AbstractDispatcherServletInitializer `中的方法`isAsyncSupported `是唯一能开启`DispatcherServlet`支持的异步的地方。默认他是`true`。

<h3 id='mvc-config'>配置Spring MVC</h3>
Section 17.2.1, “Special Bean Types In the WebApplicationContext” and Section 17.2.2, “Default DispatcherServlet Configuration” explained about Spring MVC’s special beans and the default implementations used by the DispatcherServlet. In this section you’ll learn about two additional ways of configuring Spring MVC. Namely the MVC Java config and the MVC XML namespace.

[Section 17.2.1, “Special Bean Types In the WebApplicationContext](#mvc-servlet-special-bean-types)” and [Section 17.2.2, “Default DispatcherServlet Configuration”](#mvc-servlet-config)   这两章解释了Spring MVC中的特殊beans 和用于`DispatcherServlet`的默认实现。本章讲解，另外两种方式配置Spring MVC。分别是基于MVC的Java配置和MVC XML配置

基于Java配置和基于XML配置都提供了想死的默认配置，用于覆盖`DispatcherServlet`默认实现。 之所以这么做，是为了减少重复工作，因为多数应用都使用相同的配置，也是为了提供更高层级的抽象配置，降低学习曲线，使用户仅需要很少的关于配置的背景知识就可以使用Spring MVC了。

MVC基于java的配置比基于XML的配置要更容易些，也有更细粒度的控制。

<h4 id='mvc-config-enable'>开启MVC Java配置、开启MVC XML配置</h4>
在`@Configuration` 注解的类上使用注解`@EnableWebMvc`即可开启MVC Java配置。
```java
@Configuration
@EnableWebMvc
public class WebConfig {

}
``` 

若是使用XML达到同样的效果,得在DispatchServlet上下文中使用`mvc:annotation-driven`元素（如果没有DispatchServlet 环境则是在root context根环境中）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <mvc:annotation-driven />

</beans>
```  

The above registers a RequestMappingHandlerMapping, a RequestMappingHandlerAdapter, and an ExceptionHandlerExceptionResolver (among others) in support of processing requests with annotated controller methods using annotations such as @RequestMapping, @ExceptionHandler, and others.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                