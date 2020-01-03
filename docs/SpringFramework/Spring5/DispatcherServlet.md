# Spring DispatcherServlet –它是如何工作的？

学习有关Spring的`DispatcherServlet`类的作用，通过示例了解如何配置它。



## 1.什么是Spring DispatcherServlet

`DispatcherServlet`充当基于Spring的Web应用程序的[前端控制器](https://en.wikipedia.org/wiki/Front_controller)。它提供了一种用于请求处理的机制，其中实际工作由可配置的处理组件执行。它继承自[javax.servlet.http.HttpServlet](https://tomcat.apache.org/tomcat-7.0-doc/servletapi/javax/servlet/http/HttpServlet.html)，通常在`web.xml`文件中进行配置。

Web应用程序可以定义任意数量的`DispatcherServlet`实例。每个servlet将在其自己的命名空间中运行，并使用映射，处理程序等加载其自己的应用程序上下文。只有[ContextLoaderListener](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/context/ContextLoaderListener.html)加载的根应用程序上下文（如果有）将被共享。在大多数情况下，应用程序只有一个`DispatcherServlet`具有上下文根URL的应用程序`(/)`，也就是说，所有进入该域的请求都将由它处理。

`DispatcherServlet` 使用Spring配置类发现请求映射，视图解析，异常处理等所需的处理组件。



## 2.如何使用WebApplicationContext

让我们了解`DispatcherServlet`在内部如何工作？在基于Spring的应用程序中，我们的应用程序对象位于对象容器中。该容器创建对象和对象之间的关联，并管理它们的完整生命周期。这些容器对象称为Bean，并且在Spring世界中，该容器称为**应用程序上下文**（class `ApplicationContext`）。

`WebApplicationContext`是`ApplicationContext`的一个直接拓展。它具有网络意识，`ApplicationContext`具有Servlet上下文信息。当`DispatcherServlet`被加载，它寻找的bean配置文件，`WebApplicationContext`并对其进行初始化。

通过访问Servlet上下文，任何实现`ServletConextAware`接口的spring bean 都可以访问`ServletContext`实例并用它做很多事情。例如，它可以获取上下文初始化参数，获取上下文根信息以及获取Web应用程序文件夹内的资源位置。



## 3. DispatcherServlet XML配置

让我们看看典型的`DispatcherServlet`声明和初始化是什么样的。

web.xml

```xml
<web-app>
 
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
 
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/dispatcher-servlet-context.xml</param-value>
  </context-param>
 
  <servlet>
    <servlet-name>dispatcher-servlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value></param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
 
  <servlet-mapping>
    <servlet-name>dispatcher-servlet</servlet-name>
    <url-pattern>/*</url-pattern>
  </servlet-mapping>
 
</web-app>
```

在以上代码中，`dispatcher-servlet-context.xml`file将包含可用于的所有bean定义和关联`DispatcherServlet`。这些bean定义将覆盖在全局范围内用相同名称定义的任何bean的定义。例如

applicationContext.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-2.5.xsd">
 
    <bean id="viewResolver"
        class="org.springframework.web.servlet.view.InternalResourceViewResolver" >
        <property name="prefix">
            <value>/WEB-INF/views/</value>
        </property>
        <property name="suffix">
            <value>.jsp</value>
        </property>
    </bean>
 
</beans>
```



## 4. DispatcherServlet Java配置

从Servlet 3.0开始，除了`web.xml`文件中的声明式配置外，还可以通过实现或扩展Spring提供的一下三个支持类之一来以编程方式配置DispatcherServlet：

- `WebAppInitializer` 接口
- `AbstractDispatcherServletInitializer` 抽象类
- `AbstractAnnotationConfigDispatcherServletInitializer` 抽象类

#### 4.1. WebAppInitializer示例

在下面的类中，`WebApplicationInitializer`确保`ApplicationInitializer`检测到该类`SpringServletContainerInitializer`（该类本身会自动引导）并用于初始化任何Servlet 3容器。

**Spring Boot dispatcherservlet映射**的示例。

ApplicationInitializer.java

```java
public class ApplicationInitializer implements WebApplicationInitializer 
{
    @Override
    public void onStartup(ServletContext servletContext) throws ServletException 
    {
        XmlWebApplicationContext appContext = new XmlWebApplicationContext();
        appContext.setConfigLocation("/WEB-INF/dispatcher-servlet-context.xml");
 
        ServletRegistration.Dynamic registration = servletContext
                    .addServlet("rootDispatcher", new DispatcherServlet(appContext));
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }
}
```

#### 4.2. 完整的基于Java的初始化

ApplicationInitializer.java

```java
public class ApplicationInitializer implements WebApplicationInitializer 
{
    @Override
    public void onStartup(ServletContext container) 
    {
        // Create the 'root' Spring application context
        AnnotationConfigWebApplicationContext rootContext = new AnnotationConfigWebApplicationContext();
        rootContext.register(AppConfig.class);
 
        // Manage the lifecycle of the root application context
        container.addListener(new ContextLoaderListener(rootContext));
 
        // Create the dispatcher servlet's Spring application context
        AnnotationConfigWebApplicationContext dispatcherContext = new AnnotationConfigWebApplicationContext();
        dispatcherContext.register(DispatcherConfig.class);
 
        ServletRegistration.Dynamic dispatcher = container.addServlet("dispatcher", 
                        new DispatcherServlet(dispatcherContext));
        dispatcher.setLoadOnStartup(1);
        dispatcher.addMapping("/");
    }
}
```

在上面的代码中，`AppConfig`和`DispatcherConfig`类定义了将在Web应用程序上下文中的spring托管bean。

#### 4.3.AbstractDispatcherServletInitializer示例

这是在servlet上下文`WebApplicationInitializer`中注册的实现的基类`DispatcherServlet`。

ApplicationInitializer.java

```java
public class ApplicationInitializer extends AbstractDispatcherServletInitializer {
 
    @Override
    protected WebApplicationContext createRootApplicationContext() {
            return null;
    }
 
    @Override
    protected WebApplicationContext createServletApplicationContext() {
            XmlWebApplicationContext cxt = new XmlWebApplicationContext();
            cxt.setConfigLocation("/WEB-INF/dispatcher-servlet-context.xml");
            return cxt;
    }
 
    @Override
    protected String[] getServletMappings() {
            return new String[] { "/" };
    }
 
    //Register filters
    @Override
    protected Filter[] getServletFilters() {
        return new Filter[] { new HiddenHttpMethodFilter(), new CharacterEncodingFilter() };
    }
}
```

请注意，如果您需要自定义`DispatcherServlet`，则可以重写该`createDispatcherServlet()`方法。

#### 4.4.AbstractAnnotationConfigDispatcherServletInitializer示例

该类可以扩展`AbstractDispatcherServletInitializer`，并且隐式地执行少量操作，否则您可能需要自己做。另一个好处是，您现在可以使用Spring提供的便利类，而无需手动配置`DispatcherServlet`and / or `ContextLoaderListener`。

对于使用基于Java的Spring配置的应用程序，这是首选方法。它使您能够启动servlet应用程序上下文以及根应用程序上下文。

AppInitializer.java

```java
public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
 
   @Override
   protected Class<?>[] getRootConfigClasses() {
      return new Class[] { RootConfig.class };
   }
 
   @Override
   protected Class<?>[] getServletConfigClasses() {
      return new Class[] { WebMvcConfig.class };
   }
 
   @Override
   protected String[] getServletMappings() {
      return new String[] { "/" };
   }
}
```

这里的`RootConfig`和`WebMvcConfig`类用于在root和servlet上下文范围内配置bean。

> 阅读更多：[Spring 5 MVC示例](https://howtodoinjava.com/spring5/webmvc/spring5-mvc-hibernate5-example/)



## 5.支持DispatcherServlet的Bean

接收到Web请求后，请`DispatcherServlet`执行一组用于请求处理的操作。为此，它使用了一组支持bean。下表列出了这些默认配置的Bean及其职责：

| Bean                       | 职责范围                                                     |
| -------------------------- | ------------------------------------------------------------ |
| `HandlerMapping`           | 将传入的Web请求映射到处理程序以及预处理器和后处理器          |
| `HandlerAdapter`           | 调用用于解析参数和依赖项的处理程序，例如用于URL映射的控制器方法端点的带注释的参数 |
| `HandlerExceptionResolver` | 允许以编程方式处理异常并将异常映射到视图                     |
| `ViewResolver`             | 解析逻辑视图名称以查看实例                                   |
| `LocaleResolver`           | 解决客户的语言环境以实现国际化                               |
| `LocaleContextResolver`    | `LocaleResolver`带有时区信息的扩展功能                       |
| `ThemeResolver`            | 解决了在您的应用中配置的主题，以增强用户体验                 |
| `MultipartResolver`        | 处理多部分文件上传作为HTTP请求的一部分                       |
| `FlashMapManager`          | 管理FlashMap实例，这些实例在彼此重定向的请求之间存储临时Flash属性 |

如果要更改任何bean的任何特定行为，则需要重写它。



## 6. Spring DispatcherServlet示例

为了演示的使用`DispatcherServlet`，我编写了一个非常简单的应用程序，该应用程序仅配置了调度程序servlet并覆盖了视图解析器bean。

#### 6.1.项目结构

![Spring5 MVC项目结构](https://howtodoinjava.com/wp-content/uploads/2017/10/Spring5-MVC-Project-Structure.png)Spring5 MVC项目结构

#### 6.2.AppInitializer.java

```java
package com.howtodoinjava.demo.spring.config;
 
public class AppInitializer extends
    AbstractAnnotationConfigDispatcherServletInitializer {
 
   @Override
   protected Class<?>[] getRootConfigClasses() {
      return new Class[] { };
   }
 
   @Override
   protected Class<?>[] getServletConfigClasses() {
      return new Class[] { WebMvcConfig.class };
   }
 
   @Override
   protected String[] getServletMappings() {
      return new String[] { "/" };
   }
}
```

#### 6.3.WebMvcConfig.java

```java
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = { "com.howtodoinjava.demo.spring"})
public class WebMvcConfig implements WebMvcConfigurer {
 
   @Bean
   public InternalResourceViewResolver resolver() {
      InternalResourceViewResolver resolver = new InternalResourceViewResolver();
      resolver.setViewClass(JstlView.class);
      resolver.setPrefix("/WEB-INF/views/");
      resolver.setSuffix(".jsp");
      return resolver;
   }
}
```

#### 6.4.HomeController.java

```java
@Controller
public class HomeController 
{
    @GetMapping("/")
    public String homeInit(Locale locale, Model model) {
        return "home";
    }
}
```

#### home.jsp

```html
<html>
<head>
    <title>Spring 5 Web MVC Example</title>
</head>
<body>
    <h1>HowToDoInJava.com</h1>
    <h2>Spring 5 Web MVC DispatcherServlet Example</h2>
</body>
</html>
```

#### 6.5.pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd;
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.howtodoinjava.spring5.mvc</groupId>
    <artifactId>spring5-webmvc-demo</artifactId>
    <packaging>war</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring5-webmvc-demo Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <properties>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <spring.version>5.2.0.RELEASE</spring.version>
        <jstl.version>1.2.1</jstl.version>
        <tld.version>1.1.2</tld.version>
        <servlets.version>3.1.0</servlets.version>
        <jsp.version>2.3.1</jsp.version>
    </properties>
    <dependencies>
        <!-- Spring MVC Dependency -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
         
        <!-- JSTL Dependency -->
        <dependency>
            <groupId>javax.servlet.jsp.jstl</groupId>
            <artifactId>javax.servlet.jsp.jstl-api</artifactId>
            <version>${jstl.version}</version>
        </dependency>
         
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>${tld.version}</version>
        </dependency>
 
        <!-- Servlet Dependency -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>${servlets.version}</version>
            <scope>provided</scope>
        </dependency>
 
        <!-- JSP Dependency -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>${jsp.version}</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    <build>
        <sourceDirectory>src/main/java</sourceDirectory>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.5.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <path>/</path>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

#### 6.6.运行应用程序

要运行该应用程序，请执行maven目标：**tomcat7：run**。现在``在浏览器中打开http://localhost:8080。

![Spring DispatcherServlet演示屏幕](https://howtodoinjava.com/wp-content/uploads/2017/10/Spring-DispatcherServlet-Demo-Screen.png)Spring DispatcherServlet演示页面

[源代码下载](https://github.com/lokeshgupta1981/spring-webmvc)

学习愉快！