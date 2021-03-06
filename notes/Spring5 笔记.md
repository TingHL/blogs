  Spring模块划分图：

1. Test：Spring的单元测试模块
2. Core Container ：核心容器
3. AOP + Aspects： 面向切面编程模块
4. Data Access/Integeration：数据访问 集成
5. Web：Spring开发WEB开发模块

![image-20201029172549818](./images/image-20201029172549818.png)

# Spring框架概述

1. 轻量级的开源的J2EE应用程序框架 容器框架 容器（可以管理所有的组件（类））框架

2. 解决企业应用开发的复杂性

3. 两个核心的部分：IOC和AOP

4. Spring特点：
   - 方便解耦，简化开发
   - AOP编程的支持
   - 方便程序的测试
   - 方便和其他框架进行整合
   - 降低Java EE API的使用难度
   
5. 注：

   框架：高度抽取可重用代码的一种设计，高度的可重用性

6. ![image-20201228164344526](images/image-20201228164344526.png)

7. 

# IOC 容器

**IOC，Inversion of Control ，控制反转，把对象创建和对象之间的调用过程交给spring进行管理，目的降低耦合度。**

控制：资源的获取方式

- 主动式：自己主动创建
- 被动式：资源的获取不是程序员主动创建，由容器创建和设置

容器：管理所有的组件（有功能的类）；容器可以自动探查哪些组件（类）需要用到另一个组件（类），容器帮我们创建资源，并赋值过去；主动的new 资源变为被动的接受资源

## IOC 底层原理

涉及到的技术：

- XML解析
- 工厂模式
- 反射

## IOC过程

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

## IOC接口 `BeanFactory ApplicationContext`

1. IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2. Spring提供IOC容器实现的两种方式（两个重要的接口）：

   - `BeanFactory`：IOC容器基本实现，是Spring内部的使用接口，不提供开发人员进行使用

     **加载配置文件时不会创建对象，在获取对象（使用）才会创建对象**

   ```java
   BeanFactory context=new ClassPathXmlApplicationContext("bean1.xml");
   User user=context.getBean("user",User.class);
   user.add();
   ```

   - `ApplicationContext`：BeanFactory接口的子接口，提供更多更强大的功能，一般面向开发人员进行使用

     **加载配置文件时，就会把在配置文件中的对象进行创建**

   ```java
   //根据spring的配置文件得到IOC容器对象
   ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
   User user=context.getBean("user",User.class);
   user.add();
   ```

3. `ApplicationContext`接口实现类：

	- `FileSystemXmlApplicationContext`：带盘符的路径 文件全路径 

	- `ClassPathXmlApplicationContext`：类路径  IOC容器的配置文件在类路径下

# IOC操作Bean管理

## Bean管理含义

**Bean管理指的是：Spring创建对象、Spring注入属性**

**创建对象的两种方法**：

1. **通过Spring XML配置文件进行创建**（Spring配置文件使用XML格式）
2. **注解方法进行创建**

## IOC操作Bean管理（基于XML）

### 创建对象

**通过Spring XML配置文件创建对象**

- 在spring配置文件中，使用**bean标签**，标签里面添加对应属性，就可以实现对象创建
- 在bean标签里面有很多属性，例如：

```xml
<!-- 两个标签 id 和class-->
<!-- id属性 对象名字，别名，通过其可以获取该对象，不可以使用特殊属性-->
<!-- class属性 类的路径（包+类的路径）-->
<!-- name属性 作用类似于id属性，可以使用特殊符号-->
<bean id="xxx" class="xxx.xxxx.xxx"></bean>
```

- **创建对象时，默认执行无参数的构造方法（反射用到的就是无参数构造函数）**

### 属性注入 

**通过XML方式注入属性**

- DI：依赖注入，就是注入属性（DI是IOC的一种具体实现，表示依赖注入，或者是注入属性，需要在创建对象的基础上完成）

  容器能够知道哪个组件（类）运行的时候，需要另外一个类（组件）；容器通过反射的形式，将容器中准备好的对象注入（利用反射给属性赋值）

  **有两种方式注入属性：使用set方法进行注入，使用有参数构造注入**

  

  ##### 第一种方式：通过set方法注入
  
  示例第一步：创建类，定义属性和对应的set方法
  
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
      <!--   name的值是由getter/setter方法（去掉set或get以后）后确定的
           value 向属性注入的属性值     -->
      <property name="bname" value="简爱"></property>
    <property name="bauthor" value="英国"></property>
  </bean>
  ```
```
  
  
  
  ##### 第二种方式：通过有参数构造注入
  
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
	
  ```xml
<bean id="orders" class="com.atguigu.spring5.Orders">
  	<constructor-arg name="oname" value="abc"/>
    <constructor-arg name="address" value="china"/>
  </bean>

  <!-- 另外一个示例  重载情况下type可以指定参数的类型-->
  <!-- public Person(String lastName,Integer age,String gender)-->
  <!-- public Person(String lastName,String email,String gender)-->
  <bean id="person" class="com.atguigu.bean.Person">
      <constructor-arg value="小丽"></constructor-arg>
      <constructor-arg value="10" index="1" type="java.lang.Integer"></constructor-arg>
      <constructor-arg value="男"></constructor-arg>
  </bean>
  ```

  ##### 第三种方式：通过P名称空间注入

  使用p名称空间注入，简化基于XML配置方式

  （名称空间：在XML文件中名称空间用来防止标签重复）

  第一步：添加P名称空间在配置文件中

  ```xml
  <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
           xmlns:util="http://www.springframework.org/schema/util"
           xmlns:p="http://www.springframework.org/schema/p"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">
  ```

    第二步：进行属性注入

  ```xml
  <bean id="book" class="com.atguigu.spring5.Book" p:bauthor="九阳神功" p:bname="达摩"></beans>
  ```



### 注入其他类型属性：null, 特殊符号等

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



###  注入属性 ：外部bean

（外部bean，在xml配置文件中，非嵌套关系生成多个bean）。示例：

1. 创建两个类service和dao类

2. 在service中调用dao里面的方法

   ![image-20201030114004958](./images/image-20201030114004958.png)

   ```java
   package com.atguigu.spring5.dao;
   public interface UserDao {
       public void update();
   }
   ```

   ```java
   package com.atguigu.spring5.dao;
   public class UserDaoImpl implements UserDao{
       @Override
       public void update() {
           System.out.println("Dao update ....");
       }
   }
   ```

   ```java
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

4. 

### 注入属性：内部bean

（内部bean在一个bean中生成另一个bean对象）。例子：

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
```
```java
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
           <!-- 在一个bean中嵌套使用另一个对象bean实例-->
           <bean id="dept" class="com.atguigu.spring5.bean.Department">
               <property name="dname" value="安保部"></property>
           </bean>
       </property>
   </bean>
```



### 注入属性：级联赋值

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
           <property name="dept.dname" value="技术部门"></property>   <!-- 修改dept实例属性，引用修改，即该实例修改，dept.dname属性为技术部门-->
       </bean>
   
       <bean id="dept" class="com.atguigu.spring5.bean.Department">
           <property name="dname" value="财务部"></property>
       </bean>
   ```

### 注入属性：集合属性

1. 注入数组类型属性
2. 注入List集合类型的属性
3. 注入Map集合类型的属性

针对以上类型，相关步骤：

步骤一：创建类，定义数组、list、map、set类型属性，生成对应set方法

步骤二：在spring配置文件中进行配置

#### 基本类型属性注入

```java
public class Stu{
    private String[] courses;
    private List<String> list;
    private Map<String,String> maps;
    private Set<String> sets;
    private Properties properties;
    //构造器方法 set get等方法
}
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

	   <!-- list类型属性注入 -->
        <property name="list">
            <list>
                <value>张三</value>
                <value>李四</value>
            </list>
        </property>

	   <!-- map类型属性注入-->
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
                <entry key="PHP" value="php"></entry>
            </map>
        </property>

	   <!-- set类型属性注入-->
        <property name="sets">
            <set>
                <value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
        
        <!-- properties类型属性注入   private Properties properties; -->
        <property name="properties">
            <!-- properties=new Properties(); 所有的K=V都是string -->
            <props>
                <prop key="username">root</prop>
                <prop key="password">root</prop>
            </props>
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

#### 把集合注入公共部分提取出来 名称空间`util`

- 步骤一：在spring配置文件中引入名称空间 util

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:util="http://www.springframework.org/schema/util"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/util  http://www.springframework.org/schema/util/spring-util.xsd">
  ```

- 步骤二：使用`util`标签 完成公共部分抽取，以集合list为例

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

#### 通过继承实现bean配置信息的重用

```xml
<!-- abstract="true" :这个bean的配置是一个抽象的，不能获取他的实例，只能被别人用来继承 -->
<bean id="person05" class="com.atguigu.bean.Person" abstract="true">
    <property name="lastName" value="张三"></property>
    <property name="age" value="18"></property>
    <property name="gender" value="男"></property>
    <property name="email" value="zhangsan@atguigu.com"></property>
</bean>

<bean id="person06" class="com.atguigu.bean.Person" parent="person05">
    <property name="lastName" value="李四"></property> <!-- 需要改变的属性 其他的继承不变-->
</bean>
```

### Bean之间的依赖

原来是按照配置的顺序创建bean

```xml
<!-- 创建顺序是：car person book-->
<bean id="car" class="com.atguigu.bean.Car"></bean>
<bean id="person" class="com.atguigu.bean.Person"></bean>
<bean id="book" class="com.atguigu.bean.Book"></bean>
```

改变bean的创建顺序

```xml
<!--创建顺序是：person book car  -->
<bean id="car" class="com.atguigu.bean.Car" depends-on="person,book"></bean>
<bean id="person" class="com.atguigu.bean.Person"></bean>
<bean id="book" class="com.atguigu.bean.Book"></bean>
```



### 静态工厂 实例工厂

静态工厂：工厂本身不用创建对象，通过静态方法调用，`对象=工厂类.工厂方法名()`

实例工厂：工厂本身需要创建对象；`工厂类.工厂对象=new 工厂类();	工厂对象.getInstance("xxx",xxxx[...]);`

```java
//静态工厂
public class AirPlaneStaticInstanceFactory{
    public static AirPlane getAirPlane(String name){
        AirPlane airPlane=new AirPlane();
        airPlane.setName(name);
       	return airPlane;
    }
}

//实例工厂
public class AirPlaneInstanceFactory{
    public AirPlane getAirPlane(String name){
        AirPlane airPlane=new AirPlane();
        airPlane.setName(name);
       	return airPlane;
    }
}
```

```xml
<!-- 静态工厂（不需要创建工厂本身） 
	class：指定静态工厂全类名
	factory-method：指定哪个方法是工厂方法
	construct-arg：可以为方法传递参数
-->
<bean id="airPlane01" class="com.atguigu.factory.AirPlaneStaticInstanceFactory" factory-method="getAirPlane">
    <constructor-arg name="airPlaneName" value="xxxx"></constructor-arg>
</bean>

<!-- 实例工厂模式
	1.先配置出实例工厂对象
	2.配置我们要创建的AirPlane使用哪个工厂实例
		1) factory-bean：指定使用哪个工厂实例
		2) factory-method:使用哪个工厂方法
-->
<bean id="airPlanInstanceFactory" class="com.atguigu.factory.AirPlaneInstanceFactory"></bean>
<bean id="airPlane02" class="com.atguigu.bean.AirPlane" factory-bean="airPlaneInstanceFactory" factory-method="getAirPlane">
    <constructor-arg name="airPlaneName" value="xxxx"></constructor-arg>
</bean>
```



### FactoryBean  

Spring里面有两种类型的bean，一种普通bean，另外一种工厂bean（`FactoryBean`）。

FactoryBean 是Spring规定的一个接口，只要是这个接口的实现类，Spring都认为是一个工厂

1. 普通bean

   特点：在配置类型中定义bean类型就是返回类型

2. 工厂bean（`FactoryBean`）

   特点：在配置文件中定义bean类型可以和返回类型不一致，在IOC容器启动的时候，不会创建实例，获取的时候才创建对象（不关`isSingleton`函数）

   第一步：创建类，让其做为工厂bean，实现接口`FactoryBean`

   第二步：实现接口类里面的方法，在实现的方法中定义返回的bean类型

   ```java
   public class MyBean implements FactoryBean<Course> {    
       // 工厂方法：定义返回的bean对象
       @Override
       public Course getObject() throws Exception {
           Course course=new Course();
           course.setCname("java");
           return course;
       }
   
       //返回创建的对象的类型
       //Spring会自动调用这个方法来确认创建的对象是什么类型
       @Override
       public Class<?> getObjectType() {
           return Course.class;
       }
   
       @Override
       public boolean isSingleton() {
           return false;
       }
   }
   ```
   
   ```java
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

###  bean作用域 scope

1. 在Spring里面，设置创建bean实例是单实例还是多实例

2. 在Spring，在默认情况下，创建的bean是一个单实例对象

3. bean实例设置

   - 在Spring配置文件bean标签里面有**属性（scope）用于设置单实例还是多实例  **

   - scope属性值：

     - 默认值，singleton，表示单实例对象
     - prototype，表示是多实例对象

     ![image-20201030201533998](./images/image-20201030201533998.png)

     

   - singleton和prototype区别

     - singleton表示单实例，prototype表示多实例

     - 设置scope的值是singleton，加载spring配置文件时就会创建单实例对象，保存在容器中

       设置scope的值是prototype时，不是在加载spring配置文件时创建对象，在调用getBean时创建对象
       
       

### bean生命周期

1. 生命周期：从对象的创建到对象销毁的过程

2. bean生命周期

   - 第一步：通过构造器创建bean实例（无参数构造）

   - 第二步：为bean的属性设置对应的值和对其他bean的引用（调用类中的set方法）

   - 第三步：调用bean中初始化的方法（需要进行配置，`init`方法）

   - bean可以使用（对象获取到了）

   - 当容器关闭时候，调用bean销毁方法（需要进行配置销毁方法，手动调用）

   - 单实例

     单例：（容器启动）构造器 ---> 初始化方法 ---> （容器关闭）销毁方法

     多例：获取bean（构造器 --> 初始化方法）--> 容器关闭不会调用bean的销毁方法

   - 

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
   <!-- 指定初始化方法init-method 和销毁方法destroy-method -->
   <bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destoryMethod">
          <property name="oname" value="iphone"></property>
   </bean>
   ```
   
   ```java
   @Test
   public void testBean3(){
       ApplicationContext context=new ClassPathXmlApplicationContext("bean4.xml");
       Orders orders=context.getBean("orders", Orders.class);
       System.out.println("第四步 获取创建bean实例的对象");
       System.out.println(orders);
       //手动 bean实例销毁
       //ApplicationContext 没有实现close方法 其子类有实现的接口所以进行类型的转换
   	//context.close();  必须调用close方法，才会调用destroy-method方法
       ((ClassPathXmlApplicationContext) context.close();
   }
   ```

4. bean的后置处理器，bean生命周期一共七步

   - 第一步：通过构造器创建bean实例（无参数构造）
   - 第二步：为bean的属性设置对应的值和对其他bean的引用（调用类中的set方法）
   - **新增：把bean实例传递bean后置处理器的方法** `postProcessBeforeInitialization`方法
   - 第三步：调用bean中初始化的方法（需要进行配置，Initialization方法）
   - **新增：把bean实例传递bean后置处理器的方法**`postProcessAfterInitialization`方法
   - bean可以使用（对象获取到了）
   - 当容器关闭时候，调用bean销毁方法（需要进行配置销毁方法，手动调用）

5. 演示添加后置处理器效果

   - 创建类，实现接口`BeanPostProcessor`，创建后置处理器

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

   会为当前配置文件的所有实例添加后置处理器 
   
   ```xml
   <!--    会为当前配置文件的所有实例添加后置处理器-->
   <!--    配置后置处理器-->
    <bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
   ```
   
   ![image-20201030212020565](./images/image-20201030212020565.png)



### XML中的自动装配

1. 自动装配：

   根据指定装配规则（属性名称或属性类型），Spring自动将匹配的属性值进行注入（仅限于自定义类型的数据）   

2. 演示自动装配过程

   bean标签属性`autowire` 配置自动装配，`autowire`属性常用两个值：

   - `byName`根据属性名注入 ，**注入值bean的id值和被注入类的属性名称一样**
   - `byType`根据属性类型注入，id的名称无限制，只与对象的类型相关

   ```java
   public class Emp {
       private Dept dept;
   }
   ```
```xml
   <!--   实现自动装配
           bean标签属性autowire 配置自动装配
           autowire属性常用两个值：byName根据属性名注入 byType根据属性类型注入-->
       <bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName">
   	<!--手动装配方式
   	<property name="dept" ref="dept"></property>
   	-->
       </bean>
   
   	<!-- id名称与Emp类中属性dept相同-->
    <bean id="dept" class="com.atguigu.spring5.autowire.Dept"></bean>
```

   ```xml
   <bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byType"></bean>
   
   <!-- id的名称无限制，只与对象的类型相关-->
   <bean id="deptTwo" class="com.atguigu.spring5.autowire.Dept"></bean>
   ```

### SpEL 表达式

Spring Expression Language，Spring表达式语言

1. 在SpEL中使用字面量

   `<property name="salary" value="#{1234*12}"></property>`

2. 引用其他bean

   `<property name="car" value="#{car}"></property>`

3. 引用其他bean的某个属性值

   `<property name="lastName" value="#{book01.bookName}"></property>`

4. 调用非静态方法 格式：`对象.方法名`

   `<property name="gender" value="#{book01.getBookName()}></property>"`

5. 调用静态方法 格式：`#{T(全类名).静态方法名(参数)}`

   `<property name="email" value="#{T(java.util.UUID).randomUUID().toString()}></property>"`

6. 调用运算符



### 外部属性文件

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
   <beans xmlns:context="http://www.springframework.org/schema/context"      
   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
   </beans>
   ```
```xml
   
步骤三：在spring配置文件使用标签引入外部属性文件
   
   ```xml
   <!-- 先引入外部属性文件-->
   <context:property-placeholder location="classpath:jdbc.properties"/>
   <!-- 配置连接池-->
   <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
   <!-- ${表达式}  使用表达式-->
       <property name="driverClassName" value="${prop.driverClass}"></property>
       <property name="url" value="${prop.url}"></property>
       <property name="username" value="${prop.userName}"></property>
       <property name="password" value="${prop.password"></property>
   </bean>
```

## IOC操作Bean管理（基于注解方式)

### 注解

- 注解是代码里面特殊的标记，格式：`@注解名称(属性名称=属性值,属性名称=属性值..)`
- 注解作用于：类、方法、属性
- 注解的目的：简化XML配置

**Spring针对Bean管理操作中的创建对象所提供的注解**，四个注解功能是一样的，都可以用来创建bean实例：

- @Service：业务逻辑，推荐业务逻辑层的组件添加这个注解
- @Controller：控制器，推荐给控制器层的组件添加这个注解
- @Repository：给数据库（持久层，dao层）的组件添加这个注解
- @Component：给不属于以上这几层的组件添加这个注解

@Repository("xxx")  id默认是类名首字母小写，自定义类名为xxx

@Scope(value="prototype")  默认是单实例，设置多实例

### 基于注解方式实现对象创建

步骤一：引入依赖

![image-20201030234100835](./images/image-20201030234100835.png)



步骤二：开启组件扫描

- 引入context名称空间

- 开启组件扫描

  ![image-20201031115247131](./images/image-20201031115247131.png)

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
  	<!--开启组件扫扫描-->
  	<!-- base-package 指定扫描哪一个指定的包-->
  	<!-- 1. 如果扫描多个包，多个包使用逗号隔开-->
      <context:component-scan base-package="com.atguigu.spring5.testdemo.dao,com.atguigu.spring5.testdemo.service"></context:component-scan>
  	<!-- 2. 如果扫描多个包，扫描包的上层目录-->
      <context:component-scan base-package="com.atguigu"></context:component-scan>
  </beans>
  ```

步骤三：创建类，在类上面添加创建对象注解

```java
//测试
@Test
public void testService(){
    ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
    UserService userService=context.getBean("userService",UserService.class);
    userService.add();
}
```

### 开启组件扫描细节配置  include-filter exclude-filter

示例一：

```xml
<!--
use-default-filter="false"：不使用默认filter 自己配置filter
context:include-filter：设置扫描哪些内容 
	type="annotation" (annotation注解) expresssion="xxx.Controller" 将会扫描controller注解修饰的类
-->
<context:component-scan base-package="com.atguigu" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

示例二：

```xml
<!--
use-default-filter="false"：不使用默认filter 自己配置filter 
context:exclude-filter：设置不扫描哪些内容 
	type="annotation" (annotation注解) expresssion="xxx.Controller" 不扫描controller注解修饰的类
	type="assignalbe" 指定排除某个具体的类，按照类进行排除  expression=""，类的全类名
-->
<context:component-scan base-package="com.atguigu" use-default-filters="false">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    <context:exclude-filter type="assignable" expression="com.atguigu.bean.Book"/>
</context:component-scan>
```



### 基于注解方式实现属性注入

三种常用注解：

1. @AutoWired：根据属性类型进行自动装配，注入 
2. @Qualifier：根据属性名称进行注入，和@AutoWired一起进行使用
3. @Resource：可以根据类型注入，也可以根据名称注入
4. @Value：注入普通类型属性

`@AutoWired`：Spring 框架自身的注解

`@Resource`：J2EE Java标准下的注解，拓展性更强，如果切换成另外一个容器框架，`@Resource`仍然可以使用，`@AutoWired`不可以

#### @AutoWired：根据属性类型进行自动装配，注入示例

方法上有`@AutoWired`，方法会在`bean`创建的时候自动运行，在这个方法上的每一个参数都会自动注入值

步骤一：把service和dao对象创建，在service和dao类添加对象注解

```java
@Repository
public class UserDaoImpl implements UserDao{
    @Override
    public void add() {
        System.out.println("dao add ...");
    }
}
```

步骤二： 在service注入dao对象，在service类和dao类型属性，在属性上面使用注解

```java
//在注解里面value属性值可以省略不写  默认值是类名称，首字母小写  创建id为userService的单实例
@Component(value="userService")  //<bean id="userService" class=""/> 等价
public class UserService {
	//  定义dao类型属性 不需要set方法 添加注入属性注解
    //根据类型进行注入
    @Autowired  
    private UserDao userDao;
    
    public void add(){
        System.out.println("service add ......");
        userDao.add();
    }
}
```

#### @Qualifier：根据属性名称进行注入示例

必须与@AutoWired一起使用

```java
@Repository(value = "userDaoImpl")
public class UserDaoImpl implements UserDao{
    @Override
    public void add() {
        System.out.println("dao add ...");
    }
}
```

```java
@Component(value="userService")  //<bean id="userService" class=""/> 等价
public class UserService {
	//定义dao类型属性 不需要set方法 添加注入属性注解
    @Autowired
    @Qualifier(value = "userDaoImpl")
    private UserDao userDao;

    public void add(){
        System.out.println("service add ......");
        userDao.add();
    }
}
```

#### @Resource：可以根据类型注入，也可以根据名称注入示例

来自javax.annotaion.Resource 拓展包中

```java
@Resource  //根据类型进行注入
private UserDao userDao;

@Resource(name = "userDaoImpl") //依据名称进行注入
private UserDao userDao;
```

#### @Value：注入普通类型属性示例

```java
@Value(value = "abc") //@注入属性值
private String nname;
```



###  完全注解开发

1. 创建配置类，代替XML配置文件

   ```java
   @Configuration //将当前类作为配置类，代替XML配置文件
   @ComponentScan(basePackages = {"com.atguigu"})  //注解表示扫描的位置包部分
   public class SpringConfig {
   }
   ```

2. 编写测试类

   ```java
   @Test
   public void testService(){
   	//  加载配置类
   	ApplicationContext context=new 	AnnotationConfigApplicationContext(SpringConfig.class);
   	UserService userService=context.getBean("userService",UserService.class);
   	userService.add();
   }
   ```

### 依赖泛型注入

![image-20201229155740485](images/image-20201229155740485.png)

```java
public class BaseService<T>{  //注意这里BaseService并没有注解表明其是组件
    @Autowired
    private BaseDao<T> baseDao;
    
    public void save(){
        Sysytem.out.println("自动注入的dao："+baseDao);
        baseDao.save();
    }
}
```

```java
@Service
public class BookService extends BaseService<Book>{}
```

```java
@Service
public class UserService extends BaseService<User>{}
```

```java
public abstract class BaseDao<T>{
    public abstract void save();
}
```

```java
@Repository
public class BookDao extends BaseDao<Book>{
    @Override
    public void save(){
        System.out.println("book basedao");
    }
}
```

```java
@Repository
public class UserDao extends BaseDao<User>{
    @Override
    public void save(){
        System.out.println("user basedao");
    }
}
```



# AOP概念

- AOP（Aspect Oriented Programming） ：面向切面编程，不改源代码进行功能增强。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可用性，提高开发效率。
- 通俗描述：不通过修改源代码方式，在主干功能里面添加新功能（将某段代码动态的切入到指定的方法的指定位置进行运行的编程方式）
- 使用登录的例子进行说明

![image-20201031201601261](./images/image-20201031201601261.png)

# AOP底层原理

先介绍底层原理如何实现，在介绍使用注解封装之后，更简单的使用方法AOP操作（AspectJ注解）

## AOP底层使用动态代理

1. 有两种情况的动态代理

   - **第一种情况：有接口的情况 使用JDK动态代理**

     创建**接口实现类的代理对象**，通过代理对象增强类的方法（增加新的功能）

     ![image-20201031202508450](./images/image-20201031202508450.png)

   - **第二种情况：没有接口的情况 使用CGLIB动态代理**

     创建**子类的代理对象**，增强类的方法

     ![image-20201031203004643](./images/image-20201031203004643.png)

   - 

## AOP (JDK动态代理)

### JDK动态代理简介

**使用JDK动态代理，使用Proxy类里面的方法，创建代理对象**，调用`newProxyInstance`方法实现，三个参数：

- ClassLoader loader，类加载器，定义由哪个classloader对象生成的代理类进行加载
- Class<?> interfaces，一个interface对象数组，表示我们将给代理对象提供一组什么样的接口，如果提供了这样一个接口对象数组，那么也就声明了代理类实现了这些接口，代理类就可以调用接口中声明的所有方法。
- `InvovationHandler`，表示的是当动态代理对象调用方法的时候会关联到哪一个`InvocationHandler`对象上，并最终由其调用（实现这个接口`InvocationHandler`，创建代理的对象，写增强的方法）

![image-20201031205625511](./images/image-20201031205625511.png)

![image-20201031215611838](./images/image-20201031215611838.png)

### 编写JDK动态代理代码

1. **创建接口，定义方法**

   ```java
   public interface UserDao {
       public int add(int a,int b);
       public String update(String id);
   }
   ```

2. **创建接口的实现类，实现方法**

   ```java
   public class UserDaoImpl implements UserDao{
       @Override
       public int add(int a, int b) {
           return a+b;
       }
   
       @Override
       public String update(String id) {
           return id;
       }
   }
   ```

3. 使用Proxy类创建接口代理对象

   ```java
   import java.lang.reflect.InvocationHandler;
   import java.lang.reflect.Method;
   import java.lang.reflect.Proxy;
   import java.util.Arrays;
   
   public class JDKProxy {
       public static void main(String[] args) {
           //创建要实现方法增强的接口类
           Class[] interfaces={UserDao.class};
           UserDaoImpl userDaoImpl=new UserDaoImpl();
           //接口等于实现类的代理对象 Proxy.newProxyInstance创建接口实现类的代理对象
           //UserDaoProxy为代理对象，在其中调用原来的方法，同时进行了增强方法涉及实现
           UserDao dao =(UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(),interfaces,new UserDaoProxy(userDaoImpl));
           int result=dao.add(1,2);
           System.out.println(result);
           dao.update("1234556Strings");
       }
   }
   
   //创建代理对象代码 里面写一些增强逻辑
   class UserDaoProxy implements InvocationHandler {
       //1. 把被代理对象传递进来
       public Object obj;
   
       //通过有参构造进行传递
       public UserDaoProxy(Object obj){
           this.obj=obj;
       }
   
       //增强的逻辑
       //proxy：代理对象 
       //method：当前将要执行的目标对象的方法
       //args：方法调用时，外界传入的参数值 
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           //增强方法之前
           //method.getName() 获得执行方法的名字
           System.out.println("方法之前执行："+method.getName()+"传递的参数:"+ Arrays.toString(args));
   
           if(method.getName()=="add"){
               //执行类原有方法
               Object res=method.invoke(obj,args);
               //增强方法之后
               System.out.println("方法之后执行："+obj);
               return res;
           }else{
               //执行类原有方法
               Object res=method.invoke(obj,args);
               //方法执行
               System.out.println("方法执行之后："+obj);
               return null;
           }
       }
   }
   ```
   

# AOP（术语）

- **连接点**：类中哪些方法可以被增强，这些方法被称为连接点

- **切入点**：实际被真正被增强的方法，称为切入点 

- **通知（增强）**：实际增强的逻辑部分被称为通知（增强）

  **通知有多种类型（五种类型）：前置通知、后置通知、环绕通知、异常通知、最终通知**

- **切面**：是一个动作上的操作，把通知应用到切入点的过程

# AOP操作（准备工作）

**Spring框架中一般基于AspectJ实现AOP操作**

适应场景：

1. AOP做日志保存到数据库库
2. AOP做权限验证
3. AOP做安全检查
4. AOP做事务控制

## AspectJ

不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作

## 基于AspectJ实现AOP操作两种方式（XML、注解）

-  **基于XML配置文件实现**
-  **基于注解方式实现**

## 在项目工程里面引入AOP相关依赖

![image-20201031233728197](./images/image-20201031233728197.png)

## 切入点表达式

### 切入点表达式作用

设置对哪个类里面的那个方法进行增强

### 语法结构

```
execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))
同时支持 && || ！表达式
```

举例1：对`com.atguigu.dao.BookDao`类里面的add方法进行增强

```java
//* 表示任意修饰符，在路径中只表示匹配一层路径，..在路径中表达匹配多层路径
//..表示匹配方法中的任意多个参数和多种类型
execution(* com.atguigu.dao.BookDao.add(..))
```

举例2：对`com.atguigu.dao.BookDao`类里面的所有方法进行增强

```java
execution(* com.atguigu.dao.BookDao.*(..))
```

举例3：对`com.atguigu.dao`包里面所有类，所有类里的所有方法进行增强

```java
execution(* com.atguigu.dao.*.*(..))
```

## AOP操作（AspectJ注解）

### 创建类，在类里面定义方法（被增强的方法）

```java
public class User {
    //    前置通知
    public void add(){
        System.out.println("add ...");
    }
}
```

### 创建增强类（编写增强逻辑）

 在增强类里面，创建方法，让不同方法代表不同通知类型

```java
public class UserProxy {
    public void before(){
        System.out.println("before...");
    }
}
```

### 进行通知配置

- 步骤一：在Spring配置文件中，开启注解扫描

  ![image-20201101142301469](./images/image-20201101142301469.png)

- 步骤二：使用注解创建User和`UserProxy`对象

  ![image-20201101142356431](./images/image-20201101142356431.png)

  ![image-20201101142334607](./images/image-20201101142334607.png)

- 步骤三：在增强类上面添加注解`@Aspect`

  ![image-20201101142718156](./images/image-20201101142718156.png)

- 步骤四：在Spring配置文件中，开启生成代理对象

  ```xml
  <!--    开启AspectJ生成代理对象-->
  <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
  ```


### 配置不同类型的通知

在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置

`@Before @After @AfterReturning @AfterThrowing @Around`

`@Around`环绕通知中有一个参数为`ProcedingJoinPoint`类，通过其`proceed()`方法执行切入点方法 

环绕通知是优先于普通通知执行，执行顺序：

（环绕前置 --- 普通前置） --- 目标方法执行 --- 环绕正常返回/出现异常 --- 环绕后置 --- 普通后置 --- 普通返回或者异常

```
普通前置
{
	try{
		环绕前置
		环绕执行：目标方法执行
		环绕返回
	}catch{
		环绕出现异常
	}finally{
		环绕后置
	}
}

普通后置
普通方法返回/方法异常
```



```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

//增强类
@Component
@Aspect  //生成代理对象
public class UserProxy {
    //前置通知
    //@Before 注解表示作为前置通知  切入点表达式execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))
    @Before(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void before(){
        System.out.println("before...");
    }

    //最终通知
    //在方法执行之后执行 方法被调用的时候就会执行 不管调用之后方法有没出错
    @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void after(){
        System.out.println("after...");
    }

    
    //在返回结果（值）之后执行
    @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning...");
    }

    //异常通知
    @AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing...");
    }

    //在方法执行之前、之后都执行 环绕通知
    @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕之前...");
		
        //被增强方法执行
        Object process=proceedingJoinPoint.proceed();
        System.out.println("环绕之后");
        return process;
    }
}
```

### 相同的切入点抽取 可重用

<u>声明一个没有实现的返回void的空方法</u>

使用`@Pointcut(value="execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))")`

```java
@Aspect
@Component
public class UserProxy {
    //相同的切入点抽取
    @Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void pointdemo(){
    }

    //前置通知
    //@Before 注解表示作为前置通知  切入点表达式execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))
    @Before(value = "pointdemo()")
    public void before(){
        System.out.println("before...");
    }

    //最终通知
    //在方法执行之后执行 方法被调用的时候就会执行 不管调用之后方法有没出错
    @After(value = "pointdemo()")
    public void after(){
        System.out.println("after...");
    }
```

###  有多个增强类对同一个方法进行增强，设置增强类的优先级

在增强类上面增加注解`@Order(数字类型的值)`，数字类型的值越小优先级越高，0、1、2、......

```java
//增强类
@Component
@Aspect  //生成代理对象
@Order(3)  //代理对象优先级
public class UserProxy {
    //todo
}
```

### 获取目标方法的信息

为通知方法的参数列表上写一个参数：`JoinPoint joinPoint`封装了当前目标方法的详细信息

```java
//最终通知
//在方法执行之后执行 方法被调用的时候就会执行 不管调用之后方法有没出错
@After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
public void after(JoinPoint joinPoint){
    //获取目标方法运行时使用的参数
    Object[] args=joinPoint.getArgs();
    //获取方法签名
    Signature signature=joinPoint.getSignature();
    //获取方法名
    String name=signature.getName();
    System.out.println("after...");
}
```

### AfterThrowing AfterReturning 指定参数获取返回值

告诉Spring 使用哪个参数接受返回值

```java
//在返回结果（值）之后执行
@AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))",returning="result")
public void afterReturning(JoinPoint joinPoint,Object result){
    System.out.println("afterReturning...");
}

//异常通知
@AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))",throwing="result")
public void afterThrowing(JoinPoint joinPoint,Object result){
    System.out.println("afterThrowing...");
}
```



### 完全使用注解开发

创建配置类，不需要配置XML配置文件

```java
@Configuration     //配置类
@ComponentScan(basePackages = {"com.atguigu"})    //开启组件扫描
@EnableAspectJAutoProxy(proxyTargetClass = true)  //开启AspectJ 代理对象
public class ConfigAop {
}
```

##  AOP操作（AspectJ配置文件 XML方式）

### 创建两个类，增强类和被增强类，创建方法

```java
public class Book {
    public void buy(){
        System.out.println("buy...");
    }
}
```

```java
public class BookProxy {
    public void before(){
        System.out.println("before....");
    }
}
```

### 在Spring配置文件中创建两个类对象

```xml
<!--创建两个类的对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
```

### 在Spring配置文件中配置切入点

```xml
<!--配置aop的增强-->
<aop:config>
	<!-- 步骤一：配置切入点 aop:pointcut
		id：切入点名称
		expression：切入点表达式 -->
	<aop:pointcut id="p" expression="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"/>

	<!--  步骤二：配置切面  aop:aspect
		ref：指定增强类 -->
	<aop:aspect ref="bookProxy">
	<!-- 增强作用在具体的方法上
		有aop:before aop:after aop:after-returning aop:after-throwing aop:around
		method:作用在哪个方法上，方法名
		pointcut-ref:作用在哪一个切入点上 -->
        
		<aop:before method="before" pointcut-ref="p"></aop:before>
         <!-- 二者效果相同 -->
         <aop:before method="before" pointcut="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"></aop:before> 
        
	</aop:aspect>
</aop:config>
```

# JdbcTemplate

# JDBCTemplate说明

Spring框架对JDBC进行封装，使用`JDBCTemplate`方便实现对数据库操作

# 准备工作

## 引入相关jar包

![image-20201103114921764](./images/image-20201103114921764.png)

## 在Spring配置文件中配置数据库的连接池

```xml
<!--数据库连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
    <property name="url" value="jdbc:mysql://localhost:3306/user_db?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
    <property name="username" value="root2"/>
    <property name="password" value="root"/>
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
</bean>
```

## 配置jdbcTemplate对象，注入DataSource

`jdbcTemplate`操作数据库`dataSource`对象

```xml
<!--    创建JDBCTemplate 对象-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	<!--  注入对象dataSource set方法注入-->
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

## 创建Service类，创建dao类，注入

在Service中注入dao，在dao中注入jdbcTemplate

![image-20201103153231253](./images/image-20201103153231253.png)

- 配置文件中

  ```xml
  <!--  组件扫描  -->
  <context:component-scan base-package="com.atguigu"></context:component-scan>
  ```

- Service 

  ```java
  @Service
  public class BookService {
      //注入dao
      @Autowired
      private BookDao bookDao;
  }
  ```

- Dao

  ```java
  @Repository
  public class BookDaoImpl implements BookDao{
  //    注入JdbcTemplate
      @Autowired
      private JdbcTemplate jdbcTemplate;
  }
  ```

# jdbcTemplate操作数据库（添加操作）

![image-20201103155319942](./images/image-20201103155319942.png)



## 对应数据库创建实体类

创建的类属性与数据库中的表字段相对应，并生成set和get方法，创建Book类（创建数据库表对应的类）

![image-20201103155154870](./images/image-20201103155154870.png)

```java
package com.atguigu.spring5.entity;
public class Book {
    private String userId;
    private String username;
    private String ustatus;

    public String getUserId() {
        return userId;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getUstatus() {
        return ustatus;
    }

    public void setUstatus(String ustatus) {
        this.ustatus = ustatus;
    }
}
```

## 编写service和dao

### 在dao进行数据库添加操作

```java
@Service
public class BookService {
    //注入dao
    @Autowired
    private BookDao bookDao;

    //添加的方法
    public void addBook(Book book){
        bookDao.add(book);
    }
}
```

```java
@Repository
public class BookDaoImpl implements BookDao{
	//注入JdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate;

    //添加的方法
    @Override
    public void add(Book book) {
        //1.SQL语句
        String sql="insert into t_book values(?,?,?)";
        //2.调用方法实现
        Object[] args={book.getUserId(),book.getUsername(),book.getUstatus()};
        //int update=jdbcTemplate.update(sql,book.getUserId(),book.getUsername(),book.getUstatus());
        int update=jdbcTemplate.update(sql,args);
        System.out.println("update数据库影响行数："+update);
    }
}
```

## 调用jdbcTemplate对象里面update方法实现添加操作

- 有两个参数
  - 参数一：SQL语句
  - 参数二：可变参数，设置SQL语句中的值

![image-20201103155726030](./images/image-20201103155726030.png)

## 测试类

```java
   @Test
    public void testJdbcTemplate(){
        ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
        BookService bookService=context.getBean("bookService",BookService.class);

        Book book=new Book();
        book.setUserId("1");
        book.setUsername("Java");
        book.setUstatus("A");

        bookService.addBook(book);
    }
```

<img src="./images/image-20201103172526704.png" alt="image-20201103172526704" style="zoom:80%;" />

![image-20201103172616539](./images/image-20201103172616539.png)

#  jdbcTemplate 操作数据库（修改、删除）

## 修改操作

```java
    @Override
    public void updateBook(Book book) {
        String sql="update t_book set username=?,ustatus=? where user_id=?";
        Object[] args={book.getUsername(),book.getUstatus(),book.getUserId()};
        int update=jdbcTemplate.update(sql,args);
        System.out.println("update数据库影响的行数："+update);
    }
```

## 删除操作

```java
    @Override
    public void delete(String id) {
        String sql="delete from t_book where user_id=?";
        int update=jdbcTemplate.update(sql,id);
        System.out.println("update数据库影响的行数："+update);
    }
```

# jdbcTemplate数据库（查询返回某个值）

## 用JdbcTemplate实现查询返回某个值代码

![image-20201103190247249](./images/image-20201103190247249.png)

有两个参数：

- 第一个参数SQL语句
- 第二个参数返回类型的class

```java
    @Override
    public int selectCount() {
        String sql="select count(*) from t_book";
        Integer count=jdbcTemplate.queryForObject(sql,Integer.class);
        return count;
    }
```

# jdbcTemplate 数据库（查询返回对象）

## jdbcTemplate实现查询返回对象

![image-20201103192335717](./images/image-20201103192335717.png)

有三个参数：

- 参数一：SQL语句
- 参数二：`RowMapper`是接口，针对返回的不同类型数据，利用这个接口的实现类完成数据的封装
- 参数三：SQL语句值

```java
    @Override
    public Book findBookInfo(String id) {
        String sql="select * from t_book where user_id=?";
        Book book=jdbcTemplate.queryForObject(sql,new BeanPropertyRowMapper<Book>(Book.class),id);
        return book;
    }
```

# jdbcTemplate操作数据库（查询返回集合）

## jdbcTemplate方法实现查询返回集合

![image-20201103193239023](./images/image-20201103193239023.png)

```java
    @Override
    public List<Book> findAllBook() {
        String sql="select * from t_book";
        List<Book> bookList =jdbcTemplate.query(sql,new BeanPropertyRowMapper<Book>(Book.class));
        return bookList;
    }
```

# jdbcTemplate操作数据库（批量操作）

操作表里面的多条记录

## JDBCTemplate实现批量添加操作

![image-20201103193951960](./images/image-20201103193951960.png)

两个参数：

- 第一个参数：SQL查询语句
- 第二个参数：ListJ集合，添加的多条记录

```java
    @Override
    public void batchAddBook(List<Object[]> batchArgs) {
        String sql="insert into t_book values(?,?,?)";
        int[] ints=jdbcTemplate.batchUpdate(sql,batchArgs);
        System.out.println(Arrays.toString(ints));
    }
```

测试类

```java
    @Test
    public void testJdbcTemplatebatchAdd(){
        ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
        BookService bookService=context.getBean("bookService",BookService.class);
        List<Object[]> batchArgs=new ArrayList<>();
        Object[] o1={"3","CC","C"};
        Object[] o2={"4","C++","D"};
        Object[] o3={"5","COMPILE","CM"};
        batchArgs.add(o1);
        batchArgs.add(o2);
        batchArgs.add(o3);
        bookService.batchAdd(batchArgs);
    }
```

![image-20201103202052681](./images/image-20201103202052681.png)

## jdbcTemplate 操作数据（批量修改 删除）

### 批量修改

```java
@Override
public void batchUpdate(List<Object[]> batchArgs) {
	String sql="update t_book set username=?,ustatus=? where user_id=?";
    int[] ints=jdbcTemplate.batchUpdate(sql,batchArgs);
    System.out.println(Arrays.toString(ints));
}
```

```java
//测试类
@Test
public void testJdbcTemplatebatchUpdate(){
    ApplicationContext context=new ClassPathXmlApplicationContext("bean1.xml");
    BookService bookService=context.getBean("bookService",BookService.class);
    List<Object[]> batchArgs=new ArrayList<>();
    Object[] o1={"CC Update","C","3"};
    Object[] o2={"C++ Update","D","4"};
    Object[] o3={"COMPILE Update","CM","5"};
    batchArgs.add(o1);
    batchArgs.add(o2);
    batchArgs.add(o3);
    bookService.batchUpdate(batchArgs);
}
```

### 批量删除

```java
@Override
public void batchDelete(List<Object[]> batchArgs) {
    String sql="delete from t_book where user_id=?";
    int[] update=jdbcTemplate.batchUpdate(sql,batchArgs);
    System.out.println(Arrays.toString(update));
}
```

# 事务管理

# 事务概念

- 事务是数据库操作最基本的单元，逻辑上的一组操作，要么都成功，如果有一个失败所有操作都失败
- 典型场景：银行转账的场景

# 事务特性 ACID

原子性、一致性、隔离性、持久性

# 事务操作（搭建事务操作环境）

**银行转账服务示例**

WEB层（视图层）只展示相关的内容，Service 只与业务逻辑相关，Dao数据库操作，与数据相关与实际业务无关

![image-20201104180640611](./images/image-20201104180640611.png)

## 创建数据库表，添加记录

![image-20201104181146611](./images/image-20201104181146611.png)

## 创建service，搭建dao，对象创建和注入关系

service注入dao，在dao注入jdbcTemplate，在jdbcTemplate中注入DataSource

```xml
<!--  组件扫描  -->
<context:component-scan base-package="com.atguigu"></context:component-scan>
<!--数据库连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" destroy-method="close">
    <property name="url" value="jdbc:mysql://localhost:3306/user_db?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"/>
    <property name="username" value="root2"/>
    <property name="password" value="root"/>
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
</bean>

<!--    创建JDBCTemplate 对象-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
<!--  注入对象dataSource set方法注入-->
    <property name="dataSource" ref="dataSource"></property>
</bean>
```

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao;
}
```

```java
public interface UserDao {
}
```

```java
@Repository
public class UserDaoImpl implements UserDao{
    @Autowired
    private JdbcTemplate jdbcTemplate;
}
```

## 在dao创建两个方法：多钱和少钱的方法，在service创建转账方法

```java
public interface UserDao {
    public void addMoney();
    public void reduceMoney();
}
```

```java
@Repository
public class UserDaoImpl implements UserDao{
    @Override
    public void addMoney() {
        String sql="update t_count set money=money+? where username=?";
        jdbcTemplate.update(sql,100,"mary");
    }

    //lucy转账100
    @Override
    public void reduceMoney() {
        String sql="update t_account set money=money-? where username=?";
        jdbcTemplate.update(sql,100,"lucy");
    }

    @Autowired
    private JdbcTemplate jdbcTemplate;
}
```

```java
@Service
public class UserService {
    @Autowired
    private UserDao userDao;

    //转账方法
    public void accountMoney(){
        // 代码可能产生问题  使用事务进行解决
        //lucy少100
        userDao.reduceMoney();
        //mary多100
        userDao.addMoney();
    }
}
```

# 事务操作基本过程

1. 开启事务操作
2. 进行业务上的操作
3. 没有发生异常，提交事务
4. 出现异常，事务回滚

```java
public void accountMoney(){
    try{
        //第一步 开启事务
        //第二步 进行业务操作
        //lucy 少100
        userDao.reduceMoney();
        
        //模拟异常
        int i=10/0;
        
        //mary多100
        userDao.addMoney();
        
        //第三步 没有发生异常提交事务
    }catch(Exception e){
        //第四步 出现异常，事务回滚
    }
}
```

# 事务操作（Spring事务管理介绍）

1. 事务添加到JavaEE三层结构里面的Service层（业务逻辑层）

2. 在Spring进行事务管理操作：

   **有两种方式：编程式事务管理和声明式事务管理**（更多使用）

3. 声明式事务管理

   基于注解方法和基于XML配置文件方法

4. 在Spring进行声明式事务管理，底层使用AOP原理

5. Spring事务管理API

   提供了一个接口`PlatformTransactionManager`，代表事务管理器，这个接口针对不同的架构提供不同的实现类。例如：针对JDBC模板的实现类`DataSourceTransactionManager`

   ![image-20201104230002978](images/image-20201104230002978.png)

   ![image-20201104230159026](images/image-20201104230159026.png)

# 事务操作（注解声明式事务管理）

## 在Spring配置文件中配置事务管理器

```xml
<!--    创建事务管理器 创建PlatformTransactionManger 接口某个实现类的实例-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<!-- 注入数据源       -->
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

## 在spring配置文件，开启事务注解

1. 在spring配置文件中引入**名称空间tx**

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                              http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                              http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                              http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
   ">
   ```

2. 开启事务注解

   ```xml
   <!--    开启事务注解 
   	transaction-manager指定事务注解-->
   <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
   ```

3. 在service类上面（获取service类里面方法上面）添加事务注解

   - `@Transactional`可以添加到类上面，也可以添加方法上面

   - 注解添加到类上面，这个类里面的所有方法都添加事务

   - 注解添加到类的方法上面，为这个方法添加事务

     ```java
     @Service
     @Transactional
     public class UserService {
         @Autowired
         private UserDao userDao;
     
         //转账方法
         public void accountMoney(){
             //lucy少100
             userDao.reduceMoney();
             int i=5/0;
             //mary多100
             userDao.addMoney();
         }
     }
     ```

# 事务操作（声明式事务管理参数配置）

在service类上面添加注解@Transactional，在这个注解里面可以配置针对事务相关参数

![image-20201119152525516](./images/image-20201119152525516.png)

1. **propagation：事务的传播行为**

   事务传播行为：多事务方法直接进行调用，这个过程中事务是如何进行管理的

   事务方法：对数据库表数据进行变化的操作

   ![image-20201119154213050](./images/image-20201119154213050.png)

   ![image-20201119154131843](./images/image-20201119154131843.png)

   **required**：如果add方法本身有事务，调用update方法之后，update使用当前add里面的事务；如果add方法方法本身没有事务，调用update方法之后，创建新事务

   **require_new**：使用add方法调用update方法，无论add是否有事务，都会创建新事务进行操作。

   ```java
   @Service
   @Transactional(propagation = Propagation.REQUIRED)
   public class UserService {
       @Autowired
       private UserDao userDao;
   
       //转账方法
       public void accountMoney(){
           //lucy少100
           userDao.reduceMoney();
           int i=5/0;
           //mary多100
           userDao.addMoney();
       }
   }
   ```

2. **isolation：事务隔离级别**

   通过设置事务隔离性，解决读的三个问题（脏读、不可重复读、虚读（幻读））。

   ![image-20201119162114046](./images/image-20201119162114046.png)

   ```java
   @Transactional(propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)
   public class UserService {
       //
}
   ```
   
   补充：事务的三个问题

- 事务的特性，隔离性。多事务操作之间不会产生影响。

  有三个读问题：脏读、不可重复读、虚读（幻读）

  脏读：一个未提交事务读取到另一个未提交事务的事务     （一种问题）

  ![image-20201119160457592](./images/image-20201119160457592.png)

  不可重复读：一个未提交事务读取到另一提交事务修改的数据   （一种现象）

  ![image-20201119160945970](./images/image-20201119160945970.png)

  虚读（幻读）：一个未提交事务读取到另一提交事务添加数据

3. **timeout：超时时间** 

   事务需要在一定的时间内进行提交，如果不提交，就会回滚

   默认值是-1，设置时间以秒为单位进行计算

   ```java
   @Transactional(timeout = -1,propagation = Propagation.REQUIRED,isolation = Isolation.REPEATABLE_READ)
   public class UserService {
   ```

   

4. **readOnly：是否只读**

   - 读：查询操作，写：添加修改删除操作
   - readOnly 默认false，表示可以查询，可以添加删除操作
   - readOnly值为true，设置为true，表示只可以查询

   

5. **rollbackFor：回滚**

   设置查询哪些异常进行事务回滚

   

6. **noRollbackFor：不回滚**

   设置出现哪些异常，不进行回滚

# 事务操作（XML声明式事务管理）

1. **在Spring配置文件中进行配置**

   **第一步：配置事务管理器**

   ```xml
   <!--    创建事务管理器 创建PlatformTransactionManger 接口某个实现类的实例-->
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <!-- 注入数据源       -->
           <property name="dataSource" ref="dataSource"></property>
       </bean>
   ```

   **第二步：配置通知**

   ```xml
       <tx:advice id="txadvice">
           <!--   配置事务相关参数     -->
           <tx:attributes>
               <!--   指定哪种规则的方法上面添加事务    -->
               <!-- account*  在account开头的方法-->
               <tx:method name="account*" isolation="READ_COMMITTED" propagation="REQUIRED"/>
           </tx:attributes>
       </tx:advice>
   ```

   

   **第三步：配置切入点，切面**

   ```xml
   <!--    配置切入点和切面-->
       <aop:config>
   <!--        配置切入点-->
           <aop:pointcut id="pt" expression="execution(* com.atguigu.spring5.service.UserService.*(..))"/>
   <!--        配置切面-->
           <aop:advisor advice-ref="txadvice" pointcut-ref="pt"></aop:advisor>
       </aop:config>
   ```

# 事务操作（完全注解声明式事务管理）

## 创建配置类

```java
@Configuration //配置类
@ComponentScan(basePackages = "com.atguigu.spring5")  //开启组件扫描
@EnableTransactionManagement //开启事务
public class TxConfig {
//    创建数据库的链接池
    @Bean  //创建对象实例
    public DruidDataSource getDruidDataSource(){
        DruidDataSource dataSource=new DruidDataSource();
        dataSource.setDriverClassName("com.alibaba.druid.pool.DruidDataSource");
        dataSource.setUrl("jdbc:mysql://localhost:3306/user_db?useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC");
        dataSource.setName("root2");
        dataSource.setPassword("root");
        return dataSource;
    }

    //jdbcTemplate 模板对象
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource){
        //到ioc容器中根据类型找到dataSource对象
        JdbcTemplate jdbcTemplate=new JdbcTemplate();
        //注入DataSource
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    //创建事务管理器的对象
    @Bean
    public DataSourceTransactionManager getDataSourceTrasactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager=new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```

![image-20201119175516422](./images/image-20201119175516422.png)

```java
    @Test
    public void testAccount3(){
        ApplicationContext context=new AnnotationConfigApplicationContext(TxConfig.class);
        UserService userService=context.getBean("userService", UserService.class);
        userService.accountMoney();
    }
```



