# Spring Security 5 – Java配置

Java示例，通过注解`@EnableWebSecurity`和类`WebSecurityConfigurerAdapter`来**启用Spring Security Java配置**。

该示例基于[spring webmvc hibernate集成](https://howtodoinjava.com/spring5/webmvc/spring5-mvc-hibernate5-example/)示例构建。

## 1.包含Spring Security 5依赖项

包括spring security jar包。我正在使用maven添加了相应的依赖项**spring security 5**。

pom.xml

```xml
<properties>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <spring.version>5.0.7.RELEASE</spring.version>
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

## 2.创建Spring Security 5配置– @EnableWebSecurity

我创建了这个简单的安全配置，并添加了两个演示用户“ `user`”和“ `admin`”。

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
        .antMatchers("/login").permitAll()
        .antMatchers("/admin/**").hasRole("ADMIN")
        .antMatchers("/**").hasAnyRole("ADMIN", "USER")
        .and().formLogin()
        .and().logout().logoutSuccessUrl("/login").permitAll()
        .and().csrf().disable();
    }
}
```

## 3.初始化spring security

在Spring中，使用`DelegatingFilterProxy`来实现security。要使用Java配置的spring容器注册它，您应该使用`AbstractSecurityWebApplicationInitializer`。

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

```
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

## 4.验证Security

启动应用程序并打开主页。您将看到一个登录页面。这意味着spring security 已配置并正常工作。

![登录表单](https://howtodoinjava.com/wp-content/uploads/2018/08/Login-Form.png)登录表单

**使用用户名/密码登录-“用户”和“ 123456”**

![登录成功](https://howtodoinjava.com/wp-content/uploads/2018/08/Login-Success.png)登录成功

学习愉快！

[源代码下载](https://github.com/lokeshgupta1981/spring-webmvc)