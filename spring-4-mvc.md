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

