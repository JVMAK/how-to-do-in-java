# 注解配置Bean

学习使用Java通过任何独立应用程序的注解来配置创建spring bean。我们将学习如何在不扫描[组件注解](https://howtodoinjava.com/spring/spring-core/how-to-use-spring-component-repository-service-and-controller-annotations/)和使用`@Bean`注解的情况下创建它。



## 1. 带有组件扫描的注解配置

使用组件扫描创建bean可以分两个步骤完成。

#### 1.1. 用相应的组件注解为bean注释

我们将酌情使用以下四个注释之一。

- `@Component`
- `@Repository`
- `@Service`
- `@Controller`

> 阅读更多：[Spring组件注释](https://howtodoinjava.com/spring-core/how-to-use-spring-component-repository-service-and-controller-annotations/)

EmployeeManagerImpl.java

```java
package com.howtodoinjava.spring.service.impl;
 
import org.springframework.stereotype.Service;
import com.howtodoinjava.spring.model.Employee;
import com.howtodoinjava.spring.service.EmployeeManager;
 
@Service
public class EmployeeManagerImpl implements EmployeeManager {
 
    @Override
    public Employee create() {
        Employee emp =  new Employee();
        emp.setId(1);
        emp.setName("Lokesh");
        return emp;
    }
}
```

#### 1.2. 在`@ComponentScan`注释中包含Bean Package

AppConfig.java

```java
@Configuration
@ComponentScan(basePackages = "com.howtodoinjava.spring.service")
public class AppConfig {
     
}
```

#### 1.3. Demo

```java
package com.howtodoinjava.spring;
 
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import com.howtodoinjava.spring.model.Employee;
import com.howtodoinjava.spring.service.EmployeeManager;
 
public class Main 
{
    public static void main( String[] args )
    {
        //Method 1
        //ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
         
        //Method 2
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
        ctx.register(AppConfig.class);
        ctx.refresh();
         
        EmployeeManager empManager = ctx.getBean(EmployeeManager.class);
        Employee emp = empManager.create();
         
        System.out.println(emp);
    }
}
```

程序输出:

```
Employee [id=1, name=Lokesh]
```



## 2. 使用@Bean 和@Configuration 注解

要使用`@Bean`注解创建spring应用程序内容，请使用以下步骤：

#### 2.1. 创建Java Bean类（无需注解或任何其他操作）

EmployeeManagerImpl.java

```java
public class EmployeeManagerImpl implements EmployeeManager {
 
    @Override
    public Employee create() {
        Employee emp =  new Employee();
        emp.setId(1);
        emp.setName("Lokesh");
        return emp;
    }
}
```

#### 2.2. 在配置类中创建@Bean注解方法

AppConfig.java

```java
package com.howtodoinjava.spring;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import com.howtodoinjava.spring.service.EmployeeManager;
import com.howtodoinjava.spring.service.impl.EmployeeManagerImpl;
 
@Configuration
public class AppConfig {
     
    @Bean
    public EmployeeManager employeeManager() {
        return new EmployeeManagerImpl();
    }
     
}
```

#### 2.3. Demo

```java
package com.howtodoinjava.spring;
 
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import com.howtodoinjava.spring.model.Employee;
import com.howtodoinjava.spring.service.EmployeeManager;
 
public class Main 
{
    public static void main( String[] args )
    {
        //Method 1
        //ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
         
        //Method 2
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
        ctx.register(AppConfig.class);
        ctx.refresh();
         
        EmployeeManager empManager = ctx.getBean(EmployeeManager.class);
        Employee emp = empManager.create();
         
        System.out.println(emp);
    }
}
```

程序输出:

```java
Employee [id=1, name=Lokesh]
```

这就是使用纯Java代码和注解创建Spring bean的两种简单方法。

学习愉快！

[源代码下载](https://github.com/lokeshgupta1981/spring-core/tree/master/src/main/java/com/howtodoinjava/core/demo/beans)

