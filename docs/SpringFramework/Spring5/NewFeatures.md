# Spring5新特性和增强功能

**Spring 5**是第一个主要版本，距Spring Framework 4.0差不多四年了。在这段时间内，大多数增强功能都是在[Spring boot](/docs/SpringFramework/Springboot/Introduction.md)项目中完成的。在本文中，我们将快速介绍Spring 5.0发行版中的一些令人兴奋的功能。



## 基准升级

要构建和运行Spring 5应用程序，您将最低需要[JDK 8](/docs/SpringFramework/Java8/Introduction.md)和Java EE7。不再支持以前的JDK和Java EE版本。详细来说，Java EE 7包括

- Servlet 3.1
- [JMS 2.0](/docs/Springboot/Spring-boot-jmstemplate-activemq.md/)
- [JPA 2.1](https://howtodoinjava.com/jpa-tutorials-and-examples/)
- [JAX-RS 2.0](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/)
- [Bean Validation 1.1](https://howtodoinjava.com/spring/spring-mvc/spring-bean-validation-example-with-jsr-303-annotations/)

与Java基准相似，许多其他框架的基准也有所变化。例如

- Hibernate 5
- Jackson 2.6
- EhCache 2.10
- [JUnit 5](https://howtodoinjava.com/junit-5-tutorial/)
- Tiles 3

另外，记下各种服务器的最低支持版本。

- Tomcat 8.5+
- Jetty 9.4+
- WildFly 10+
- Netty 4.1+
- Undertow 1.4+



## JDK 9运行时兼容性

Spring 5发行版与[JDK 9](https://howtodoinjava.com/java9/java9-new-features-enhancements/)发行日期非常吻合。目标是在JDK 9的GA之后立即使Spring Framework 5.0成为GA。Spring 5.0的release版本已经在classpath和[modulepath](https://howtodoinjava.com/java9/java-9-modules-tutorial/)上支持Java 9 。

您可以期望在GA版本中获得良好的JDK 9支持。



## JDK 8特性的使用

在Spring 4.3之前，JDK的基准版本为6。因此Spring 4必须支持Java 6、7和8。为了保持向后兼容性，Spring框架并没有适应Java 8自身带来的许多新功能，例如[Lambda编程](https://howtodoinjava.com/java8/complete-lambda-expressions-tutorial-in-java/)。

Spring 5具有基准版本8，因此它也使用Java 8和9的许多新功能。例如：

1. Spring核心接口中的Java 8 [默认方法](https://howtodoinjava.com/java8/default-methods-in-java-8/)
2. 基于Java 8反射增强的内部代码改进
3. 在框架代码中使用函数式编程lambda和[流](https://howtodoinjava.com/java8/java-8-tutorial-streams-by-examples/)



## 响应式编程支持

[响应式编程](https://howtodoinjava.com/rxjava/rxjava-2-0-tutorial/)是Spring Framework 5.0的最重要功能之一。响应式编程提供了另一种编程风格，专注于构建对事件做出反应的应用程序。Spring Framework 5包含响应式流（用于定义响应式语言的中性语言尝试）和[Reactor](https://projectreactor.io/)（Spring Pivotal团队提供的响应式流的Java实现），用于其自身的响应式使用以及其许多核心API。

Spring Web Reactive位于新`spring-web-reactive`模块中，位于该模块中现有且流行的Spring Web MVC的`spring-webmvc`。请注意，在Spring 5中，传统的Spring MVC可以在任何Servlet 3.1堆栈上运行，包括Java EE 7服务器。



## 功能性网络框架

在响应功能的基础上，Spring 5还提供了一个功能性的Web框架。它提供了使用功能性编程风格定义端点的功能。该框架引入了两个基本组件：`HandlerFunction`和`RouterFunction`。

`HandlerFunction`代表处理传入的请求并生成响应的功能。`RouterFunction`用作`@RequestMapping`注释的替代方法。它用于将传入的请求路由到处理程序函数。例如：

```java
RouterFunction<String> route =
    route(GET("/hello-world"),
    request -> Response.ok().body(fromObject("Hello World")));
```



## Kotlin支持

[Kotlin](https://kotlinlang.org/)是一种静态类型的JVM语言，它使代码具有表达力，简短性和可读性。Spring framework 5.0对Kotlin有很好的支持。



## 功能删减

随着Java，Java EE和其他一些框架的基准版本的增加，Spring Framework 5删除了对一些框架的支持。例如

- Portlet
- Velocity
- JasperReports
- XMLBeans
- JDO
- Guava

学习愉快！

参考：[链接](https://github.com/spring-projects/spring-framework/wiki/What's-New-in-the-Spring-Framework#whats-new-in-spring-framework-5x)