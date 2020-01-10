# Spring教程

本页列出了所有Spring教程和HowToDoInJava.com上可用的示例。每次都会更新此页面，我将在Spring框架上写任何文章。随意建议您想阅读的主题。

![春季教程](https://howtodoinjava.com/wp-content/uploads/2013/01/logo_spring_258x1511.png)

## Spring Core教程

- [Spring bean范围](https://howtodoinjava.com/spring/spring-core/spring-bean-scopes/)
  
可以在五个范围中创建spring容器中的bean。单例，原型，请求，会话和全局会话。
  
- [Spring bean生命周期回调方法](https://howtodoinjava.com/spring-core/spring-bean-life-cycle/)

  Spring框架提供了以下4种方法来控制bean的生命周期事件：

  1. InitializingBean和DisposableBean回调接口
  2. 其他用于特定行为的Aware接口
  3. Bean配置文件中的自定义init（）和destroy（）方法
  4. @PostConstruct和@PreDestroy注解

- [Spring bean自动装配](https://howtodoinjava.com/2013/05/08/spring-beans-autowiring-concepts/)
  
在Spring框架中，遵循配置文件中的bean依赖关系是一个很好的做法，但是spring容器也能够自动装配协作bean之间的关系。这意味着可以通过检查BeanFactory的内容来自动让Spring为您的bean解决协作者（其他bean）。
  
- [通过类型自动装配Bean](https://howtodoinjava.com/spring/spring-core/spring-bean-autowire-bytype/)
  
如果容器中恰好有一个属性类型的bean，则通过类型自动装配允许属性自动装配。如果存在多个，则将引发致命异常，这表明您不能通过类型对bean使用自动装配。
  
- [通过名称自动装配Bean](https://howtodoinjava.com/spring/spring-core/spring-bean-autowire-byname/)
  
按名称自动装配允许按属性自动装配属性，以便它检查容器并查找名称与需要自动装配的属性完全相同的bean。
  
- [通过构造函数自动装配Bean](https://howtodoinjava.com/spring/spring-core/spring-autowiring-by-constructor/)
  
通过构造函数自动装配与通过类型自动装配相似，但适用于构造函数参数。在启用自动装配的bean中，它将查找构造函数参数的类类型，然后按类型对所有构造函数参数进行自动装配。
  
- [如何使用FactoryBean创建bean](https://howtodoinjava.com/spring/spring-core/how-to-create-beans-using-spring-factorybean/)
  
工厂bean是充当IoC容器中创建其他bean的工厂的bean。从概念上讲，工厂bean与工厂方法非常相似，但是它是特定于Spring的bean，可以在构造bean的过程中由Spring IoC容器识别，并且可以被容器用来实例化其他bean。了解如何使用Spring FactoryBean创建bean。
  
- [如何使用静态工厂方法创建bean](https://howtodoinjava.com/spring/spring-core/spring-create-beans-using-static-factory-method/)
  
如果要在Spring中通过调用静态工厂方法创建bean，其目的是将对象创建过程封装在静态方法中，则可以使用factory-method属性。
  
- [如何使用util：constant从final static字段引用中声明Bean](https://howtodoinjava.com/spring/spring-core/spring-declare-beans-from-final-static-field-references-using-util-constant/)
  
如果您在某个Bean中具有final static字段，并且希望将这些引用用作应用程序上下文文件中的Bean注入到另一个Bean中，则可以使用<util：constant >标签。
  
- [如何将外部资源/文件加载到Spring上下文中？](https://howtodoinjava.com/spring/spring-core/how-to-load-external-resources-files-into-spring-context/)
  
很多时候，您希望将外部资源或文件（例如，文本文件，XML文件，属性文件或图像文件）加载到Spring应用程序上下文中。Spring的资源加载器提供了统一的getResource（）方法，供您通过资源路径检索外部资源。
  
- [如何创建BeanPostProcessor](https://howtodoinjava.com/spring/spring-core/how-to-create-spring-bean-post-processors/)
  
BeanPostProcessor允许在bean初始化回调方法之前和之后进行其他处理。BeanPostProcessor的主要特征是它将一个接一个地处理IoC容器中的所有Bean实例，而不仅仅是单个Bean实例。学习使用BeanPostProcessor接口类创建此类后置处理器。
  
- [如何解析文本消息：ResourceBundleMessageSource示例](https://howtodoinjava.com/spring/spring-core/resolving-text-messages-in-spring-resourcebundlemessagesource-example/)
  
为了支持国际化的应用程序，它需要能够解决不同语言环境的文本消息的功能。Spring的应用程序上下文能够通过其键解析目标语言环境的文本消息。了解如何使用ResourceBundleMessageSource类支持i10n。
  
- [如何发布和监听应用程序事件](https://howtodoinjava.com/spring/spring-core/how-to-publish-and-listen-application-events-in-spring/)
  
有时在spring应用程序中，您可能希望增加监听特定事件的功能，以便可以按照应用程序逻辑处理这些事件。让我们学习如何在Spring应用程序中实现此发布和监听事件。
  
- [如何使用@ Component，@ Repository，@ Service和@Controller注解？](https://howtodoinjava.com/spring/spring-core/how-to-use-spring-component-repository-service-and-controller-annotations/)
  
在合适的地方使用`@Component`，`@Repository`，`@Service`和`@Controller注释启用自动组件扫描，Spring会自动导入beans放入容器中，这样你就不必使用XML明确定义它们。这些注解也称为构造型注解。
  
- [@Required Annotation和RequiredAnnotationBeanPostProcessor示例](https://howtodoinjava.com/spring/spring-core/spring-required-annotation-and-requiredannotationbeanpostprocessor-example/)
  
  在实际的应用程序中，您不会对检查上下文文件中配置的所有Bean属性感兴趣。相反，您只想检查是否仅在某些特定的Bean中设置了特定的属性集。Spring的依赖检查功能使用“dependency-check”属性，在这种情况下将无法为您提供帮助。因此要解决此问题，可以使用`@Required`注释。

## Spring框架最佳实践

- [编写Spring配置文件的13种最佳实践](https://howtodoinjava.com/spring/spring-core/13-best-practices-for-writing-spring-configuration-files/)
  
  在xml配置文件或注释中指定了Spring bean，依赖项以及bean所需的服务。但是，XML配置文件是冗长且更清晰的。如果未正确设计和编写，在大型项目中将变得非常难以管理。在本文中，我将向您展示编写Spring XML配置的13种最佳实践。

## 控制反转和依赖注入

- [控制反转（IoC）和依赖项注入（DI）模式](https://howtodoinjava.com/spring/spring-core/inversion-of-control-ioc-and-dependency-injection-di-patterns-in-spring-framework-and-related-interview-questions/)
  
  在传统编程中，业务逻辑流由静态分配给彼此的对象确定。在控制反转的情况下，流程依赖于由汇编程序实例化的对象图，并且通过抽象定义对象交互来使流程成为可能。绑定过程是通过依赖项注入实现的。阅读这篇文章以获取详细信息。

## Spring REST

- [使用Spring 3 MVC的REST APIs](https://howtodoinjava.com/spring/spring-restful/how-to-write-restful-webservices-using-spring-3-mvc/)
  
  Spring 3 mvc创建RESTful Web服务应用程序的步骤指南。这篇文章涵盖了所有需要的依赖项，应用的注解和有效的应用程序演示，您可以免费下载。
  
- [Spring REST Hello World XML示例](https://howtodoinjava.com/spring/spring-restful/spring-rest-hello-world-xml-example/)
  
  了解如何编写能够返回资源的XML表示形式的REST API。
  
- [Spring REST Hello World JSON示例](https://howtodoinjava.com/spring/spring-restful/spring-rest-hello-world-json-example/)
  
  了解如何编写能够返回资源的JSON表示形式的REST API。
  
- [Spring RESTFul Client– RestTemplate示例](https://howtodoinjava.com/spring/spring-restful/spring-restful-client-resttemplate-example/)
  
  学习构建RESTFul clinet以使用前面示例中编写的REST API。

## Spring ORM

- [Spring 3和hibernate集成教程与示例](https://howtodoinjava.com/spring/spring-orm/spring-3-and-hibernate-integration-tutorial-with-example/)
  
  本教程重点介绍将Spring 3框架与[Hibernate](https://howtodoinjava.com/hibernate-tutorials/)结合使用。我将展示这种集成的结果，我将展示作为这种集成的结果，基本的端到端应用程序流是什么样子的。
  
- [Spring 3.2.5.RELEASE和Hibernate 4集成示例](https://howtodoinjava.com/spring/spring-orm/spring-3-2-5-release-and-hibernate-4-integration-example-tutorial/)
  
  在前面的示例中，很多人都在努力解决maven依赖性。在此示例中，我在项目源代码本身中添加了所有必需的jar文件。另外，我已经将spring的版本从3.0.5升级到3.2.5.RELEASE。
  
- [Spring AbstractRoutingDataSource示例](https://howtodoinjava.com/spring/spring-orm/spring-3-2-5-abstractroutingdatasource-example/)
  
  如果您的设计允许基于某些条件的多个数据库，而这些条件可能会因每个用户请求而改变，那么AbstractRoutingDataSource是非常有用的功能。一个例子可以是数据库的使用。当用户属于某个语言环境时，可以使用特定的数据库；如果用户属于另一个语言环境，则可以切换到另一个语言环境。
  
- [通过Spring JDBC + JPA + HSQLDB使用SQL脚本](https://howtodoinjava.com/spring/spring-jdbc/using-sql-scripts-with-spring-jdbc-jpa-hsqldb/)
  
  在spring中使用自定义SQL脚本在应用程序启动时初始化数据库，并在其中填充适当的表和数据。

## Spring 整合

- [Spring 3与hornetq的独立集成](https://howtodoinjava.com/spring/spring-integration/spring-3-hornetq-standalone-integration-example/)
  
  HornetQ是一个开源项目，旨在构建多协议，可嵌入，非常高性能的集群异步消息传递系统。HornetQ具有很大的灵活性，可以通过一些现有的应用程序框架进行配置。在这篇文章中，我将演示在Spring 3中使用HornetQ。
  
- [Spring 4 + Struts 2 + Hibernate集成](https://howtodoinjava.com/struts-2/spring-4-struts-2-hibernate-integration-tutorial/)
  
  将Spring框架与Struts与Hibernate结合使用时，要牢记所有要点。
  
- [Spring 3与Maven的JSTL集成](https://howtodoinjava.com/maven/how-to-add-jstl-support-in-spring-3-using-maven/)
  
  如何使用maven构建工具向Spring 3添加JSTL支持。

## 杂项示例

- [Spring Email：JavaMailSender示例](https://howtodoinjava.com/spring/spring-core/send-email-with-spring-javamailsenderimpl-example/)
  
  一个简单的示例，展示了如何使用Spring框架发送电子邮件。

## Spring 单元测试

- [junit中的单元测试授权](https://howtodoinjava.com/junit/how-to-unit-test-spring-security-authentication-with-junit/)
  
  这篇文章的主要目的是描述以编程方式构建完全填充的身份验证对象，然后在单元测试中以及可能在应用程序代码本身中使用它的方法。

## Spring 事务

- [非公共方法上的Spring事务](https://howtodoinjava.com/spring/spring-transaction/spring-transactions-on-non-public-methods-with-load-time-weaving/)
  
  学习如何在非公共方法上的任何spring应用程序中应用事务（默认spring AOP只建议在IoC容器中声明的bean的公共方法）。使用这种技术，您可以管理非公共方法的事务，也可以管理任何方法在Spring IoC外部创建的对象中的事务。

## 引用

[Spring 3文档](https://docs.spring.io/spring/docs/3.0.x/spring-framework-reference/html/)



[Spring 4文档](https://docs.spring.io/spring/docs/4.0.x/spring-framework-reference/html/)