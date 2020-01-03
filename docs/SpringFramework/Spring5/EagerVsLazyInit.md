# Spring – 懒加载

默认情况下，Spring的application context会在应用程序启动过程中积极创建并初始化所有"[单例作用域](https://howtodoinjava.com/design-patterns/creational/singleton-design-pattern-in-java/)"的Bean。在大多数情况下，它有助于早期发现Bean配置问题。但有时，由于项目要求不同，您可能需要标记部分或全部Bean进行延迟初始化。

Spring提供了两种简单的方法来配置bean的延迟初始化，这取决于您采用的是哪种配置，即[基于XML的配置](https://howtodoinjava.com/spring5/core/spring-bean-xml-config/)或[基于Java的配置](https://howtodoinjava.com/spring5/core/spring-bean-java-config/)。



## 1. 懒加载 beans – Java 配置

#### 1.1. 懒加载特定bean

要只懒加载特定的bean，请在Java配置中使用`@Lazy`注解和`@Bean`注解。

AppConfig.java

```java
import org.springframework.context.annotation.Lazy;
 
@Configuration
public class AppConfig {
     
    @Lazy
    @Bean
    public EmployeeManager employeeManager() {
        return new EmployeeManagerImpl();
    }
     
}
```

#### 1.2. 懒加载所有bean

要懒加载所有bean，请在Java配置中使用`@Lazy`注解和`@Bean`注解。

AppConfig.java

```java
import org.springframework.context.annotation.Lazy;
 
@Lazy
@Configuration
public class AppConfig {
     
    @Bean
    public EmployeeManager employeeManager() {
        return new EmployeeManagerImpl();
    }
     
}
```

#### 1.3. 注入懒加载的bean

通常，使用**@Autowired**注解将bean注入到其他组件中。在这种情况下，我们必须在两个地方都使用`@Lazy`注解：

- 您要懒加载的bean定义
- @Autowired注解注入的位置

```java
@Lazy
@Service
public class EmployeeManagerImpl implements EmployeeManager {
  //
}
```

```java
@Controller
public class EmployeeController {
 
    @Lazy
    @Autowired
    EmployeeManager employeeManager;
}
```

**如果没有在以上两个地方都使用@Lazy注解，它将无法正常工作。**



## 2. XML配置懒加载bean

#### 2.1. 懒加载特定bean

要为特定bean启用懒加载，请在bean配置xml文件中的bean定义上使用**lazy-init =“ true”**属性。

beans.xml

```xml
<beans>
 
<bean id="employeeManager" class="com.howtodoinjava.spring.service.impl.EmployeeManagerImpl"
    lazy-init="true"/>
 
<beans>
```

#### 2.2. 全局懒加载所有bean

要为所有bean启用懒加载，请在bean配置xml文件中的标记上使用**default-lazy-init =“ true”**属性`beans`。

beans.xml

```xml
<beans default-lazy-init="true">
 
<bean id="employeeManager" class="com.howtodoinjava.spring.service.impl.EmployeeManagerImpl" />
 
<beans>
```

## 3. Spring 懒加载 demo

让我们看一下bean的代码，我们正在尝试延迟加载。

```java
@Lazy
@Service
public class EmployeeManagerImpl implements EmployeeManager {
 
    @Override
    public Employee create() {
        Employee emp =  new Employee();
        emp.setId(1);
        emp.setName("Lokesh");
        return emp;
    }
     
    @PostConstruct
    public void onInit(){
        System.out.println("EmployeeManagerImpl Bean is Created !!");
    }
}
```

我已经放置了`@PostConstruct`注释来检测何时创建bean。

让我们初始化应用程序上下文：

#### 3.1. 没有懒加载

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
 
import com.howtodoinjava.spring.model.Employee;
import com.howtodoinjava.spring.service.EmployeeManager;
 
public class Main 
{
    public static void main( String[] args )
    {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
         
        System.out.println("Bean Factory Initialized !!");
         
        EmployeeManager empManager = ctx.getBean(EmployeeManager.class);
        Employee emp = empManager.create();
         
        System.out.println(emp);
    }
}
```

程序输出：

```java
EmployeeManagerImpl Bean is Created !!
Bean Factory Initialized !!
Employee [id=1, name=Lokesh]
```

在这里，在bean工厂完全初始化之前，已创建并初始化了第一个bean。

#### 3.2. 有懒加载

```
Bean Factory Initialized !!
EmployeeManagerImpl Bean is Created !!
Employee [id=1, name=Lokesh]
```

启用bean延迟加载后，bean工厂首先进行完全初始化。稍后，当我们请求`EmployeeManager`bean时，factory然后创建了实例并返回了它。

学习愉快！

[源代码下载](https://github.com/lokeshgupta1981/spring-core/tree/master/src/main/java/com/howtodoinjava/core/demo/beans)