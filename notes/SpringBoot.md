# Springboot

Spring是为了解决企业级应用开发的复杂性而创建的，简化开发。

Springboot：简化开发，**约定大于配置**

# 微服务

一种架构风格

- 节省了调用资源
- 每个功能元素的服务都是一个可替换的、可独立升级的软件代码
- 庞大的系统架构给部署和运维带来了很大的难度

# 自动配置

pom.xml

- spring-boot-dependencies：核心依赖在父工程中

- 在写或引入一些springboot的时候，不需要指定版本，因为有这些版本的仓库

- 启动器

  Springboot的启动场景，springboot会将所有的功能场景变成一个个的启动器，例如：spring-boot-starter-web，自动导入web环境的依赖。

  需要使用什么功能，只需要找到对应的启动器就可以了`starter`

  

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
    <artificated>spring-boot-starter</artificated>
</dependency>
```

# 主程序

```java
@SpringBootApplication  //标注类是一个springboot应用
public class HelloworldApplication {
    public static void main(String[] args) {
        //将Springboot应用启动
        SpringApplication.run(HelloworldApplication.class, args);
    }
}
```

```java
@SpringBootConfiguration   //springboot的配置注解
	@Configuration //Spring 配置类
	

@EnableAutoConfiguration 
```

