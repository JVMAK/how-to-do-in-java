# Spring CORS

**CORS**（*跨域资源共享*）允许网页从其他域向浏览器请求其他资源，例如字体，CSS或CDN的静态图像。CORS有助于将来自多个域的Web内容提供给通常具有[相同来源](https://en.wikipedia.org/wiki/Same-origin_policy)安全策略的浏览器。

在此示例中，我们将学习在方法级别和全局级别在[Spring MVC](https://howtodoinjava.com/spring-mvc-tutorial/)应用程序中**启用Spring CORS支持**。

> 阅读更多：[Java CORS过滤器示例](https://howtodoinjava.com/servlets/java-cors-filter-example/)



## 1. Spring CORS – @CrossOrigin的方法级别

Spring MVC提供`@CrossOrigin`注解。该注解将注解的方法或类型标记为允许跨源请求。

#### 1.1.Spring CORS允许所有

默认情况下，**@ CrossOrigin**允许所有来源，所有标头，注解中指定的[HTTP方法](https://restfulapi.net/http-methods/)`@RequestMapping`以及`maxAge`为30分钟的时间。

您可以通过为注解属性赋值来覆盖默认的CORS设置：

| 属性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `origins`          | 允许的来源清单。它的值放在`Access-Control-Allow-Origin`预检响应和实际响应的Header中。`"*" `表示允许所有来源。如果*未定义*，则允许所有源。 |
| `allowedHeaders`   | 可以在实际请求中使用的请求Header列表。在预检响应Header `Access-Control-Allow-Headers`中使用该值。`"*" `表示允许客户端请求的所有Header。如果*未定义*，则允许所有请求的Header。 |
| `methods`          | 支持的HTTP请求方法列表。如果未定义，则使用注解`RequestMapping`定义的方法。 |
| `exposedHeaders`   | 浏览器将允许客户端访问的响应Headaer列表。在实际响应Header`Access-Control-Expose-Headers`中设置成该值。如果*未定义*，则使用一个空的公开Header列表。 |
| `allowCredentials` | 它确定浏览器是否应包括与请求关联的任何cookie。`false` 不应该包含cookie。`" "`（空字符串）表示*undefined*。`true`预检响应将包括Header`Access-Control-Allow-Credentials`，其值设置为true。如果*未定义*，则允许凭据。 |
| `maxAge`           | 预检响应的缓存持续时间的最长期限（以秒为单位）。值设置在header中的`Access-Control-Max-Age`。如果*未定义*，则将最长期限设置为1800秒（30分钟）。 |

#### 1.2.@CrossOrigin在类/控制器级别

HomeController.java

```java
@CrossOrigin(origins = "*", allowedHeaders = "*")
@Controller
public class HomeController 
{
    @GetMapping(path="/")
    public String homeInit(Model model) {
        return "home";
    }
}
```

> 阅读更多– [Spring 5 MVC示例](https://howtodoinjava.com/spring5/webmvc/spring5-mvc-hibernate5-example/)

#### 1.3.方法级别的@CrossOrigin

HomeController.java

```java
@Controller
public class HomeController 
{
    @CrossOrigin(origins = "*", allowedHeaders = "*")
    @GetMapping(path="/")
    public String homeInit(Model model) {
        return "home";
    }
}
```

#### 1.4。@CrossOrigin在方法级别被覆盖

`homeInit()`方法只能从域`http://example.com`访问。其他方法`HomeController`都可以从所有域访问。

HomeController.java

```java
@Controller
@CrossOrigin(origins = "*", allowedHeaders = "*")
public class HomeController 
{
    @CrossOrigin(origins = "http://example.com")
    @GetMapping(path="/")
    public String homeInit(Model model) {
        return "home";
    }
}
```

## 2. Spring CORS –全局CORS配置

#### 2.1.实现WebMvcConfigurer

要为整个应用程序启用CORS，请使用`WebMvcConfigurer`增加`CorsRegistry`。

CorsConfiguration.java

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
 
@Configuration
@EnableWebMvc
public class CorsConfiguration implements WebMvcConfigurer
{
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedMethods("GET", "POST");
    }
}
```

#### 2.2。WebMvcConfigurer Bean

在Spring Boot应用程序中，建议仅声明一个`WebMvcConfigurer`bean。

CorsConfiguration.java

```java
@Configuration
public class CorsConfiguration 
{
    @Bean
    public WebMvcConfigurer corsConfigurer() 
    {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**").allowedOrigins("http://localhost:8080");
            }
        };
    }
}
```

#### 2.3.Spring Security的CORS

要通过[Spring security](https://howtodoinjava.com/spring-security-tutorial/)启用CORS支持，请配置`CorsConfigurationSource`bean并使用`HttpSecurity.cors()`配置。

WebSecurityConfig.java

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and()
            //other config
    }
 
    @Bean
    CorsConfigurationSource corsConfigurationSource() 
    {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("https://example.com"));
        configuration.setAllowedMethods(Arrays.asList("GET","POST"));
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
}
```

学习愉快！

[源代码下载](https://github.com/lokeshgupta1981/spring-webmvc)