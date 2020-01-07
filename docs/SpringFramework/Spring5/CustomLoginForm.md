# Spring Security 5登录表单示例

在这个Spring Security 5教程中，学习把基于**自定义登录表单**的Security添加到我们的Spring WebMVC应用程序中。我正在使用**Spring Security 5**构建此示例。本教程还讨论了从会话注销。

## 1.包含Spring Security 5依赖项

包含spring security jar包。我正在使用maven，因此为**spring security version 5.0.7.RELEASE**添加了相应的依赖项。

pom.xml

```xml
<properties>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <spring.version>5.2.0.RELEASE</spring.version>
</properties> 
 
<!-- Spring MVC Dependency -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>${spring.version}</version>
</dependency>
 
<!-- Spring Security Core -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>${spring.version}</version>
</dependency>
 
<!-- Spring Security Config -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>${spring.version}</version>
</dependency>
 
<!-- Spring Security Web -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>${spring.version}</version>
</dependency>
```

## 2.创建Spring Security配置

#### 2.1.配置身份验证和URL安全

- 我创建了这个简单的安全配置，并添加了两个演示用户“ `user`”和“ `admin`”。
- `@EnableWebSecurity` 启用Spring Security的Web安全支持，并提供Spring MVC集成。
- `WebSecurityConfigurerAdapter` 提供了一组用于启用特定Web安全配置的方法。
- `configure(HttpSecurity http)` 用于保护需要安全性的不同URL。
- 它在URL **配置自定义登录页面**`/login`。如果用户名/密码匹配，则将请求重定向到`/home`，否则登录页面会刷新并显示相应的错误消息。
- 它还**配置了session的注销**。注销后，用户将再次重定向到登录页面。

> 我已使用进行了内存中身份验证`auth.inMemoryAuthentication()`。您可以使用来配置JDBC身份验证`auth.jdbcAuthentication()`或使用来配置LDAP身份验证`auth.ldapAuthentication()`。

SecurityConfig.java

```java
package com.howtodoinjava.demo.spring.config;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
 
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
 
    @Autowired
    PasswordEncoder passwordEncoder;
 
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
        .passwordEncoder(passwordEncoder)
        .withUser("user").password(passwordEncoder.encode("123456")).roles("USER")
        .and()
        .withUser("admin").password(passwordEncoder.encode("123456")).roles("USER", "ADMIN");
    }
 
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
        .antMatchers("/login")
            .permitAll()
        .antMatchers("/**")
            .hasAnyRole("ADMIN", "USER")
        .and()
            .formLogin()
            .loginPage("/login")
            .defaultSuccessUrl("/home")
            .failureUrl("/login?error=true")
            .permitAll()
        .and()
            .logout()
            .logoutSuccessUrl("/login?logout=true")
            .invalidateHttpSession(true)
            .permitAll()
        .and()
            .csrf()
            .disable();
    }
}
```

> 阅读更多：[登录表单的Spring Security XML Config](https://howtodoinjava.com/spring-security/login-form-based-spring-3-security-example/)

#### 2.2.将Spring Security绑定到Web应用程序

在Spring Web应用程序中，使用来实现安全性`DelegatingFilterProxy`。要使用Java配置的spring容器注册它，您应该使用`AbstractSecurityWebApplicationInitializer`。

Spring将在应用程序启动期间检测此类的实例，并在任何其他Filter注册之前先注册`DelegatingFilterProxy`来使用`springSecurityFilterChain。`它还会注册一个`ContextLoaderListener`。

SpringSecurityInitializer.java

```java
package com.howtodoinjava.demo.spring.config;
 
import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;
public class SpringSecurityInitializer extends AbstractSecurityWebApplicationInitializer {
    //no code needed
}
```

另外，包含`SecurityConfig`到`AppInitializer`。

AppInitializer.java

```java
package com.howtodoinjava.demo.spring.config;
 
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;
 
public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
 
   @Override
   protected Class<?>[] getRootConfigClasses() {
      return new Class[] { HibernateConfig.class, SecurityConfig.class };
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

## 3.添加自定义登录表单

#### 3.1.登录控制器

添加登录控制器方法来处理登录和注销请求。

LoginController.java

```java
package com.howtodoinjava.demo.spring.controller;
 
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
 
@Controller
public class LoginController 
{
    @RequestMapping(value = "/login", method = RequestMethod.GET)
    public String loginPage(@RequestParam(value = "error", required = false) String error, 
                            @RequestParam(value = "logout", required = false) String logout,
                            Model model) {
        String errorMessge = null;
        if(error != null) {
            errorMessge = "Username or Password is incorrect !!";
        }
        if(logout != null) {
            errorMessge = "You have been successfully logged out !!";
        }
        model.addAttribute("errorMessge", errorMessge);
        return "login";
    }
  
    @RequestMapping(value="/logout", method = RequestMethod.GET)
    public String logoutPage (HttpServletRequest request, HttpServletResponse response) {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        if (auth != null){    
            new SecurityContextLogoutHandler().logout(request, response, auth);
        }
        return "redirect:/login?logout=true";
    }
}
```

#### 3.2.login.jsp

创建接受`username`和`password``的login.jsp`文件；并将它们发布到URL `/login`。

login.jsp

```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<body onload='document.loginForm.username.focus();'>
    <h1>Spring Security 5 - Login Form</h1>
 
    <c:if test="${not empty errorMessge}"><div style="color:red; font-weight: bold; margin: 30px 0px;">${errorMessge}</div></c:if>
 
    <form name='login' action="/login" method='POST'>
        <table>
            <tr>
                <td>UserName:</td>
                <td><input type='text' name='username' value=''></td>
            </tr>
            <tr>
                <td>Password:</td>
                <td><input type='password' name='password' /></td>
            </tr>
            <tr>
                <td colspan='2'><input name="submit" type="submit" value="submit" /></td>
            </tr>
        </table>
        <input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
    </form>
</body>
</html>
```

## 4. Spring Security 5登录表单Demo

1. 使用maven run命令启动应用程序`tomcat7:run`。启动主页`http://localhost:8080/home`。它将重定向到登录页面`http://localhost:8080/login`。

   ![登录表单](https://howtodoinjava.com/wp-content/uploads/2018/08/Login-Form-1.png)登录表单

2. 输入不正确的用户名或密码。将显示登录错误消息。

   

   ![用户名或密码错误](https://howtodoinjava.com/wp-content/uploads/2018/08/Incorrect-username-or-password.png)用户名或密码错误

3. 输入正确的用户名和密码组合user`和`123456`。将显示主页。

   

   ![主页](https://howtodoinjava.com/wp-content/uploads/2018/08/Home-Page.png)主页

4. 单击注销按钮。用户将被注销，然后重定向回到登录页面。

   

   ![登出](https://howtodoinjava.com/wp-content/uploads/2018/08/Logged-Out.png)登出

学习愉快！

[源代码下载](https://github.com/lokeshgupta1981/spring-webmvc)

