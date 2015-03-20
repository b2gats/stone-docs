<h2 id='mvc'>Web MVC framework框架</h2>
<h3 id='mvc-introduction'>Spring Web MVC框架简介</h3>
Spring MVC的核心是`DispatcherServlet`，该类作用非常多，分发请求处理，配置处理器映射，处理视图view，本地化，时间区域和主题，也支持文件上传。默认的处理器依赖于`@Controller`和`RequestMapping`注解，提供了大量的灵活的处理方法。spring3.0中就介绍过了，`@Controller`机制，可通过SpringＭＶＣ提供的`@PathVariable`注解和其他功能,创建`RESTful`web网站和应用。
```text
在spring MVC中一个关键的设计就是“开闭原则”，即对扩展开放对修改闭合原则。Spring 一些核心类的方法
是`final`方法。开发者不能重写这些方法，来增加自己的行为。这些都不是随意决定，而是意在符合“开闭原则”。


预知此原则详情，参看 Seth Ladd(这应该就是马丁大叔)等人的
*Expert Spring Web MVC and Web Flow*，尤其是要看“A Look At Desig” 章节，在第一版的117页

* [Bob Martin, The Open-Closed Principle (PDF)](http://www.objectmentor.com/resources/articles/ocp.pdf)

```
