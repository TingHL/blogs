# 监听器简介和分类

## 监听器

监听对象：

监听事件

触发行为

## JavaWeb监听器 Listener

​	和`serverlet`，`filter`，`listener`：`JavaWeb`的三大组件

### **八个监听器**

#### 按照监听对象

分为三类：

1. 监听`ServletContext`事件

   ​	`ServletContextListener`：

   ​			作用：监听`ServletContext`生命周期（从创建到销毁的过程（服务器启动创建--服务器停止销毁））

   ​	`ServletContextAttributeListener`：

   ​			作用：监听`ServletContext`域中属性变化的；

2. 监听`HttpSession`事件

   ​	`HttpSessionListener`：

   ​			作用：监听`HttpSession`的生命周期（session第一次使用的时候创建；session超时销毁或session手动设置失效（`session.invalidate`方法））

   ​	`HttpSessionAttributeListener`：

   ​			作用：监听`HttpSession`域中属性的变化（增删改）

   ​	`HttpSessionActivitionListener`：

   ​			作用：监听某个对象随着`HttpSession`活化钝化的过程

   ​	`HttpSessionBindlingListener`：

   ​			作用：监听某个对象保存（绑定）到session中和从session中移除（解绑）

3. 监听`ServletRequest`事件

   ​	`ServletRequestListener`：

   ​			作用：监听request对象的生命周期（请求进来创建新的request，保存请求的详细信息，请求完成销毁request对象）

   ​	`ServletRequestAttributeListener`：

   ​			作用：监听request域中属性变化

#### 按照功能

1. 一类是：生命周期监听器

   **`javax.servlet.ServletContextListener  ServletContext监听器`**

   |        |                                                              |
   | ------ | ------------------------------------------------------------ |
   | `void` | `contextDestroyed(ServletContextEvent sce)` <br> 销毁（服务器停止）调用<br>Receives notification that the ServletContext is about to be shut down. |
   | `void` | `contextInitialized(ServletContextEvent sce)`<br>初始化（服务器启动）调用<br>Receives notification that the web application initialization process is starting. |

   **`javax.servlet.http.HttpSessionListener HttpSession监听器`**

   |        |                                                              |
   | ------ | ------------------------------------------------------------ |
   | `void` | `sessionCreated(HttpSessionEvent se)`<br>session创建（第一次使用session创建）<br>Receives notification that a session has been created. |
   | `void` | `sessionDestroyed(HttpSessionEvent se)`<br>session自动失效（超时timeout）、手动设置session失效（session.invalidate）<br>Receives notification that a session is about to be invalidated. |

   **`javax.servlet.ServletRquestListener ServletRequest监听器`**

   |        |                                                              |
   | ------ | ------------------------------------------------------------ |
   | `void` | `requestDestroyed(ServletRequestEvent sre)`<br> request销毁调用（当次请求结束）<br>Receives notification that a ServletRequest is about to go out of scope of the web application. |
   | `void` | `requestInitialized(ServletRequestEvent sre)`<br>request初始化（发起一个新请求）<br>Receives notification that a ServletRequest is about to come into scope of the web application. |

   

2. 一类是：属性监听器 监听域中属性变化

   **`javax.servlet.ServletContextAttributeListener ServletContext属性监听器`**

   

   **`javax.servlet.http.HttpSessionAttributeListener HttpSession属性监听器`**

   

   **`javax.servlet.ServletRequestAttributeListener Request属性监听器`**

   

3. 一类是：对象监听器（session独有的监听器）

   **`javax.servlet.http.HttpSessionActivationListener 保存到session域中的对象，活化和钝化的监听器`**

   
   
   **`javax.servlet.http.HttpSessionBindingListener 对象被绑定到session域中的监听器`**
   
   

生命周期监听器和属性监听器，一定要在`web.xml`文件中配置之后才会生效。

```xml
<listener>
    <listener-class>全类名</listener-class>
</listener>
```

#### **补充**

1. JSP四大作用域（范围从小到大）：`PageContext域 --> Session域 --> Request域 --> Application域`

   **生命周期：**

   page：存在page中的变量，只作用于当前的jsp页面，当发生跳转、重定向、定时刷新时，将随之销毁；

   request：存在request中的变量，作用于一次HTTP请求到服务器处理结束，返回响应的整个过程，该变量可以随着forward的方式跳转到多个jsp中，一但刷新页面，它们将重新计算；

   session：存在Session中的变量，作用于一次会话中，从打开浏览器到关闭浏览器过程中，将一直累加；（若想在再次打开浏览器时，变量仍然存在，则可以将session的JSESSIONID存到Cookie中，在给cookie一个存活时间）

   application：存在application中的变量，作用于整个应用中，即从应用启动到应用结束，如果不进行手工删除，它们将一直可以使用，而且这些变量所有用户均可使用。

   **作用范围：**

   page：用户请求的当前页面；

   request：用户请求访问的当前组件，以及和当前web组件共享同一用户请求的web组件；

   session：同一个Http会话中的web组件共享；

   application：整个web应用的所有web组件共享，即只要是同一个服务器下的均可使用。

   

2. `ServletContext  HttpSession HttpServletRequest`

   “会话"概念：浏览器开启-->关闭

   `ServletContext`：即`application`，置入其中的域属性是整个应用范围的，可以完成跨会话共享数据

   `HttpSession`：置入其中的域属性是会话范围的，可以完成跨请求共享数据

   `HttpServletRequest`：置入其中的域属性是请求范围的，可以跨Servlet共享数据，但是这些Servlet必须在同一个请求中

3. 





