# Spring Boot 快速创建一个HelloWorld项目

> JDK版本：java version "1.8.0_45"
>
> IntelliJ IDEA版本：IntelliJ IDEA 2019.2.4 (Ultimate Edition)
>
> maven版本：apache-maven-3.3.3

 ## Step1:pom.xml文件配置

##### 1.1 父项目

```java

//``` 
<parent>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-parent</artifactId>
   <version>2.3.2.RELEASE</version>
   <relativePath/> <!-- lookup parent from repository -->
</parent>


//它的父项目是，负责管理Spring Boot应用里的所有依赖版本
<parent>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-dependencies</artifactId>
   <version>2.3.2.RELEASE</version>
</parent>
```

##### 1.2导入依赖---启动器

``` java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**spring-boot-starter**-<u>*web*</u>:

spring-boot-starter：spring-boot场景启动器；帮我们导入了web模块正常运行所依赖的组件；

Spring-boot将所有的功能场景都抽取出来，做成一个个的starters（启动器）,只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器。

## Step2:创建HelloWorld.java

```java
package com.springTest;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class HelloWorld {

    public static void main(String[] args) {
        SpringApplication.run(HelloWorld.class, args);
    }

    @GetMapping("/hello")
    public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
        return String.format("Hello %s!", name);
    }
}
```

> @SpringBootApplication：表示这个是Spring-Boot应用程序
>
> @RestController：这个注释告诉Spring这个代码描述了一个端点，可以在web上使用
>
> @GetMapping(“/hello”)：告诉Spring使用hello()方法来响应发送到 http://localhost:8080/hello 地址的请求
>
> @RequestParam：这个注释告诉Spring在请求中使用的名称值，如果不存在，默认使用“World”；也就是说如果你将请求中的name的值设置为“Jack”，则响应结果将是“Hello Jack!”

## Step3:运行程序

* 在浏览器中输入http://localhost:8080/hello
* 在浏览器中输入http://localhost:8080/hello?name=Jack
