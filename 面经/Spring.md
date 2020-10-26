# 1. Spring

## Spring框架概述

1. 轻量级的开源的J2EE应用程序框架
2. 解决企业应用开发的复杂性
3. Spring有两个核心的部分：IOC和AOP
4. Spring特点：
   - 方便解耦，简化开发
   - AOP编程的支持
   - 方便程序的测试
   - 方便和其他框架进行整合
   - 降低Java EE API的使用难度

# 2. IOC容器

IOC（Inversion of Control）：控制反转

- 把对象创建以及对象之间的调用过程，交给spring进行管理
- 目的：为了耦合度降低

## IOC底层原理

涉及到三种技术，包括：**XML解析、工厂模式和反射**

1. XML解析

2. 工厂模式

3. 反射

IOC示例过程，步骤：

1. XML配置文件，配置创建的对象

   ```xml
   <bean id="dao" class="com.atguigu.UserDao"></bean>
   ```

2. 有service类和dao类，创建工厂类

   ```java
   class UserFactory{
       public static UserDao getDao(){
           //1. xml解析 bean中class的属性值
           String classValue=calss属性值;
           //2. 创建对象 通过反射创建对象
           Class clazz=Class.forName(classValue);
           return (UserDao)clazz.newInstance();
       }
   }
   ```

## IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2. Spring提供了IOC容器实现两种方式：（两个接口）

   均能实现加载Spring配置文件，创建对象的功能

   - BeanFactory：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用

     （加载配置文件时候不会创建里面的对象，而是在使用（获取对象）的时候才创建对象）

   - ApplicationContext：BeanFactory接口的子接口，提供了更多更强大的功能，一般面向开发人员进行使用

     （加载配置文件时候就会把配置文件对象进行创建）

   ```java
   ApplicationContext context=new ClassPathXMLApplicationContext("xxx.xml");
   YYY yy=context.getBean(YYY.class);
   
   BeanFactory context=new ClassPathXMLApplicationContext("xxx.xml");
   YYY yyy=contenxt.getBean(YYY.class);
   ```

   **ApplicationContext实现类有两个**：

   ![image-20201022121717959](F:/Code/blogs/面经/images/image-20201022121717959.png)

   - FileSystemXmlApplicationContext

     文件全路径（绝对路径）

   - ClassPathXmlApplicationContext

     src路径下（相对路径）

## IOC操作 Bean管理

**什么是Bean管理**

Bean管理指的是两个操作

- Spring创建对象
- Spring注入属性

**Bean管理操作有两种方式**

- 基于xml配置文件方式实现
- 基于注解方式实现

**IOC操作Bean管理（基于XML方式）**

1. 基于xml方式创建对象

   - 在spring配置文件中使用bean标签，标签中添加对应属性，实现对象创建
   - 在bean标签中存在很多属性
     - id：唯一标识，不能加特殊符号
     - class：类全路径（包类路径）
     - name：可以加特殊符号，类似于id
   - 在创建对象时，默认使用无参数的构造方法

   ```xml
   <!-- 两个标签 id 和class-->
   <!-- id为对象的名字-->
   <!-- class为对象的路径-->
   <bean id="xxx" class="xxx.xxxx.xxx"></bean>
   ```

2. 基于xml方式注入属性

   - DI：依赖注入，就是注入属性（DI是IOC的一种具体实现）

     - 第一种注入方式：使用set方法进行注入

       <u>步骤一</u>：创建类，定义属性和对应的set方法（set方法是必须的）

       ```java
       package com.atguigu.spring5;
       
       public class Book {
           private String bname;
           private String bauthor;
       
           public void setBauthor(String bauthor) {
               this.bauthor = bauthor;
           }
       
           public void setBname(String bname) {
               this.bname = bname;
           }
       
       
           public static void main(String[] args) {
               Book book=new Book("abc");
           }
       }
       
       ```

       

       <u>步骤二</u>：在spring文件配置文件配置对象创建，配置属性注入

       ```java
       <!--    1.配置Book对象创建-->
           <bean id="book" class="com.atguigu.spring5.Book">
               <!--    2.set 方法注入属性-->
               <!--   name 属性名 value 属性值     -->
               <property name="bname" value="简爱"></property>
               <property name="bauthor" value="英国"></property>
           </bean>
       ```

       

     - 第二种注入方式：使用有参数构造进行注入

   - 

# 3. AOP

AOP：面向切面，不改源代码进行功能增强

# 4. JdbcTemplate

# 5. 事务管理

# Spring5新特性
