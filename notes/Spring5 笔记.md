# 一、Spring5

![image-20201029172549818](./images/image-20201029172549818.png)

n

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

# 二、IOC 容器

IOC：控制反转，把对象创建和对象之间的调用过程交给spring进行管理，目的降低耦合度。

# 2.1 IOC底层原理

涉及到的技术：

- XML解析
- 工厂模式
- 反射

## 2.1.1 IOC过程

第一步：XML配置文件，配置创建的对象

```xml
<bean id="dao" class="com.atguigu.UserDao"></bean>
```

第二步：创建工厂类，例如service类和dao类

```java
class UserFactory{
    public static UserDao getDao(){
        //1.xml解析
        String classValue=class属性值; 
  		//2.通过反射创建对象
        Class clazz=Class.forName(classValue);
        return (UserDao)clazz.newInstance();
    }
}
```



# 2.2 IOC接口（BeanFactory）

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2. Spring提供IOC容器实现的两种方式（两个重要的接口）：

   - **BeanFactory**：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用

     **加载配置文件时不会创建对象，在获取对象（使用）才会创建对象**

   ```java
   BeanFactory context=new ClassPathXmlApplicationContext("bean1.xml");
   User user=context.getBean("user",User.class);
   user.add();
   ```

   - **ApplicationContext**：BeanFactory接口的子接口，提供更多更强大的功能，一般面向开发人员进行使用

     **加载配置文件时，就会把在配置文件中的对象进行创建**

   ```java
   ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
   User user=context.getBean("user",User.class);
   user.add();
   ```

3. ApplicationContext接口实现类：

- FileSystemXmlApplicationContext

  带盘符的路径（文件全路径）

- ClassPathXmlApplicationContext

  类路径

# 2.3 IOC操作Bean管理

## 2.3.1 Bean管理含义

Bean管理指的是：Spring创建对象、Spring注入属性

**创建对象的两种方法**：

1. **通过Spri ng XML配置文件进行创建**（Spring配置文件使用XML格式）

2. **注解方法进行创建**

## 2.3.2 IOC操作Bean管理（基于XML）

### 2.3.2.1 创建对象 属性注入

**通过Spring XML配置文件创建对象**

- 在spring配置文件中，使用bean标签，标签里面添加对应属性，就可以实现对象创建
- 在bean标签里面有很多属性

```xml
<!-- 两个标签 id 和class-->
<!-- id属性 对象名字，别名，通过其可以获取该对象，不可以使用特殊属性-->
<!-- class属性 类的路径（包+类的路径）-->
<!-- name属性 作用类似于id属性，可以使用特殊符号-->
<bean id="xxx" class="xxx.xxxx.xxx"></bean>
```

- 创建对象时，默认执行无参数的构造方法

**通过XML方式注入属性**

- DI：依赖注入，就是注入属性（DI是IOC的一种具体实现，表示依赖注入，或者是注入属性，需要在创建对象的基础上完成）

  有两种方式注入属性：使用set方法进行注入，使用有参数构造注入

  ##### set方法注入

  第一步：创建类，定义属性和对应的set方法

  ```java
  public class Book {
      private String bname;
      private String bauthor;
  
      public void setBname(String bname){
          this.bname=bname;
      }
  
      public void setBauthor(String bauthor) {
          this.bauthor = bauthor;
      }
  }
  ```

  第二步：在spring配置文件配置对象创建，配置属性注入

  ```xml
  <!--    1.Book对象创建-->
      <bean id="book" class="com.atguigu.spring5.Book">
          <!--    2.set 方法注入属性-->
          <!--   name 类的属性名 value 向属性注入的属性值     -->
          <property name="bname" value="简爱"></property>
          <property name="bauthor" value="英国"></property>
      </bean>
  ```

  ##### 有参数构造注入

  第一步：创建类，定义属性，创建属性对应的有参数构造方法

  ```java
  private String oname;
  private String address;
  
  //有参数构造
  public Orders(String oname, String address) {
      this.oname = oname;
      this.address = address;
  }
  ```

  第二步：在Spring配置文件中进行配置

  ```java
  <bean id="orders" class="com.atguigu.spring5.Orders">
  	<constructor-arg name="oname" value="abc"/>
      <constructor-arg name="address" value="china"/>
  </bean>
  ```

  ##### P名称空间注入

  使用p名称空间注入，简化基于XML配置方式

  第一步：添加P名称空间在配置文件中

  ```xml
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
         xmlns:p="http://www.springframework.org/schema/p"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">
  ```

  第二步：进行属性注入

  ```java
  <bean id="book" class="com.atguigu.spring5.Book" p:bauthor="九阳神功" p:bname="达摩">
      </beans>
  ```

### 2.3.2.2 XML注入其他类型属性

1. 字面量

   - null

     ```xml
     <!--        设置一个空值-->
     <property name="address"><null/></property>
     ```

   - 属性值包含特殊符号

     ```XML
     <!--属性值中包含特殊符号
     1.把<>进行转义 &lt;&lt;南京&gt;&gt;
     2.把带特殊符号内容写到CDATA结构中去 <![CDATA[[XXXX]]>
     -->
     <property name="address">
     	<value><![CDATA[<<南京>>]]></value>
     </property>
     ```

### 2.3.2.3 注入属性 外部bean

（外部bean，在xml配置文件中，非嵌套关系生成多个bean）

例子：

1. 创建两个类service和dao类

2. 在service中调用dao里面的方法

   ![image-20201030114004958](./images/image-20201030114004958.png)

   ```java
   package com.atguigu.spring5.dao;
   public interface UserDao {
       public void update();
   }
   
   package com.atguigu.spring5.dao;
   public class UserDaoImpl implements UserDao{
       @Override
       public void update() {
           System.out.println("Dao update ....");
       }
   }
   
   
   package com.atguigu.spring5.service;
   import com.atguigu.spring5.dao.UserDao;
   public class UserService {
       private UserDao userDao;
   
       public void setUserDao(UserDao userDao) {
           this.userDao = userDao;
       }
   
       public void add(){
           System.out.println("service add ...");
           userDao.update();
       }
   }
   ```

3. 在spring配置文件中进行配置

```xml
    <!--service和dao对象创建    -->
<bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
    <!-- 注入userDao对象
        name属性：类里面属性名称
        ref 属性：创建userDao对象bean标签id值-->
    
<bean id="userService" class="com.atguigu.spring5.service.UserService">
        <!--  ref 指向引用创建的对象  -->
        <property name="userDao" ref="userDaoImpl"></property>
    </bean>
```

### 2.3.2.4 注入属性 内部bean和级联赋值

（内部bean在一个bean中生成另一个bean对象）

1. 一对多的关系，例如：部分和员工
2. 在实体类之间表示一对多的关系

```java 
package com.atguigu.spring5.bean;
//部门类
public class Department {
    private  String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }
}


package com.atguigu.spring5.bean;
public class Emp {
    private String ename;
    private String gender;
    //员工属于某一个部门，使用对象形式表示
    private Department dept;

    public void setDept(Department dept) {
        this.dept = dept;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }
}
```

3. spring 配置文件中进行配置

   ```xml
   <bean id="emp" class="com.atguigu.spring5.bean.Emp">
       <property name="ename" value="lucky"></property>
       <property name="gender" value="女"></property>
       <property name="dept">
           <!-- 在一个bean中嵌套使用另一个对象-->
           <bean id="dept" class="com.atguigu.spring5.bean.Department">
               <property name="dname" value="安保部"></property>
           </bean>
       </property>
   </bean>
   ```

### 2.3.2.5 注入属性 级联赋值

1. 第一种写法

   ```xml
   <!--级联赋值操作-->
       <bean id="emp" class="com.atguigu.spring5.bean.Emp">
           <property name="ename" value="lucy"></property>
           <property name="gender" value="女"></property>
           <!-- 级联赋值-->
           <property name="dept" ref="dept"></property>
       </bean>
   
       <bean id="dept" class="com.atguigu.spring5.bean.Department">
           <property name="dname" value="财务部"></property>
       </bean>
   ```

2. 第二种写法

   前提：需要get方法

   ```xml
   <!--级联赋值操作-->
       <bean id="emp" class="com.atguigu.spring5.bean.Emp">
           <property name="ename" value="lucy"></property>
           <property name="gender" value="女"></property>
           <!-- 级联赋值-->
           <property name="dept" ref="dept"></property>
           <!-- 这种方式需要emp类有get方法-->
           <property name="dept.dname" value="技术部门"></property>
       </bean>
   
       <bean id="dept" class="com.atguigu.spring5.bean.Department">
           <property name="dname" value="财务部"></property>
       </bean>
   ```

### 2.3.2.6 IOC操作Bean管理（注入集合属性）

1. 注入数组类型属性
2. 注入List集合类型的属性
3. 注入Map集合类型的属性

以上类型，相关步骤：

步骤一：创建类，定义数组、list、map、set类型属性，生成对应set方法

步骤二：在spring配置文件中进行配置

#### 基本类型属性注入

```java
public class Stu 
    private String[] courses;
    private List<String> list;
    private Map<String,String> maps;
    private Set<String> sets;
```

```xml
    <!-- 集合类型属性注入   -->
    <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
    <!--数组类型属性注入 array或list标签-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>

<!--        list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>李四</value>
            </list>
        </property>

<!--        map类型属性注入-->
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
                <entry key="PHP" value="php"></entry>
            </map>
        </property>

<!--        set类型属性注入-->
        <property name="sets">
            <set>
                <value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
    </bean>
```

#### 在集合属性中设置对象类型值

```java
public class Stu 
    private String[] courses;
    private List<String> list;
    private Map<String,String> maps;
    private Set<String> sets;
	//Course为类对象 具有一个属性课程名cname
    private List<Course> courseList;  
```

```xml
<!-- 创建多个course对象-->
    <bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="spring5框架"></property>
    </bean>
    <bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="MyBatis框架"></property>
    </bean>

    <!-- 集合类型属性注入   -->
    <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
<!--数组类型属性注入 array或list标签-->
<!--        list类型属性注入-->     
<!--        map类型属性注入-->    
<!--        set类型属性注入-->
<!--现在注入List集合类型，值是对象-->
        <property name="courseList">
            <list>
                <ref bean="course1"></ref>
                <ref bean="course2"></ref>
            </list>
        </property>
    </bean>
```

#### 把集合注入公共部分提取出来

- 步骤一：在spring配置文件中引入名称空间 util

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:util="http://www.springframework.org/schema/util"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/util  http://www.springframework.org/schema/util/spring-util.xsd">
  ```

- 步骤二：使用util标签 完成公共部分抽取，以集合list为例

  ```java
  public class Book 
      private List<String> list;
  
      public void setList(List<String> list) {
          this.list = list;
      }
  ```

  ```xml
  <!--   1.提取list集合属性注入-->
      <util:list id="bookList">
          <value>简爱</value>
          <value>飘</value>
          <value>百年孤独</value>
      </util:list>
  <!--    2.提取list类型属性注入使用-->
      <bean id="book" class="com.atguigu.spring5.collectiontype.Book">
          <property name="list" ref="bookList"></property>
      </bean>
  ```

### 2.3.2.7 IOC操作Bean管理（FactoryBean）

Spring里面有两种类型的bean，一种普通bean，另外一种工厂bean（FactoryBean）

1. 普通bean

   特点：在配置类型中定义bean类型就是返回类型

2. 工厂bean（FactoryBean）

   特点：在配置文件中定义bean类型可以和返回类型不一致

   第一步：创建类，让其做为工厂bean，实现接口FactoryBean

   第二步：实现接口类里面的方法，在实现的方法中定义返回的bean类型

   ```java
   public class MyBean implements FactoryBean<Course> {
       
       //    定义返回bean
       @Override
       public Course getObject() throws Exception {
           Course course=new Course();
           course.setCname("java");
           return course;
       }
   
       @Override
       public Class<?> getObjectType() {
           return null;
       }
   
       @Override
       public boolean isSingleton() {
           return false;
       }
   }
   
   
       @Test
       public void test3(){
           ApplicationContext context=new ClassPathXmlApplicationContext("bean3.xml");
           Course course=context.getBean("myBean",Course.class);
           System.out.println(course);
       }
   ```

   ```xml
   <bean id="myBean" class="com.atguigu.spring5.factorybean.MyBean"></bean>
   ```

### 2.3.2.8 IOC操作管理 bean作用域

1. 在Spring里面，设置创建bean实例是单实例还是多实例

2. 在Spring，在默认情况下，创建的bean是一个单实例对象

3. bean实例设置

   - 在Spring配置文件bean标签里面有**属性（scope）**用于设置单实例还是多实例  

   - scope属性值：

     - 默认值，singleton，表示单实例对象
     - prototype，表示是多实例对象

     ![image-20201030201533998](./images/image-20201030201533998.png)

     

   - singleton和prototype区别

     - singleton表示单实例，prototype表示多实例

     - 设置scope的值是singleton，加载spring配置文件时就会创建单实例对象

       设置scope的值是prototype时，不是在加载spring配置文件时创建对象，在调用getBean时创建对象

### 2.3.2.9  IOC操作Bean管理（bean生命周期）

1. 生命周期：从对象的创建到对象销毁的过程

2. bean生命周期

   - 第一步：通过构造器创建bean实例（无参数构造）
   - 第二步：为bean的属性设置对应的值和对其他bean的引用（调用类中的set方法）
   - 第三步：调用bean中初始化的方法（需要进行配置）
   - bean可以使用（对象获取到了）
   - 当容器关闭时候，调用bean销毁方法（需要进行配置销毁方法，手动调用）

3. 示例bean的声明周期

   ```java
   package com.atguigu.spring5.bean;
   public class Orders {
   
       private String oname;
   
       public Orders(){
           System.out.println("第一步 执行无参数构造创建bean实例");
       }
   
       public Orders(String oname) {
           this.oname = oname;
       }
   
       public void setOname(String oname) {
           this.oname = oname;
           System.out.println("第二步 调用set方法设置属性的值");
       }
   
       //创建执行的初始化的方法
       public void initMethod(){
           System.out.println("第三步 执行初始化的方法");
       }
   
       //创建销毁执行的方法
       public void destoryMethod(){
           System.out.println("第五步 执行销毁的方法");
       }
   }
   
   ```

   ```xml
   <bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destoryMethod">
       <property name="oname" value="iphone"></property>
   </bean>
   ```

   ```java
       @Test
       public void testBean3(){
   //        ClassPathXmlApplicationContext context=new ClassPathXmlApplicationContext("bean4.xml");
           ApplicationContext context=new ClassPathXmlApplicationContext("bean4.xml");
           Orders orders=context.getBean("orders", Orders.class);
           System.out.println("第四步 获取创建bean实例的对象");
           System.out.println(orders);
   
           //手动 bean实例销毁
           //ApplicationContext 没有实现close方法 其子类有实现的接口所以进行类型的转换
   //        context.close();
           ((ClassPathXmlApplicationContext) context).close();
       }
   ```

4. bean的后置处理器，bean生命周期一共七步

   - 第一步：通过构造器创建bean实例（无参数构造）
   - 第二步：为bean的属性设置对应的值和对其他bean的引用（调用类中的set方法）
   - **新增：把bean实例传递bean后置处理器的方法** postProcessBeforeInitialization方法
   - 第三步：调用bean中初始化的方法（需要进行配置）
   - **新增：把bean实例传递bean后置处理器的方法**postProcessAfterInitialization方法
   - bean可以使用（对象获取到了）
   - 当容器关闭时候，调用bean销毁方法（需要进行配置销毁方法，手动调用）

5. 演示添加后置处理器效果

   - 创建类，实现接口BeanPostProcessor，创建后置处理器

   ```java
   public class MyBeanPost implements BeanPostProcessor {
   
       @Override
       public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("bean初始化之前执行");
           return null;
       }
   
       @Override
       public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
           System.out.println("在bean初始化之后执行");
           return null;
       }
   }
   ```

   ```xml
   <!--    会为当前配置文件的所有实例添加后置处理器-->
   <!--    配置后置处理器-->
       <bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
   ```

   ![image-20201030212020565](./images/image-20201030212020565.png)

### 2.3.2.10 IOC 操作Bean管理（自动装配）

1. 自动装配：

   根据指定装配规则（属性名称或属性类型），Spring自动将匹配的属性值进行注入

2. 演示自动装配过程

   bean标签属性autowire 配置自动装配，autowire属性常用两个值：

   - byName根据属性名注入 ，**注入值bean的id值和类属性名称一样**
   - byType根据属性类型注入，id的名称无限制，只与对象的类型相关

   ```java
   public class Emp {
       private Dept dept;
   ```

   ```xml
   <!--   实现自动装配
           bean标签属性autowire 配置自动装配
           autowire属性常用两个值：byName根据属性名注入 byType根据属性类型注入-->
       <bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName">
   <!--        手动装配方式-->
   <!--        <property name="dept" ref="dept"></property>-->
       </bean>
   
   <!-- id名称与Emp中属性dept相同-->
       <bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
   ```

   ```xml
       <bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byType">
   <!--        手动装配方式-->
   <!--        <property name="dept" ref="dept"></property>-->
       </bean>
   
   <!-- id的名称无限制，只与对象的类型相关-->
       <bean id="deptTwo" class="com.atguigu.spring5.autowire.Dept"></bean>
   ```

### 2.3.2.11 IOC 操作Bean管理（外部属性文件）

1. 直接配置数据库信息

   - 配置德鲁伊连接池
   - 引入德鲁伊连接池依赖jar包

   ```xml
   <!--    直接配置连接池-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
           <property name="url" value="jdbc:mysql://localhost:3306"></property>
           <property name="username" value="root"></property>
           <property name="password" value="root"></property>
       </bean>
   ```

2. 引入外部属性文件配置数据库连接池

   ​	步骤一：**创建外部的属性文件，properties格式文件**，写数据库信息

   ![image-20201030223115015](./images/image-20201030223115015.png)

   ![image-20201030231839983](./images/image-20201030231839983.png)

   步骤二：把外部properties属性文件引入到spring配置文件中

   <u>引入context名称空间</u>

   ```xml
   xmlns:context="http://www.springframework.org/schema/context"      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
   "
   ```

   步骤三：在spring配置文件使用标签引入外部属性文件

   ```xml
   <!--    先引入外部属性文件-->
       <context:property-placeholder location="classpath:jdbc.properties"/>
   <!--    配置连接池-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
   <!--        ${表达式}  使用表达式-->
           <property name="driverClassName" value="${prop.driverClass}"></property>
           <property name="url" value="${prop.url}"></property>
           <property name="username" value="${prop.userName}"></property>
           <property name="password" value="${prop.password"></property>
       </bean>
   ```

## 2.3.3 IOC操作Bean管理（基于注解方式)

### 2.3.3.1 什么是注解

- 注解是代码里面特殊的标记，格式：`@注解名称(属性名称=属性值,属性名称=属性值..)`
- 注解作用于：类、方法、属性
- 注解的目的：简化XML配置

### 2.3.3.2 Spring针对Bean管理操作中的创建对象所提供的注解

- @Component
- @Service
- @Controller
- @Repository

*上面四个注解功能是一样的，都可以用来创建bean实例

### 2.3.3.3 基于注解方式实现对象创建

步骤一：引入依赖

![image-20201030234100835](./images/image-20201030234100835.png)



步骤二：

步骤三：

# 三、AOP

AOP：面向切面，不改源代码进行功能增强

# 四、JdbcTemplate

# 五、事务管理

# 六、Spring5新特性

