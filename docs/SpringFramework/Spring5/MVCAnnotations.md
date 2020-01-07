# Spring @PostMapping示例– @GetMapping示例

了解如何用`@Controller`创建[Spring MVC的](https://howtodoinjava.com/spring-mvc-tutorial/)controller，并用请求映射注解来映射请求，如`@RequestMapping`，`@GetMapping`，`@PostMapping`，`@PutMapping`，`@DeleteMapping`和`@PatchMapping`。



## 1.Spring Controller

#### 1.1.@Controller注解

Spring MVC提供了基于注解的方法，您无需扩展任何基类即可表达**请求映射**，请求**输入参数**，**异常处理**等。`@Controller`是将一个类标记为请求处理程序的注解。

HomeController.java

```java
package com.howtodoinjava.web;
 
@Controller
public class HomeController 
{
    @GetMapping("/")
    public String homeInit(Model model) {
        return "home";
    }
}
```

在上面的代码中，`HomeController`类充当请求控制器。它的`homeInit()`方法将处理对URI的所有传入请求`"/"`。它接受一个 `Model`并返回视图名称`home`。此视图名称由配置的**视图解析器解析**。

#### 1.2.启用组件扫描

要让Spring扫描和配置带`@Controller`注解的类，您需要在存储控制器的软件包上配置组件扫描。

WebConfig.java

```java
@Configuration
@ComponentScan("com.howtodoinjava.web")
public class WebConfig {
    //Other configurations
}
 
//or
 
<context:component-scan base-package="com.howtodoinjava.web"/>
```

> 阅读更多：[Spring MVC示例](https://howtodoinjava.com/spring5/webmvc/spring5-mvc-hibernate5-example/)



## 2. Spring @GetMapping示例

**@GetMapping**是`@RequestMapping`注解的专用版本，可作为`@RequestMapping(method = RequestMethod.GET)`的快捷方式。`@GetMapping`带注释的方法处理`GET`与给定URI表达式匹配的HTTP 请求。例如

```java
@GetMapping("/home")
public String homeInit(Model model) {
    return "home";
}
 
@GetMapping("/members/{id}")
public String getMembers(Model model) {
    return "member";
}
```



## 3. Spring @PostMapping示例

**@PostMapping**是`@RequestMapping`注解的专用版本，可作为`@RequestMapping(method = RequestMethod.POST)`的快捷方式。`@PostMapping`带注释的方法处理`POST`与给定URI表达式匹配的HTTP 请求。例如

```java
@PostMapping(path = "/members", consumes = "application/json", produces = "application/json")
public void addMember(@RequestBody Member member) {
    //code
}
```

`consumes`和`produces`属性也支持否定的表达-例如`!text/plain`意味着不同于`text/plain`的任何媒体类型。

## 4.共享的类级别属性

您可以`produces`在类级别声明共享属性。在类级别使用时，方法级别的`produces`属性将覆盖类级别的声明。

HomeController.java

```java
package com.howtodoinjava.web;
 
@Controller
@RequestMapping(path = "/", produces = MediaType.APPLICATION_JSON_VALUE)
public class HomeController 
{
    @PostMapping(path = "/members")
    public void addMemberV1(@RequestBody Member member) {
        //code
    }
 
    @PostMapping(path = "/members", produces = MediaType.APPLICATION_XML_VALUE)
    public void addMemberV2(@RequestBody Member member) {
        //code
    }
}
```

在上面的示例中，`addMemberV1()`方法以默认媒体类型（即`application/json`）生成内容。第二种方法`addMemberV2()`会覆盖`produces`属性，并将生成`application/xml`类型的内容。

## 5. @PostMapping与@RequestMapping

1. 如上所述，**@ PostMapping**注解是`@RequestMapping`处理**HTTP POST**请求的注解的一种特殊版本。

2. @PostMapping充当@RequestMapping(method = RequestMethod.POST)的快捷方式。

   PostMapping.java

   ```java
   @Target({ java.lang.annotation.ElementType.METHOD })
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   @RequestMapping(method = { RequestMethod.POST })
   public @interface PostMapping 
   {
       //code
   }
   ```

3. 在两个注释中，传递URL信息都是相同的。

让我们看看使用简单代码的**PostMapping和@RequestMapping**注解**之间**的**区别**。

```
@RequestMapping(value = "/employees", method = RequestMethod.POST)  //1
 
@PostMapping("/employees")  //2
```



## 6.总结

Spring MVC使编写请求处理程序controlloer类和方法变得非常容易。只需添加一些注解（如**@GetMapping**和**@PostMapping）**，并将该类放在组件扫描可以找到它们的位置，并在Web应用程序上下文中对其进行配置。

在类级别创建属性也非常容易，以便所有处理程序方法默认都继承它们，并可以在需要时覆盖它们。

同样的方式，你可以使用其他的请求映射注解如`@PutMapping`，`@DeleteMapping`和`@PatchMapping`。

学习愉快！