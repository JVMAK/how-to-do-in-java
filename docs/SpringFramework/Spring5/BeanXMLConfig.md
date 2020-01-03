# XML配置Bean

在这个**spring bean XML配置示例中**，学习创建定义和创建spring bean以及在任何spring应用程序中填充应用程序上下文。本示例使用**xml config定义bean**。我们将使用maven管理*spring依赖关系，*并使用Eclipse构建和运行代码。

## 1. Spring maven 依赖

要创建能够创建和管理bean的spring应用程序上下文，我们至少需要三个maven依赖项，即**spring-core，spring-beans和spring-context**。

1. **Spring-core**模块具有与其他spring模块一起使用所需的最基本的类。

2. **Spring-beans**模块提供了`org.springframework.beans.factory.BeanFactory`所需的接口。

3. **Spring-context** 模块提供的`org.springframework.context.ApplicationContext`接口可启用其他功能，例如[消息资源](https://howtodoinjava.com/spring/spring-mvc/spring-mvc-internationalization-i18n-and-localization-i10n-example/)，[AOP](https://howtodoinjava.com/spring-aop-tutorial/)功能，特定类型的应用程序上下文和[bean生命周期](https://howtodoinjava.com/spring/spring-core/spring-bean-life-cycle/)事件监听器。

   pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd;
    <modelVersion>4.0.0</modelVersion>
 
    <groupId>com.howtodoinjava.spring.webmvc</groupId>
    <artifactId>SpringDemos</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
 
    <name>SpringDemos</name>
    <url>http://maven.apache.org</url>
 
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.version>5.2.0.RELEASE</spring.version>
    </properties>
 
    <dependencies>
        <!-- Spring Dependencies -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>
</project>
```

## 2. xml配置中的Spring bean定义

#### 2.1. bean定义的单个配置文件

您可以在单个xml文件中定义所有**spring bean**及其传递依赖项。该xml文件可用于**创建应用程序上下文**。

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context.xsd">
     
    <bean id="operations"    class="com.howtodoinjava.spring.beans.Operations"></bean>
    <bean id="employee"  class="com.howtodoinjava.spring.beans.Employee"></bean>
    <bean id="department"    class="com.howtodoinjava.spring.beans.Department"></bean>
     
</beans> 
```

#### 2.2. 在多个配置文件中定义bean并导入到主文件中

此方法在编写**模块化代码时**更有用。您可以在单独的xml文件中定义bean，然后将文件导入主xml文件。

employee.xml

```xml
<beans>
     
    <bean id="employee" class="com.howtodoinjava.spring.beans.Employee"></bean>
     
</beans> 

```

department.xml

```xml
<beans>
     
    <bean id="department" class="com.howtodoinjava.spring.beans.Department"></bean>
     
</beans> 
```

beans.xml

```xml-dtd
<?xml version="1.0" encoding="UTF-8"?>
<beans>
     
    <import resource="employee.xml"/>
    <import resource="department.xml"/>
     
    <bean id="operations" class="com.howtodoinjava.spring.beans.Operations"></bean>
     
</beans> 
```



## 3. Spring bean 示例

要**创建ApplicationContext**，我们可以使用从可用的它的一个具体实施的实现，如清单`ClassPathXmlApplicationContext`，`FileSystemXmlApplicationContext`，`StaticApplicationContext`，`XmlWebApplicationContext`等。

我们将需要将**Bean配置文件名作为**使用的类的**构造函数参数**传递。不要忘记将文件放在classpath或[resources文件夹中](https://howtodoinjava.com/java/io/read-file-from-resources-folder/)。

Main.java

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
 
public class XmlConfigExample 
{
    public static void main( String[] args )
    {
    @SuppressWarnings("resource")
    ApplicationContext ctx = new
                  ClassPathXmlApplicationContext( "com/howtodoinjava/core/demo/beans/beans.xml" );
         
        Employee employee = ctx.getBean(Employee.class);
          
        Department department = ctx.getBean(Department.class);
          
        Operations operations = ctx.getBean(Operations.class);
  
        System.out.println(department);
        System.out.println(employee);
  
        operations.helloWorld();
    }
}
```

程序输出:

```
Jan 02, 2018 3:10:27 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [beans.xml]
 
Jan 02, 2018 3:10:27 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [employee.xml]
 
Jan 02, 2018 3:10:27 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
INFO: Loading XML bean definitions from class path resource [department.xml]
 
Employee [id=0, name=null]
Department [id=0, name=null]
Hello World !!
```

## 4. 示例中使用的项目结构和其他类

#### 4.1. 项目结构

![Spring XML Config Project Structure](https://howtodoinjava.com/wp-content/uploads/2018/01/Spring-XML-Config-Project-Structure.png)Spring XML **Spring XML Config项目结构**

#### 4.2. Bean类

Employee.java

```java
@Getter
@Setter
@ToString
public class Employee 
{
    private long id;
    private String name;
}
```

Department.java

```java
@Getter
@Setter
@ToString
public class Department 
{
    private long id;
    private String name;
}
```

Operations.java

```java
public class Operations 
{
    public void helloWorld(){
        System.out.println("Hello World !!");
    }
}
```

学习愉快！

有关：

[Spring Java配置示例](https://howtodoinjava.com/spring5/core/spring-bean-java-config/)



[Spring Boot2配置示例](https://howtodoinjava.com/spring-boot2/springbootapplication-auto-configuration/)



[源代码下载](https://github.com/lokeshgupta1981/spring-core/tree/master/src/main/java/com/howtodoinjava/core/demo/beans)

